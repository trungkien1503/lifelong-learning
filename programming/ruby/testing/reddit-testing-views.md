*What to Test*

1. *All the expected elements are present and say the right thing.* Does the view have a Save button? Nothing on the position, just make sure it's there.
2. *Variations based on data.* If a blog post's title field is optional, then test if you display the placeholder title anyway.
3. *What happens when there is no data?* Make sure things show up the way they are supposed to when expected records are not present.

When? Same time as when all the other tests run.

Integration tests are slow. The more they are and the more complicated they are, the slower they run. Do have them, but follow the test pyramid: Make many, many more simple and fast tests and fewer slower, end-to-end tests (UI integration tests).

Integration tests are also hard to write well.

