# Weekend Experiment Logs - 1
Just some notes of things I found interesting on weekends.

## Going through bluesky repo
- [link](https://github.com/bluesky-social/social-app)
- they are using [pnpm](https://pnpm.io/)
    - I have never use pnpm, seems like's it's cooler than yarn. Better speed and disk space management.
- App is React Native, Typescript
- They are using expo
- They are using StyleSheet, sometimes inline styles.
- Not that interesting 🥱, stopping now. May be I will comeback if I have any specific requirement.

## Trying out shdcn/ui
- [link](https://ui.shadcn.com/)
- It's not component library, no npm package, just copy paste things and customize as you need.
- shdcn/ui is using tailwind for styling
- I did checkout `Button`, this repo is just giving you a component to copy paste as advertised. If you have a design system you can adjust accordingly, may be some styles you might not need. Is this even helpful for big projects?
- I can copy paste from shdcn/ui, then remove stuff and add my things, use shdcn/ui for their setup alone💭
- In reddit people are saying this is just a lot of hype, shdcn just a tailwind wrapper around Radix-ui

## Tic Tac Toe Game Algorithm
- If you are making a tic tac toe game, what algorithm will you use, is there any specific one?   
- when I first thought about this, I was thinking about `backtracking`, find all possible movements and choose what is best for the computer. But I was stuck what should I do next?
- I found this [video](https://www.youtube.com/watch?v=trKjYdBASyQ), there is algorithm for this specific scenario.
- What we need to consider during backtracking is, this is a turn based game. When you are making a best move for you, on next turn your opponent will make best turn for himself. Name of algorithm is [minmax](https://www.geeksforgeeks.org/minimax-algorithm-in-game-theory-set-1-introduction/)