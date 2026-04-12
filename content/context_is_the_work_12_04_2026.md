# Context is the work
- summary of Sunil pai's [blog](https://sunilpai.dev/posts/context-is-the-work/)
- Not entire summary but some parts of blog I might need to remember


## Code vs Review
> code got cheaper. Review often feels harder.

* This might not be self review, but normal pr review by team members. 

## PR description starts carrying the engineering

The pr description is supposed to answer:
* what did we mean?
* what constraints shaped this?
* how do we know it’s correct?
* what should reviewers focus on?

## Like in a interview
think about coding interviews; the ones where the **prompt** is intentionally **under-specified**.

yes, you’re “solving a problem.” but the real signal isn’t whether you can type out a solution under time pressure. the real signal is usually:

* what clarifying questions you ask
* what constraints you surface
* what assumptions you state explicitly
* how you test the edges mentally before writing code

## “knowing what questions to ask” becomes the job
> "turn ambiguity into constraints, and make correctness legible."

some questions that almost always matter (and that you can learn to ask on purpose):

* **goal**: what outcome are we trying to produce? (not the mechanism)
* **non-goals**: what are we explicitly not doing?
* **invariants**: what would make this change wrong even if tests pass?
* **failure modes**: how does this fail in prod? what do we do then?
* **verification**: what did we run? what would convince us this works?
* **rollout/rollback**: how do we deploy safely, and how do we undo it?

# Yada, Yada
There is more about how the pr description should be with detailed example. 

# Conclusion
So author is trying to say the pr should be more verbose, for other person to review. It's not about the code, it's more about higher level implementation details. I feel this should be there before as well. Or without this how could you review? I think this should be more moved towards the one how created the diff. He should ask himself before engineering that diff.

