# GTest Notes

---
GoogleTest is a testing framework developed by the Testing Technology team with Google’s specific requirements and constraints in mind. Whether you work on Linux, Windows, or a Mac, if you write C++ code, GoogleTest can help you. And it supports any kind of tests, not just unit tests.

---
When using GoogleTest, you start by writing assertions, which are statements that check whether a condition is true. An assertion’s result can be success, nonfatal failure, or fatal failure. If a fatal failure occurs, it aborts the current function; otherwise the program continues normally.

---
<mark>ASSERT_*</mark> is used when you want to generate a fatal failure to a verification, and abort the execution. <mark>EXPECT_*</mark> is used to generate nonfatal failures and continue the execution after verification - it just reports the failure in the test.

To provide custom messages, just stream it to the macro:
```
ASSERT_EQ(x.size(), y.size()) << "Vectors x and y are of unequal length";
```

---

## Creating Tests

Use the TEST() macro to define and name a test function. TEST() arguments go from general to specific. The first argument is the name of the test suite, and the second argument is the test’s name within the test suite. Both names must be valid C++ identifiers, and they should not contain any underscores (_).

GoogleTest groups the test results by test suites, so logically related tests should be in the same test suite; in other words, the first argument to their TEST() should be the same. Example:
```
int Factorial(int n);  // Returns the factorial of n

// Tests factorial of 0.
TEST(FactorialTest, HandlesZeroInput) {
  EXPECT_EQ(Factorial(0), 1);
}

// Tests factorial of positive numbers.
TEST(FactorialTest, HandlesPositiveInput) {
  EXPECT_EQ(Factorial(1), 1);
  EXPECT_EQ(Factorial(2), 2);
  EXPECT_EQ(Factorial(3), 6);
  EXPECT_EQ(Factorial(8), 40320);
}
```

## Using Data to multiple tests:
