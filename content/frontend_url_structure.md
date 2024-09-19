# Frontend URL structure

# Problem
Usually I don't think that much of url. But now I'm solving a problem at work.  

We have some url like this `/summary?storeId='123'`, If someone is trying to access `/summary`, I should hit api and show set of stores for them to select, then proceed. So I had a question, was that the best approach for defining the URL in first place, as the parameter is already mandatory.


# Better Approach
If the parameter is required to locate the resource, It should be part of url `/summary/<store-id>/`.  
query params can be used for modifying, filtering the resource. Like: `category=laptops&price=low-high`