# Copyright 2017 gRPC authors.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

licenses(["notice"])  # 3-clause BSD

package(default_visibility = ["//visibility:public"])

load("@rules_cc//cc:defs.bzl", "cc_library", "cc_test")

load("@rules_proto//proto:defs.bzl", "proto_library")
load("@rules_cc//cc:defs.bzl", "cc_binary", "cc_proto_library")
load("@com_github_grpc_grpc//bazel:cc_grpc_library.bzl", "cc_grpc_library")

config_setting(
    name = "windows",
    constraint_values = ["@bazel_tools//platforms:windows"],
)

config_setting(
    name = "has_absl",
    values = {"define": "absl=1"},
)

# The following three rules demonstrate the usage of the cc_grpc_library rule in
# in a mode compatible with the native proto_library and cc_proto_library rules.
proto_library(
    name = "helloworld_proto",
    srcs = ["helloworld.proto"],
)

cc_proto_library(
    name = "helloworld_cc_proto",
    deps = [":helloworld_proto"],
)

cc_grpc_library(
    name = "helloworld_cc_grpc",
    srcs = [":helloworld_proto"],
    grpc_only = True,
    deps = [":helloworld_cc_proto"],
)

cc_binary(
    name = "greeter_client",
    srcs = ["greeter_client.cc"],
    defines = ["BAZEL_BUILD"],
    deps = [
        ":helloworld_cc_grpc",
        # http_archive made this label available for binding
        "@com_github_grpc_grpc//:grpc++",
    ],
)

cc_binary(
    name = "greeter_server",
    srcs = ["greeter_server.cc"],
    defines = ["BAZEL_BUILD"],
    deps = [
        ":helloworld_cc_grpc",
        # http_archive made this label available for binding
        "@com_github_grpc_grpc//:grpc++",
    ],
)

# The following rules build samples of how to use gTest.
cc_library(
    name = "gtest_sample_lib",
    srcs = [
        "googletest/samples/sample1.cc",
        "googletest/samples/sample2.cc",
        "googletest/samples/sample4.cc",
    ],
    hdrs = [
        "googletest/samples/prime_tables.h",
        "googletest/samples/sample1.h",
        "googletest/samples/sample2.h",
        "googletest/samples/sample3-inl.h",
        "googletest/samples/sample4.h",
    ],
    features = select({
        ":windows": ["windows_export_all_symbols"],
        "//conditions:default": [],
    }),
)

# Google Test including Google Mock
cc_library(
    name = "gtest",
    srcs = glob(
        include = [
            "googletest/src/*.cc",
            "googletest/src/*.h",
            "googletest/include/gtest/**/*.h",
            "googlemock/src/*.cc",
            "googlemock/include/gmock/**/*.h",
        ],
        exclude = [
            "googletest/src/gtest-all.cc",
            "googletest/src/gtest_main.cc",
            "googlemock/src/gmock-all.cc",
            "googlemock/src/gmock_main.cc",
        ],
    ),
    hdrs = glob([
        "googletest/include/gtest/*.h",
        "googlemock/include/gmock/*.h",
    ]),
    copts = select({
        ":windows": [],
        "//conditions:default": ["-pthread"],
    }),
    defines = select({
        ":has_absl": ["GTEST_HAS_ABSL=1"],
        "//conditions:default": [],
    }),
    features = select({
        ":windows": ["windows_export_all_symbols"],
        "//conditions:default": [],
    }),
    includes = [
        "googlemock",
        "googlemock/include",
        "googletest",
        "googletest/include",
    ],
    linkopts = select({
        ":windows": [],
        "//conditions:default": ["-pthread"],
    }),
    deps = select({
        ":has_absl": [
            "@com_google_absl//absl/debugging:failure_signal_handler",
            "@com_google_absl//absl/debugging:stacktrace",
            "@com_google_absl//absl/debugging:symbolize",
            "@com_google_absl//absl/strings",
            "@com_google_absl//absl/types:optional",
            "@com_google_absl//absl/types:variant",
        ],
        "//conditions:default": [],
    }),
)

cc_library(
    name = "gtest_main",
    srcs = ["googlemock/src/gmock_main.cc"],
    features = select({
        ":windows": ["windows_export_all_symbols"],
        "//conditions:default": [],
    }),
    deps = [":gtest"],
)


cc_test(
    name = "gtest_samples",
    size = "small",
    srcs = [
        "googletest/samples/sample1_unittest.cc",
    ],
    linkstatic = 0,
    deps = [
        "@com_github_grpc_grpc//:grpc++",
        ":helloworld_cc_grpc",
        "gtest_sample_lib",
        ":gtest_main",
    ],
)

cc_test(
    name = "sample9_unittest",
    size = "small",
    srcs = ["googletest/samples/sample9_unittest.cc"],
    deps = [":gtest"],
)

cc_test(
    name = "sample10_unittest",
    size = "small",
    srcs = ["googletest/samples/sample10_unittest.cc"],
    deps = [":gtest"],
)
