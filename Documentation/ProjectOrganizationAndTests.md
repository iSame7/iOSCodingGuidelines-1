# Project Organization
## Folder strucutre
For the project structure we adhere to a use case-based file grouping. That said, we group our files and classes in folders according to functionality. For example, all the classes necessary for a Catalog screen implementation we group into `Catalog` folder in Xcode. Within such groups could be other related sub-groups. For example, within an Article folder there are folders for ImagesSection, RecommendationsSection and ReviewsSection. Grouping of related files together makes it easy to understand app structure, navigate between classes and localize changes within one group. If necessary, such groups could be further extracted as separate components in the future.

Try to avoid architecture-related groupings, such as splitting all the files by "Model", "Controller", "View". 


# Unit Tests
## Where to put tests?
We put tests next to the class under test. That makes navigation between test and tested class much easier than if tests would reside in a separate "Tests" folder.

## Frameworks
We use [SwiftHamcrest](https://github.com/nschum/SwiftHamcrest) and [OCHamcrest](https://github.com/hamcrest/OCHamcrest) for making unit tests more readable. For mocking, we either create our own mocks or use [OCMock](http://ocmock.org).

For UI automation testing we found [KIF](https://github.com/kif-framework/KIF/) is much better solution than Apple's UIAutomation, especially when it comes to server mocking (we use [AMYServer](https://github.com/kif-framework/AMYServer)).

## Naming
Name your tests with descriptive names. Good name should have a precondition and assertion to clearly indicate what scenario is tested. For example:

```
test_whenFilterHasQuery_resultContainsQueryAndNumberOfItems
test_whenNoArticles_resultContainsNumberZero
test_whenSkuListHasElements_appendsSkuListWithCommas
```

## Test method structure
Make sure your test complies with 3 A's: Arrange, Act, Assert. Sometimes, it is 4 A's: Arrange, Act, Assert, Annihilate. 

1. Arrange: prepare test fixtures (test data)
1. Act: execute action under test
1. Assert: assert that actual results of action are the same as expected
1. Annihilate: clean up (for example, delete temporary test files)

The parts of a test listed above are logical parts. In general, there could be multiple statements constituting a single logical test part.

Refactor your tests.
