This repository is a minimal reproducible example of Pants failing to generate a coverage
report when importing `PySide2` package modules in the code (either in the source or test
modules).

Bootstrap Pants:

```
$ curl -L -o ./pants https://pantsbuild.github.io/setup/pants && \
chmod +x ./pants
```

Run standalone `coverage.py` without Pants involved:

```
$ cd minimalcov/minimalcov
$ coverage run --source src -m pytest tests; coverage html
```

or from the repository root

```
$ PYTHONPATH="./minimalcov:$PYTHONPATH" coverage run --source minimalcov/minimalcov/src -m pytest minimalcov/minimalcov/tests; coverage html
```

Both of these results in a coverage report generated.

Running the Pants:

```
$ ./pants test :: --print-stacktrace --no-process-execution-local-cleanup
```

Results in an exception raised:

```
pants.engine.internals.scheduler.ExecutionError: 1 Exception encountered:

  ProcessExecutionFailure: Process 'Generate Pytest html coverage report.' failed with exit code 1.
stdout:
No source for code: '/private/var/folders/k0/3nssm8v15r5b08k1k1wyhs2r0000gp/T/process-executionNiGXI5/(builtin)'.
Aborting report output, consider using -i.

stderr:

```

Inspecting the kept working directory:

``` $ tree -a
.
├── .cache
│   └── pex_root -> /Users/username/.cache/pants/named_caches/pex_root
├── .coverage
├── .coveragerc
├── __run.sh
├── coverage.pex
├── coverage.pex_bin_python_shim.sh
├── coverage.pex_pex_shim.sh
└── minimalcov
    └── minimalcov
        ├── __init__.py
        ├── src
        │   ├── __init__.py
        │   └── foo.py
        └── tests
            ├── __init__.py
            └── test_foo.py
6 directories, 11 files
```

Commenting out this line

```
from PySide2.QtWidgets import QApplication
```

in the `test_foo.py` lets Pants successfully generate the coverage report.
