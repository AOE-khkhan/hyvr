<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
# Table of Contents

- [HyVR Tests](#hyvr-tests)
    - [Default test](#default-test)
    - [Un-randomized test](#un-randomized-test)
    - ["Unittests"](#unittests)
    - [Full test case](#full-test-case)
    - [Recreating test output](#recreating-test-output)
    - [TODOs](#todos)
- [Coverage](#coverage)

<!-- markdown-toc end -->


# HyVR Tests

In order to ensure that HyVR works even after changing something, several tests
have been set up. Tests are created and run using `pytest`.

## Default test

The "default test" is a test that runs a simplified version of the `made.ini`
case (which only outputs as npz-format instead of all available formats) with a
seeded random number generator and compares whether the output is the same as a
reference output.
This test is in `tests/simple_testcases/test_made.py`. It also has the pytest
mark `default`, so it can be run via
```
pytest -m default
```
from the main project directory.

If this test passes, all is good. If it doesn't, this does however not
necessarily mean that HyVR doesn't operate as expected. There are some changes
that might make this test fail without being "wrong". This could either be
changes in HyVR I/O, or, more probable, a different order of operations, leading
to different random numbers being drawn.

## Un-randomized test

Therefore, the test can also be run with all random numbers being replaced by
deterministic numbers. This test is also implemented in
`tests/simple_testcases/test_made.py` and has the mark
`no_random`.

## "Unittests"

For more detailed results, there are also tests that more resemble unit
tests. (These are not "pure" unit tests, as the main hyvr tasks are implemented
as very long functions with lots of input parameters and unit testing is
therefore hard. Unfortunately breaking the code down to shorter functions is not
really an option, as it decreases performance.)
These tests can be found in the directory `tests/unittests`.
The tests in `tests/unittests/geo` are testing construction of a single
stratum with a single architectural element, containing again only one
geometrical object. These aim to test all options for the geometrical
objects. However, this is still in development (a.k.a. I don't have time for
this at the moment).

## Full test case

Finally we also have a full testcase of the MADE site. This can be found in
`tests/full_testcases`. You can run the test using the `run_made.py` script and
plot the results for visual inspection using `plot_made.py`.

## Recreating test output

In case the default test or the non-random test fail, but proper behaviour has
been verified, their output must be regenerated with the current "true" version
of hyvr.
Therefore, run the test in question and move its output to the reference
directory, i.e.:
```
cd tests/simple_testcases
pytest -m default
rm -r made_simple_reference
mv made_simple made_simple_reference
```
or
```
cd tests/simple_testcases
pytest -m no_random
rm -r made_simple_no_random_reference
mv made_simple made_simple_no_random_reference
```


## TODOs
* port the existing "unit tests" for troughs and sheets to HyVR 1.1.0
* create "unit tests"
* there are some even simpler ini-files than `made_simple.ini` in
  `tests/simple_testcases`, these should be reactivated
  
# Coverage

Test coverage can be tested using
[coverage](https://coverage.readthedocs.io/en/coverage-5.0.2/). You can run it
from the top level directory via
```
coverage run run_all_tests.py
```
and generate html reports with
```
coverage html
```
The results can be viewed by opening `htmlcov/index.html`.

Keep in mind that coverage does not include the cython code, so actual coverage
is lower.
