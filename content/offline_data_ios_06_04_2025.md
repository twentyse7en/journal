# Managing offline data in IOS

# TL;DR
Building an iOS affirmation app with mostly static content—no server needed. Initially thought of using JSON, but it didn’t scale well. Explored SwiftData, which is nice but has limitations with prefilling and migrations. Considered splitting static and user data, but still hit roadblocks. Now looking into GRDB to manage a read-only SQLite DB for static content, and keeping user-generated data separate. Still learning, figuring things out as I go. Native iOS dev feels a bit lonely, but making progress.

# Intro
I'm building an iOS app—an Affirmation App, to be precise. Most of the data is static, so I don't need a server. The data can live on the user's phone, and if I ever need to make changes, I'll just push an app update. At first, I thought of using a simple JSON file, but that felt too basic. We have a lot of data, and I don't want to load everything into memory at once. Then I discovered SwiftData and other SQLite wrappers.

# SwiftData
For the last 2–3 weeks (on weekends), I’ve been figuring out how to manage offline data in an iOS app. iOS has introduced SwiftData, which is a wrapper over SQLite. It had a learning curve, most of the time you are alone, feels like not much people interested in ios development. StackOverflow and Reddit questions often have no answers—feels like a ghost town.It made me question my choice of framework—was React Native a better option? But the whole point of this project was to actually finish something and learn native development. If we consider from community support, Youtube tutorial it's quite lot less popular than React Native. But at the end of the tunnel there was light, I was able figure out stuffs. 

With SwiftData, you can’t run raw queries directly—you have to go through their abstraction layer. What you miss out on: I have to prefill the DB manually, and I also need to handle migrations on updates. Decision should be based on the volume of data, If you have to prefill the 1000 rows It will take around 1 second. My idea was to delete the rows every time app update and fill the new rows again. But when my app grows, this method doesn't make sense for me.

User-Created Content: users can create their own affirmations and categories, which should stay separate from the static content. They can also mark affirmations as favorites, so my plan is to make this data independent from the static content. SwiftData is great, but I couldn’t find a clean solution for prefilling. I don’t want to delete and repopulate the entire DB on every update.

# Raw Database
I can create a read-only SQLite database so I can just replace it with every update. [GRDB](https://github.com/groue/GRDB.swift) is one of the SQLite wrappers that supports this. With a GRDB setup, I can replace the SQLite file itself, while user-generated content can live in a separate DB—either using GRDB or SwiftData.

# Useful Resources
- SwiftData Tutorials: [
Stewart Lynch](https://www.youtube.com/watch?v=CAr_1kcf2_c&list=PLBn01m5Vbs4Ck-JEF2nkcFTF_2rhGBMKX) | [tundsdev](https://www.youtube.com/watch?v=kLNNNXD8X2U&list=PLvUWi5tdh92wZ5_iDMcBpenwTgFNan9T7&index=1)

# GRDB Sample Code
It's easy to understand.

```swift
import SwiftUI
import GRDB

// MARK: - Models

struct Quote: Codable, FetchableRecord {
    var id: Int
    var text: String
    var author: String
    var year: Int?
}

struct QuoteCategory: Codable, FetchableRecord {
    var id: Int
    var name: String
}

// MARK: - Database manager

class DatabaseManager {
    static let shared = DatabaseManager()
    
    var dbQueue: DatabaseQueue!
    
    private init() {
        setupDatabase()
    }
    
    // not sure if we need this copy paste
    func setupDatabase() {
        let fileManager = FileManager.default
        let dbName = "quotes.db"
        
        let docsURL = try! fileManager.url(for: .documentDirectory, in: .userDomainMask, appropriateFor: nil, create: true)
        let destURL = docsURL.appendingPathComponent(dbName)
        
        if !fileManager.fileExists(atPath: destURL.path) {
            guard let bundleDB = Bundle.main.url(forResource: "quotes", withExtension: "db") else {
                fatalError("Quotes.sqlite not found in bundle!")
            }
            try! fileManager.copyItem(at: bundleDB, to: destURL)
        }
        
        dbQueue = try! DatabaseQueue(path: destURL.path)
    }
    
    func fetchAllCategories() throws -> [QuoteCategory] {
        try dbQueue.read { db in
            try QuoteCategory.fetchAll(db, sql: "SELECT * FROM categories ORDER BY name")
        }
    }
    
    func fetchQuotes(forCategoryId id: Int) throws -> [Quote] {
        try dbQueue.read { db in
            try Quote.fetchAll(db,
                sql: """
                SELECT q.*
                FROM quotes q
                JOIN quote_categories qc ON q.id = qc.quote_id
                WHERE qc.category_id = ?
                """,
                arguments: [id]
            )
        }
    }
    
    func fetchRandomQuote() throws -> Quote? {
        try dbQueue.read { db in
            try Quote.fetchOne(db, sql: "SELECT * FROM quotes ORDER BY RANDOM() LIMIT 1")
        }
    }
}

// MARK: - ViewModels

class CategoriesVM: ObservableObject {
    @Published var categories: [QuoteCategory] = []
    
    init() {
        loadCategories()
    }
    
    func loadCategories() {
        do {
            self.categories = try DatabaseManager.shared.fetchAllCategories()
        } catch {
            print("Failed to load categories: \(error)")
        }
    }
}

class QuotesVM: ObservableObject {
    @Published var quotes: [Quote] = []
    
    func loadQuotes(forCategoryId id: Int) {
        do {
            self.quotes = try DatabaseManager.shared.fetchQuotes(forCategoryId: id)
        } catch {
            print("Failed to load quotes: \(error)")
        }
    }
}

// MARK: - Views

struct CategoriesView: View {
    @StateObject var viewModel = CategoriesVM()
    
    var body: some View {
        NavigationView {
            List(viewModel.categories, id: \.id) { category in
                NavigationLink(destination: QuotesListView(category: category)) {
                    Text(category.name)
                }
            }
            .navigationTitle("Quote Categories")
        }
    }
}

struct QuotesListView: View {
    let category: QuoteCategory
    @StateObject private var viewModel = QuotesVM()
    
    var body: some View {
        List(viewModel.quotes, id: \.id) { quote in
            VStack(alignment: .leading, spacing: 6) {
                Text("“" + quote.text + "”")
                    .font(.body)
                Text("- \(quote.author)\(quote.year != nil ? " (\(quote.year!))" : "")")
                    .font(.caption)
                    .foregroundColor(.gray)
            }
            .padding(.vertical, 6)
        }
        .navigationTitle(category.name)
        .onAppear {
            viewModel.loadQuotes(forCategoryId: category.id)
        }
    }
}

#Preview {
    CategoriesView()
}
```