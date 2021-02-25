# TF Internals Debugging Techniques

You can use following commands to debug TF internal code or verify any code change you have made to TF internals.

All of the following commands are run under the [root dir of TF repo](https://github.com/tensorflow/tensorflow/).

# run Python unit tests
```
bazel test --test_output=all --verbose_failures -c opt  //tensorflow/python/data/kernel_tests:unbatch_test
```
First time run takes 1 hour first time as it needs to build the whole TF projects, but subsequent run is very fast.


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

