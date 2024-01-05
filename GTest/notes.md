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

Use the <mark>TEST()</mark> macro to define and name a test function. TEST() arguments go from general to specific. The first argument is the name of the test suite, and the second argument is the test’s name within the test suite. Both names must be valid C++ identifiers, and they should not contain any underscores (_).

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

---

## Using Data to multiple tests:
If you find yourself writing two or more tests that operate on similar data, you can use a test fixture.

1. Derive a class from testing::Test . Start its body with protected:, as we’ll want to access fixture members from sub-classes.

2. Inside the class, declare any objects you plan to use.

3. If necessary, write a default constructor or <mark>SetUp()</mark> function to prepare the objects for each test. 

4. If necessary, write a destructor or <mark>TearDown()</mark> function to release any resources you allocated in SetUp()

When using a fixture, use <mark>TEST_F()</mark> instead of <mark>TEST()</mark> as it allows you to access objects and subroutines in the test fixture:

```
#include "TestFixtureClass.h"
TEST_F(TestFixtureClassName, TestName) {
  ... test body ...
}
```

For each test defined with TEST_F(), GoogleTest will create a fresh test fixture at runtime, immediately initialize it via SetUp(), run the test, clean up by calling TearDown(), and then delete the test fixture.

There is a good example of a FIFO Queue in documentation:
```
template <typename E>  // E is the element type.
class Queue {
 public:
  Queue();
  void Enqueue(const E& element);
  E* Dequeue();  // Returns NULL if the queue is empty.
  size_t size() const;
  ...
};

---

class QueueTest : public testing::Test {
 protected:
  void SetUp() override {
     // q0_ remains empty
     q1_.Enqueue(1);
     q2_.Enqueue(2);
     q2_.Enqueue(3);
  }

  // void TearDown() override {}

  Queue<int> q0_;
  Queue<int> q1_;
  Queue<int> q2_;
};

--- 

TEST_F(QueueTest, IsEmptyInitially) {
  EXPECT_EQ(q0_.size(), 0);
}

TEST_F(QueueTest, DequeueWorks) {
  int* n = q0_.Dequeue();
  EXPECT_EQ(n, nullptr);

  n = q1_.Dequeue();
  ASSERT_NE(n, nullptr);
  EXPECT_EQ(*n, 1);
  EXPECT_EQ(q1_.size(), 0);
  delete n;

  n = q2_.Dequeue();
  ASSERT_NE(n, nullptr);
  EXPECT_EQ(*n, 2);
  EXPECT_EQ(q2_.size(), 1);
  delete n;
}
```
Of course, we should separate it into several classes to hold a specific function.

---

## Run Your Tests

After defining your tests, you can run them with <mark>RUN_ALL_TESTS()</mark>, which returns 0 if all the tests are successful, or 1 otherwise. Note that RUN_ALL_TESTS() runs all tests in your link unit–they can be from different test suites, or even different source files.

```
int = RUN_ALL_TESTS();
```

Writing a main function its needed only when you want to do something custom that cannot be expressed within the framework of fixtures and test suites. For more information, look at: https://google.github.io/googletest/primer.html