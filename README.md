# fixtures

Before we start to talk about fixtures, we need to understand a little more about tests. Kent Beck, in 
Test Driven-Development By Example, on page 99 said that every test consists of, at least, 3 steps:

-Arrange: is where we create objects, provide data and prepare everything for we use to exercise our method;
-Act:  is where we interact with the SUT (System Under Test);
-Assert: is where we look the results given after the Act step and check if it looks how we'd expected.

All these steps can be in a single line of code, and Kent Beck says that it is called One Step Test.

But, create tests can result in a redundant code, causing problems like performance drop. 

"If we want to remove duplication from our model code, do we want to remove it
also from our test code? Maybe." Kent Beck, Test Driven-Development By Example, pg. 163

From that idea, we can understand for what we use fixtures.

A test fixture is the fixed state that exists at the start of a test. A test fixture ensures
that a test is repeatable—every time a test is run it starts in the same state so it
should produce the same results. A fixture may be set up before the test runs and
torn down after it has finished.

But, where do they but where do they come in?

Gerard Meszaros, in xUnit Test Patterns pg. 358, said that, now, we have a Four-Phases Test instead of 3, and they are:

-Setup: In the first phase, we set up the test fixture that is required for the SUT to exhibit the expected behavior as 
well as anything you need to put in place to be able to observe the actual outcome;

-Exercise: In the second phase, we interact with the SUT;

-Verify: In the third phase, we do whatever is necessary to determine whether the expected outcome has been obtained;

TeardDown: In the fourth phase, we tear down the test fixture to put the world back into the state in which we found it.

So, instead we run the same code many times, we can just attach the fixture function to tests and it will run and return
the results for the test before it run any of them.

So, the fixtures are how we prepare for a test, but how do we tell to pytest wich fixture is going to be used
for wich test?
At a basic level, test functions request fixtures by declaring them as arguments. When pytest goes to run a test, 
it looks at the parameters in that test function’s signature, and then searches for fixtures that have the same 
names as those parameters. Once pytest finds them, it runs those fixtures, captures what they returned (if anything), 
and passes those objects into the test function as arguments.

Sometimes you may want to have a fixture (or even several) that you know all your tests will depend on. 
“Autouse” fixtures are a convenient way to make all tests automatically request them. This can cut out 
a lot of redundant requests, and can even provide more advanced fixture usage (more on that further down).
We can make a fixture an autouse fixture by passing in autouse=True to the fixture’s decorator.

When we run our tests, we’ll want to make sure they clean up after themselves so they don’t mess with 
any other tests (and also so that we don’t leave behind a mountain of test data to bloat the system). 
Fixtures in pytest offer a very useful teardown system, which allows us to define the specific 
steps necessary for each fixture to clean up after itself.

“Yield” fixtures yield instead of return. With these fixtures, we can run some code and pass an object back 
to the requesting fixture/test, just like with the other fixtures. The only differences are:

- The "return" is exchanged for the "yield".

- Any TearDown code for this fixture is placed after the yield.

Once pytest figures out a linear order for the fixtures, it will run each one up until it returns or yields, 
and then move on to the next fixture in the list to do the same thing.
Once the test is finished, pytest will go back down the list of fixtures, but in the reverse order, taking 
each one that yielded, and running the code inside it that was after the yield statement.


Fixtures are created when first requested by a test, and are destroyed based on their scope:

-function: the default scope, the fixture is destroyed at the end of the test;

-class: the fixture is destroyed during teardown of the last test in the class;

-module: the fixture is destroyed during teardown of the last test in the module;

-package: the fixture is destroyed during teardown of the last test in the package;

-session: the fixture is destroyed at the end of the test session;


Fixture availability is determined from the perspective of the test. A fixture is only available for tests 
to request if they are in the scope that fixture is defined in. If a fixture is defined inside a class, it 
can only be requested by tests inside that class. But if a fixture is defined inside the global scope of 
the module, than every test in that module, even if it’s defined inside a class, can request it.

In the same way, a test can also only be affected by an autouse fixture if that test is in the same scope that 
autouse fixture is defined in.A fixture can also request any other fixture, no matter where it’s defined, 
as long as the test requesting them can see all fixtures involved.

Fixtures are defined using the @pytest.fixture decorator. Pytest has useful built-in fixtures, 
listed down below for reference:

capfd:
Capture, as text, output to file descriptors 1 and 2.

capfdbinary:
Capture, as bytes, output to file descriptors 1 and 2.

caplog:
Control logging and access log entries.

capsys:
Capture, as text, output to sys.stdout and sys.stderr.

capsysbinary:
Capture, as bytes, output to sys.stdout and sys.stderr.

cache:
Store and retrieve values across pytest runs.

doctest_namespace:
Provide a dict injected into the docstests namespace.

monkeypatch:
Temporarily modify classes, functions, dictionaries, os.environ, and other objects.

pytestconfig:
Access to configuration values, pluginmanager and plugin hooks.

record_property:
Add extra properties to the test.

record_testsuite_property:
Add extra properties to the test suite.

recwarn:
Record warnings emitted by test functions.

request:
Provide information on the executing test function.

testdir:
Provide a temporary test directory to aid in running, and testing, pytest plugins.

tmp_path:
Provide a pathlib.Path object to a temporary directory which is unique to each test function.

tmp_path_factory:
Make session-scoped temporary directories and return pathlib.Path objects.

tmpdir:
Provide a py.path.local object to a temporary directory which is unique to each test function; replaced by tmp_path.

tmpdir_factory:
Make session-scoped temporary directories and return py.path.local objects; replaced by tmp_path_factory.

References:

MESZAROS, Gerard. xUnit Test Patterns. Addison-Wesley, 2007.
BECK, Kent. Test Driven-Development By Example. Addison-Wesley, 2000.
pytest fixtures: explicit, modular, scalable. pytest, Full pytest Documentarion, 2015. Available in: 
<https://docs.pytest.org/en/6.2.x/fixture.html#what-fixtures-are>. Accessed on: February 2, 2022
