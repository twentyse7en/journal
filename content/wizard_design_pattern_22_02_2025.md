const doc = `
# Invoice Upload Flow – State Management Decision

## Use Case

Multi-step invoice upload flow:

1. Step 1 – Upload screen  
   User uploads invoice data (files, parsed data, preview info)

2. Step 2 – Submit screen  
   Data passed via navigation params  
   Preview displayed, user confirms

3. Step 3 – Preview / Final review  
   Same uploaded data must be shown again

---

## Approach 1: Passing Data via Navigation Params

### How it works
- Step 1 passes full payload using navigation params
- Step 2 receives and forwards it
- Step 3 receives it again

### Drawbacks

- ❌ Large payloads (files, base64, parsed JSON) increase memory usage
- ❌ Data duplication across screens
- ❌ Hard to maintain when flow grows (Step 4, Step 5...)
- ❌ Editing data in Step 2 requires re-passing updated params
- ❌ Poor scalability
- ❌ Fragile if navigation structure changes
- ❌ No recovery if app reloads

👉 Works only for small, simple payloads.

---

## Recommended: Wizard State Pattern (Single Source of Truth)

### How it works

- Step 1 creates a draft (e.g., invoiceDraft)
- Draft stored in centralized or flow-scoped state (Context / Zustand / Redux)
- Navigation passes only: { uploadId }
- Step 2 & Step 3 read from the same source of truth

---

## Advantages Over Params Approach

- ✅ Single source of truth
- ✅ No large objects in navigation
- ✅ Cleaner architecture
- ✅ Easy to extend (Step 4+)
- ✅ Easier edits and updates
- ✅ More predictable state flow
- ✅ Can be extended with persistence if needed

---

## Decision Rule

- Small payload → Navigation params acceptable
- Files / complex objects / multi-step edits → Use Wizard State Pattern

## Acknowledgement
- This is my summary of problem discussed with chatgpt 5.2.
