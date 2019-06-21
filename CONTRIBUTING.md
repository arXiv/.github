# Contributing to arXiv projects

Thanks for your interest in contributing to arXiv software development! Here
is a quick run-through of things you should know before diving in.

## Reading

For a high-level overview of the arXiv-NG project, see the [arXiv
arXitecture](https://arxiv.github.io/arxiv-arxitecture/). Of particular
interest:

- [Development practices](https://arxiv.github.io/arxiv-arxitecture/crosscutting/development_practices.html)
  - [Testing & QA](https://arxiv.github.io/arxiv-arxitecture/crosscutting/development_practices.html#testing-qa)
- [Services in arXiv-NG](https://arxiv.github.io/arxiv-arxitecture/crosscutting/services.html)
- [Design system](https://arxiv.github.io/arxiv-arxitecture/crosscutting/design_system.html)

## Making contributions

Take a look at [branch
management](https://arxiv.github.io/arxiv-arxitecture/crosscutting/development_practices.html#branch-management).

In general, we deliver work by raising a pull request from a feature branch
(e.g. ``bug/ARXIVNG-3092``, ``feature/issue-32-better-widgets``) to the
``develop`` branch. You can also raise a PR from a forked repo to the
``develop`` branch of this (arXiv) repo.

For the PR to be merged:

- There should be an open issue documenting the feature, bug, or task to be
  completed. This helps to prevent going down rabbit holes or otherwise
  spending time on the wrong thing.
- All tests must be passing (and the contribution must have tests).
- Mypy type checking should pass.
- Pydocstyle should have no errors.
- Pylint should score at or above 8/10.

More on each of these below.

Please also include a description of the goals of the PR, the overall approach,
and any unresolved questions or potential issues. Examples and/or screen shots
are appreciated if applicable.

## Testing

We use the built-in Python [unittest
framework](https://docs.python.org/3/library/unittest.html) to write all of our
tests. Try to stick to the built-in tooling here.

Tests should live either in a ``tests/`` module at the root of the repo, or 
in a ``tests`` module in a particular component of the application (e.g. 
``someapp/services/tests/``).

We generally run tests with [nose2](https://github.com/nose-devs/nose2), but
you can also use [pytest](https://docs.pytest.org/en/latest/) if you prefer.

We aim for tests at the following levels of granularity:

- Unit tests. We focus on unit tests for public functions/classes of modules.
  These should be consciously designed to avoid testing behavior that is 
  implementation-specific.
- Module tests. These test whole functional components within the application,
  making liberal use of Python's [mock
  library](https://docs.python.org/3/library/unittest.mock.html). The kinds of
  modules that you might test include:

  - Domain modules. These capture the core concepts, rules, and interactions
    within the application. You shouldn't need to mock anything, because the
    domain must not depend on any other components of the application.
  - Controller modules. Mock service modules (e.g. for persistence), and test
    return values against whatever parameters might get passed in by the
    routes.
  - Service integration modules. If possible, mock/spoof the external service
    that you're integrating with. For AWS services,
    [moto](https://github.com/spulec/moto) is pretty nice.

- Application tests. These tests run the whole application, possibly mocking
  external dependencies as needed. Use the [Flask/Werkzeug test
  tooling](http://flask.pocoo.org/docs/1.0/testing/) for these tests.

For some useful background on test design, check out [The Practical Test
Pyramid](https://martinfowler.com/articles/practical-test-pyramid.html).

## Type annotations + static checks

We use type annotations throughout the running codebase (i.e. everything except
tests). Check out Python's built-in
[typing](https://docs.python.org/3/library/typing.html) library. 

We use [mypy](https://mypy.readthedocs.io/en/latest/) to type-check code. If
everything checks out, the following should return no lines and exit 0, using 
the ``mypyp.ini`` config in the root of this repo.

```bash
pipenv run mypy -p [APPLICATION] | grep -v "test.*" | grep -v "defined here"
```

You can also just run:

```bash
./tests/type-check.sh [APPLICATION]
```

## Linting + Documentation

Code should adhere as closely as possible to
[PEP008](https://www.python.org/dev/peps/pep-0008/). The following should
exit 0:

```bash
pipenv run pydocstyle --convention=numpy --add-ignore=D401 [APPLICATION]
```

Or just run:

```bash
./tests/style.sh [APPLICATION]
```

We use [Numpy
style](https://github.com/numpy/numpy/blob/master/doc/HOWTO_DOCUMENT.rst.txt)
for docstrings, and otherwise follow
[PEP257](https://www.python.org/dev/peps/pep-0257/). All modules, classes,
public functions (i.e. not starting with ``_``) should have docstrings. It's
also nice if you add docstrings for constants and class attributes; see
[PEP224](https://www.python.org/dev/peps/pep-0224/).

A ``.pylintrc`` config file can be found in the root of this repository. The 
following should score >= 8/10.

```bash
pipenv run pylint [APPLICATION]
```

Or run:

```bash
./tests/lint.sh [APPLICATION]
```