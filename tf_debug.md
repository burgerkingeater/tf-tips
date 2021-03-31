# TF Techniques

You can use following commands to debug TF internal code or verify any code change you have made to TF internals.

All of the following commands are run under the [root dir of TF repo](https://github.com/tensorflow/tensorflow/).

First time invoking any of the commands takes 1 hour+ as it needs to build the entire TF project, but subsequent run is very fast with bazel cache.

# run Python unit tests with designated bazel cache
```
bazel --output_base=/private/var/tmp/_bazel_chren/7b4326bd388cb9cfdba373fadbcf24aa test --test_output=all --verbose_failures -c opt  //tensorflow/python/data/kernel_tests:unbatch_test
```

run Python unit tests with designated python interpreter
```
--python_path=/usr/local/bin/python3 --noincompatible_use_python_toolchains
```

# run bazel build/tests with higher concurrency
```
bazel build XXX --jobs=XXX  #default jobs is AUTO, on my mac it's set to 16.
```

# run Python unit tests with env vars(E.g TF_CPP_MIN_VLOG_LEVEL or TF_CPP_MIN_LOG_LEVEL)
```
bazel --output_base=/private/var/tmp/_bazel_chren/7b4326bd388cb9cfdba373fadbcf24aa  test --test_output=all --verbose_failures -c opt  //tensorflow/python/profiler:profiler_v2_test --test_env=TF_CPP_MIN_LOG_LEVEL=0 --test_env=TF_CPP_MIN_VLOG_LEVEL=2
```

# run C++ unit tests
```
bazel test --test_output=all --verbose_failures -c opt tensorflow/core:__tensorflow_core_lib_io_legacy_lib_io_all_tests
```
__tensorflow_core_lib_io_legacy_lib_io_all_tests is defined as bazel file group [here](https://github.com/burgerkingeater/tensorflow/blob/0601b81c88ec390de0997448b4a739d723a86479/tensorflow/core/lib/io/BUILD#L342)

# use test filter to run single C++ unit test
```
bazel test -c opt tensorflow/core:__tensorflow_core_lib_io_legacy_lib_io_all_tests --test_filter=BufferedInputStream.ReadLine_EmptyLines --test_output=all
```
BufferedInputStream.ReadLine_EmptyLines is defined [here](https://github.com/tensorflow/tensorflow/blob/v2.4.0/tensorflow/core/lib/io/buffered_inputstream_test.cc#L132)

# add debugging log in C++ files
```
VLOG(1) << dataset()->count_;
```


# Print graph definition
```
std::cout << "SummarizeGraphDef(graph_def)" << std::endl
```


# Debug Estimator with customized built TF
1. add debugging logs in TF
2. build wheel:
```
bazel build //tensorflow/tools/pip_package:build_pip_package
./bazel-bin/tensorflow/tools/pip_package/build_pip_package ~/tmp/tensorflow_pkg/
```
You might encounter error `Fix in-place editing only works for regular files`, [here](https://github.com/tensorflow/tensorflow/issues/45434#issuecomment-739635923) is the resolution.


3. activate or create conda env just for debugging purpose

4. needed if you haven't installed 3rd party dependencies like numpy ```pip install tensorflow=2.4.0 #this installs other needed dependencies(numpy, absl-py)```
5.  ```pip install ~/tmp/tensorflow_pkg/tensorflow-2.4.0-cp38-cp38-macosx_11_0_x86_64.whl   --upgrade --force --no-dependencies ```

You might encounter error `not a supported wheel on this platform` error when pip install wheel package in Conda, [here](https://github.com/apple/tensorflow_macos/issues/153) is the resolution. 

6. run estimator unit tests
e.g:
```
bazel test --test_output=all --verbose_failures -c opt  //tensorflow_estimator/python/estimator:distribute_strategy_estimator_integration_test
```

# Run dist. training strategy locally in unit test
[Estimator unit test example](https://github.com/tensorflow/estimator/blob/master/tensorflow_estimator/python/estimator/distribute_strategy_estimator_training_test.py)


# Other tips
```
When runing unit test with bazel, if the unit test is dumpping something to dir, it might got permission denied error, as the process started by bazel only has write permission in its sandbox dir.
```

