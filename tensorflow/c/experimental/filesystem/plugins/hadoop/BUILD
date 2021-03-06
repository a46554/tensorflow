# Experimental hadoop filesystem plugin.
load("//tensorflow:tensorflow.bzl", "get_win_copts", "tf_cc_shared_object", "tf_cc_test")

package(
    licenses = ["notice"],  # Apache 2.0
)

# Filesystem implementation for HADOOP environments
tf_cc_shared_object(
    name = "hadoop_filesystem",
    framework_so = [],
    linkstatic = False,
    per_os_targets = 1,
    visibility = ["//visibility:public"],
    deps = [":hadoop_filesystem_impl"],
)

# The real implementation of the filesystem.
cc_library(
    name = "hadoop_filesystem_impl",
    srcs = ["hadoop_filesystem.cc"],
    hdrs = ["hadoop_filesystem.h"],
    copts = select({
        "//conditions:default": [],
        "//tensorflow:windows": get_win_copts(),
    }),
    deps = [
        "//tensorflow/c:env",
        "//tensorflow/c:tf_status",
        "//tensorflow/c/experimental/filesystem:filesystem_interface",
        "//third_party/hadoop:hdfs",
        "@com_google_absl//absl/base:core_headers",
        "@com_google_absl//absl/synchronization",
    ],
)

# This test is set to manual because it requires downloading the Hadoop
# distribution to run. To run this test:
# 1. Ensure $JAVA_HOME is set to the location of a JDK 8 installation.
# 2. Download the binary Hadoop distribution from:
#    http://hadoop.apache.org/releases.html
# 3. Extract the Hadoop distribution and run:
#    source libexec/hadoop-config.sh
# 4. Optionally set up HDFS cluster configurations (optionally Kerberos) within
#    $HADOOP_HDFS_HOME/etc/hadoop if you want to test against real
#    distributed HDFS cluster
# 5. bazel test \
#      --test_env=LD_LIBRARY_PATH=$JAVA_HOME/jre/lib/amd64/server \
#      --test_env=HADOOP_HDFS_HOME=$HADOOP_HDFS_HOME \
#      --test_env=CLASSPATH=$($HADOOP_HDFS_HOME/bin/hadoop classpath --glob) \
#      :hadoop_file_system_test
#    To test against the real distributed cluster, add the following option for
#    bazel test:
#      --test_env=HADOOP_TEST_TMPDIR=hdfs://cluster/test/tmp/dir
tf_cc_test(
    name = "hadoop_filesystem_test",
    srcs = [
        "hadoop_filesystem_test.cc",
    ],
    tags = [
        "manual",
        "notap",
    ],
    deps = [
        ":hadoop_filesystem_impl",
        "//tensorflow/core/platform:path",
        "//tensorflow/core/platform:stacktrace_handler",
        "//tensorflow/core/platform:test",
    ],
)
