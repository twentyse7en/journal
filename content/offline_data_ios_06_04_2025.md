# Managing offline data in IOS

# Intro
I'm trying to make an App for iOS. Affirmation App precisely. Most of my data is static, I don't need a server, data can reside on users phone and If I need a change I will update the App. I first thought to use a simple JSON, but that might be too simple, we have a lot of data, we don't need open everything to memory at once. Then I found about `SwiftData` and other sqlite wrapper.

# SwiftData
For last 2-3 weeks I was figuring out how to manage offline data in ios app. iOS have introduces `SwiftData` which is wrapper over `sqlite`. It had a learning curve, most of the time you are alone, feels like not much people interested in ios development. StackOverflows and reddit questions with no answers, feel like abondoned place. That questions my choice of choosing framework, was react native a better choice? Whole point of the project was to compelte a project and understand the native development part. If we consider from community support, Youtube tutorial it's quite lot less popular than React Native. But at the end of the tunnel there was light, I was able figure out stuffs. 

With `SwfitData` you can't run the query directly, you have to go through their abstraction layer. What do you miss out, I have to prefill the db, also on update I have to migrate the changes. Decision should be based on the volume of data, If you have to prefill the 1000 rows It will take around 1 second, my idea was to delete the db every time when app update and fill the database again. But my app the contents will grow, this method doesn't make sense for me.

**User Created Content**: user can create their own affirmation and categories, which is independent of the static content, and mark affirmation as favouite, so my plan is to make this data independent from the static content. Swift data is great, but I couldn't find a way to solve the problem with prefilling, I don't need delete and populate my database on every update.

# Raw Database
I can create a read only sqlite database system, so I can replace that on every update. [GRDB](https://github.com/groue/GRDB.swift) is one of the sqlite wrapper for this. With *GRDB* setup, I can replace the sqlite itself and user generate content can stay in a different db either with *GRDB* or *SwiftData*.

# GRDB Setup
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