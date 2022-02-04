
# fixtures

Before we start to talk about fixtures, we need to understand a little more about tests.<br>

## Test Structure 3A:

Kent Beck, in Test Driven-Development By Example, on page 99 said that every test consists of, at least, 3 steps:<br>

<p><strong>-Arrange</strong>: is where we create objects, provide data and prepare everything for we use to exercise our method;

<p>-<strong>Act</strong>:  is where we interact with the SUT (System Under Test);

<p>-<strong>Assert</strong>: is where we look the results given after the Act step and check if it looks how we'd expected.</p>


<p>All these steps can be in a single line of code, and Kent Beck says that it is called One Step Test.<br>

But, create tests can result in a redundant code, causing problems like performance drop.<br> 

<p><em>"If we want to remove duplication from our model code, do we want to remove it
also from our test code? Maybe."</em> Kent Beck, Test Driven-Development By Example, pg. 163<br>

From that idea, we can understand for what we use fixtures.

## What are fixtures?

A <strong>test fixture</strong> is the fixed state that exists at the start of a test. A test fixture ensures
that a test is repeatable—every time a test is run it starts in the same state so it
should produce the same results. A fixture may be set up before the test runs and
torn down after it has finished.

But, where do they but where do they come in?

## Four-Phases Test:

Gerard Meszaros, in xUnit Test Patterns pg. 358, said that, now, we have a Four-Phases Test instead of 3, and they are:

<p>-Setup: In the first phase, we set up the test fixture that is required for the SUT to exhibit the expected behavior as 
well as anything you need to put in place to be able to observe the actual outcome;


<p>-Exercise: In the second phase, we interact with the SUT;


<p>-Verify: In the third phase, we do whatever is necessary to determine whether the expected outcome has been obtained;


<p>TeardDown: In the fourth phase, we tear down the test fixture to put the world back into the state in which we found it.


So, instead we run the same code many times, we can just attach the fixture function to tests and it will run and return
the results for the test before it run any of them.

So, the fixtures are how we prepare for a test, but how do we tell to pytest wich fixture is going to be used
for wich test?<\p>
At a basic level, test functions request fixtures by declaring them as arguments. When pytest goes to run a test, 
it looks at the parameters in that test function’s signature, and then searches for fixtures that have the same 
names as those parameters. Once pytest finds them, it runs those fixtures, captures what they returned (if anything), 
and passes those objects into the test function as arguments.

## Autouse:

Sometimes you may want to have a fixture (or even several) that you know all your tests will depend on. 
“Autouse” fixtures are a convenient way to make all tests automatically request them. This can cut out 
a lot of redundant requests, and can even provide more advanced fixture usage (more on that further down).
We can make a fixture an autouse fixture by passing in autouse=True to the fixture’s decorator.

## TearDown:

When we run our tests, we’ll want to make sure they clean up after themselves so they don’t mess with 
any other tests (and also so that we don’t leave behind a mountain of test data to bloat the system). 
Fixtures in pytest offer a very useful teardown system, which allows us to define the specific 
steps necessary for each fixture to clean up after itself.

## Teard Down - yield:

“Yield” fixtures yield instead of return. With these fixtures, we can run some code and pass an object back 
to the requesting fixture/test, just like with the other fixtures. The only differences are:

<p>- The "return" is exchanged for the "yield".


<p>- Any TearDown code for this fixture is placed after the yield.


Once pytest figures out a linear order for the fixtures, it will run each one up until it returns or yields, 
and then move on to the next fixture in the list to do the same thing.
Once the test is finished, pytest will go back down the list of fixtures, but in the reverse order, taking 
each one that yielded, and running the code inside it that was after the yield statement.

## Fixtures Scopes:

Fixtures are created when first requested by a test, and are destroyed based on their scope:

<p>-function: the default scope, the fixture is destroyed at the end of the test;


<p>-class: the fixture is destroyed during teardown of the last test in the class;


<p>-module: the fixture is destroyed during teardown of the last test in the module;


<p>-package: the fixture is destroyed during teardown of the last test in the package;


<p>-session: the fixture is destroyed at the end of the test session;


## Fixture Availability:

Fixture availability is determined from the perspective of the test. A fixture is only available for tests 
to request if they are in the scope that fixture is defined in. If a fixture is defined inside a class, it 
can only be requested by tests inside that class. But if a fixture is defined inside the global scope of 
the module, than every test in that module, even if it’s defined inside a class, can request it.

<p>In the same way, a test can also only be affected by an autouse fixture if that test is in the same scope that 
autouse fixture is defined in.A fixture can also request any other fixture, no matter where it’s defined, 
as long as the test requesting them can see all fixtures involved.<\p>

<p>Fixtures are defined using the @pytest.fixture decorator. Pytest has useful built-in fixtures, 
listed down below for reference:

<p>capfd: Capture, as text, output to file descriptors 1 and 2.

<p>capfdbinary: Capture, as bytes, output to file descriptors 1 and 2.

<p>caplog: Control logging and access log entries.

<p>capsys: Capture, as text, output to sys.stdout and sys.stderr.

<p>capsysbinary: Capture, as bytes, output to sys.stdout and sys.stderr.

<p>cache: Store and retrieve values across pytest runs.

<p>doctest_namespace: Provide a dict injected into the docstests namespace.

<p>monkeypatch: Temporarily modify classes, functions, dictionaries, os.environ, and other objects.

<p>pytestconfig: Access to configuration values, pluginmanager and plugin hooks.

<p>record_property: Add extra properties to the test.

<p>record_testsuite_property: Add extra properties to the test suite.

<p>recwarn: Record warnings emitted by test functions.

<p>request: Provide information on the executing test function.

<p>testdir: Provide a temporary test directory to aid in running, and testing, pytest plugins.

<p>tmp_path: Provide a pathlib.Path object to a temporary directory which is unique to each test function.

<p>tmp_path_factory: Make session-scoped temporary directories and return pathlib.Path objects.

<p>tmpdir: Provide a py.path.local object to a temporary directory which is unique to each test function; replaced by tmp_path.

<p>tmpdir_factory: Make session-scoped temporary directories and return py.path.local objects; replaced by tmp_path_factory.

## References:

<p>MESZAROS, Gerard. xUnit Test Patterns. Addison-Wesley, 2007.<br>
<p>BECK, Kent. Test Driven-Development By Example. Addison-Wesley, 2000.<br>
<p>pytest fixtures: explicit, modular, scalable. pytest, Full pytest Documentarion, 2015. Available in: 
<https://docs.pytest.org/en/6.2.x/fixture.html#what-fixtures-are>. Accessed on: February 2, 2022
