# python-test-env

A lightweight functional test environment utilizing nose/nose2, unittest, logging, ConfigParser, [coverage](http://coverage.readthedocs.io/en/latest/) and ant [JUnitReport](https://ant.apache.org/manual/Tasks/junitreport.html).

Test cases are added to *tests* and extend BaseTestCase from framework.testbase.  Test utilities are added to *utils*.

Test settings are defined in *framework/test_settings.cfg* and are accessible as a dictionary from the *settings* class variable.  The case-sensitive section name is specified as an argument to *run_tests.py*.

Tests log to *log/test.log* using the *log* class variable.  Logging is configured using *framework/logging.cfg* and by default sets a log level of DEBUG and auto-rotates (50) 2 MB files.  Timestamps are in local time.

    2016-11-19 10:53:08,931 DEBUG    [test_example] (test_logging) debug level message

Reports (*nosetests.xml*, *nose2-junit.xml* and xml/html required/generated by ant JUnitReport) are located in *reports*.

The resources directory is a place for various files needed by the tests.

The *run_tests.py* script wraps nose/nose2, sets PY_TEST_ENV environment variable to the section name in *framework/test_settings.cfg* to be used by *config.py* and can optionally call ant to generate html reports.  ant must be in the path to use the html generation option.  It was tested with ant 1.9.7.

    $ python run_tests.py
    $ python run_tests.py --testenv env1 --nose2 --xml
    $ python run_tests.py -te env1 -a tags=tag1 tags=tag3
    $ python run_tests.py tests.test_example tests.test_example2 -te env1 -n2
    $ python run_tests.py --help

#### Jenkins Integration

Manage Jenkins > Manage Plugins
* Git plugin
* Violations plugin
* Test Results Analyzer Plugin
* Cobertura Plugin
* Workspace Cleanup Plugin
* Green Balls
* Email Extension Plugin

New Item > Freestyle project

Source Code Management > Git > Repository URL

Build Environment > Delete workspace before build starts

Build > Execute shell > Command

    python run_tests.py -te env1 --xml -n2
    nose2 --with-coverage --coverage utils --coverage-report xml tests.utils_tests
    export PYTHONPATH='.'
    pylint -f parseable utils tests | tee reports/pylint.out

Post-build Actions > Archive the artifacts > log/*

Post-build Actions > Publish Cobertura Coverage Report > coverage.xml

Post-build Actions > Publish JUnit test result report > Test report XMLs > reports/nose2-junit.xml

Post-build Actions > Report Violations > pylint > reports/pylint.out

Post-build Actions > Editable Email Notification > Content Type > HTML (text/html)

Post-build Actions > Editable Email Notification > Default Content > ${JELLY_SCRIPT,template="html"}

#### Links

[Nose documentation](http://nose.readthedocs.io/en/latest/index.html)

[Nose2 documentation](http://nose2.readthedocs.io/en/latest/index.html)

Great post from Steve Brettschneider titled [Automated python unit testing, code coverage and code quality analysis with Jenkins](http://bhfsteve.blogspot.com/2012/04/automated-python-unit-testing-code_27.html)

Alex Conrad's [Jenkins and Python](http://www.alexconrad.org/2011/10/jenkins-and-python.html) post
