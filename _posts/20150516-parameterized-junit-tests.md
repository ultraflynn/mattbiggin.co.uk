---
layout: post
title: Parameterized JUnit tests
tags: [Unit Testing, Java, JUnit, TDD]
synopsis: Some classes have just a few input but many different combinations and writing a test for each becomes really repetitive. Parameterized tests in JUnit offer a neat way to define a test and then push as many combinations of inputs as you can dream up.
---
Aside from proving that your code works the major goal of any unit test is to provide a living document that shows how to actually *use* your code. Just writing a test to prove that everything is groovy is not enough. One day you, or another developer may want to add more functionality and it is your unit tests that show what it is capable of.

That said I believe the major goal of any unit test is to clearly express what you code can do. As a side affect thus proving that it does in fact work that way.

There is a type of test that easily produces lots of highly repetitive boilerplate and as such obscures showing off the functionality of your code. That type of test is where given just a few inputs it is possible to produce a myriad of possible combinations. Here is an example.

### Let's start with an example
As part of our user registration we need to validate the user name that is being requested. This is allowed to contain letters, both upper and lower case, numbers, underscores and hyphens. And nothing else. No whitespace, hashes, ampersands, etc. It cannot contain only numbers, hyphens or underscores though, it must contain some letters in there somewhere.

#### Write the first test
```java
@Test
public void shouldAcceptLowerCaseUserNames() {
    Pattern pattern = Pattern.compile("...");
    Matcher m = pattern.matcher("LOWERCASE");
    assertThat(m.matches(), is(true));
}
```

#### Add some more tests
That's good, now for the next test.

```java
@Test
public void shouldAcceptUpperCaseUserNames() {
    Pattern pattern = Pattern.compile("...");
    Matcher m = pattern.matcher("UPPERCASE");
    assertThat(m.matches(), is(true));
}
```

#### Refactor as much as possible
Right, we have some serious duplication there so let's refactor that away and inline the `Matcher` to get that as small as possible. I also create the pattern under test as a constant so it is loud and proud at the top of the unit test.

```java
private static final Pattern PATTERN = Pattern.compile("...");

@Test
public void shouldAcceptLowerCaseUserNames() {
    assertThat(PATTERN.matcher("lowercase").matches(), is(true));
}

@Test
public void shouldAcceptUpperCaseUserNames() {
    assertThat(PATTERN.matcher("UPPERCASE").matches(), is(true));
}
```

That's ok I guess. Each test case is 5 lines. How many other test cases are there though?

* Mixed case (accepted)
* Mixed case including numbers (accepted)
* Lower case including many numbers (accepted)
* All numbers (rejected)
* All hyphens (rejected)
* All underscores (rejected)
...

The list is going to go on and on, each time adding another 5 lines to your unit test. I'm not going to entertain the idea of cutting out some newlines and putting those 5 lines on 1 line either, that's an anti-pattern and nasty to maintain. You can clearly see we are going to end up with a monster test and most importantly it's going to be really hard to see all the cases under test. You would have to scroll through the entire file and that's going to get tedious.

### There is a alternative though, let's create a parameterized test
Here is the general gist of what we are trying to acheive.

```java
private static final Pattern PATTERN = Pattern.compile("...");

private String username;
private boolean isValid;

@Test
public void shouldValidateUsernameCorrectly() {
    assertThat(PATTERN.matcher(username).matches(), is(isValid));
}
```

We want the test here to run over and over with the two fields (username and isValid) set to different values each time. On each test iteration the username is set to the value under test and the assertion uses isValid to determine whether the regular expression should accept it or not. To make this work we need to add a little JUnit decorations and define our test cases.

```java
@RunWith(Parameterized.class)
public class UsernameTest {
    private static final Pattern PATTERN = Pattern.compile("...");

    private static final Collection<Object[]> TEST_CASES = Arrays.asList(new Object[][] {
            {"Includes hyphen",         "valid-valid",  true},
            {"Includes underscores",    "valid_valid",  true},
            {"Numeric characters only", "123",          true},
            {"Mixed case",              "validValid",   true},
            {"Lowercase",               "valid",        true},
            {"Uppercase",               "VAlID",        true}
            // ... etc, etc, etc
    });
    
    @Parameters(name = "{0}: '{1}' should be {2}")
    public static Collection<Object[]> testCases() {
        return TEST_CASES;
    }

    @Parameter(value = 0)
    private String testName;

    @Parameter(value = 1)
    private String username;

    @Parameter(value = 2)
    private boolean isValid;


    @Test
    public void shouldValidateUsernameCorrectly() {
        assertThat(PATTERN.matcher(username).matches(), is(isValid));
    }
}
```

Now all your test cases are clearly defined at the top of the test. Each includes a description of the test, the username being tested and the expected result. Now the functionality of your code is laid bare. It's easy to see what it does, easy to add new test cases and most importantly easy to understand.

I believe that parameterized tests are a useful tool to have in your toolbox when writing unit tests. When the situation dictates they provide clarity in the face of an explosion of testing code.

You can find more details about this on the JUnit website: [Parameterized Tests](https://github.com/junit-team/junit/wiki/Parameterized-tests)   
