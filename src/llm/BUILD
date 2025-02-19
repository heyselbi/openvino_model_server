#
# Copyright (c) 2024 Intel Corporation
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#

load("@mediapipe//mediapipe/framework/port:build_config.bzl", "mediapipe_cc_proto_library", "mediapipe_proto_library")
load("//:common_settings.bzl",
     "COMMON_STATIC_LIBS_COPTS", "COMMON_STATIC_LIBS_LINKOPTS", "COMMON_FUZZER_COPTS", "COMMON_FUZZER_LINKOPTS", "COMMON_LOCAL_DEFINES", "PYBIND_DEPS")

COPTS_ADJUSTED = COMMON_STATIC_LIBS_COPTS + select({
        "//conditions:default": [],
        "//:fuzzer_build" : COMMON_FUZZER_COPTS,
})

LINKOPTS_ADJUSTED = COMMON_STATIC_LIBS_LINKOPTS + select({
    "//conditions:default": [],
    "//:fuzzer_build" : COMMON_FUZZER_LINKOPTS,
})

cc_library(
    name = "llm_engine",
    srcs = [],
    deps =  ["@llm_engine//:llm_engine"],
    visibility = ["//visibility:public"],
    copts = COPTS_ADJUSTED,
    linkopts = LINKOPTS_ADJUSTED,
    alwayslink = 1,
)

cc_library(
    name = "llmcalculator",
    srcs = ["http_llm_calculator.cc"],
    deps = [
        "//third_party:openvino",
        "@mediapipe//mediapipe/framework:calculator_framework",
        "@com_github_tencent_rapidjson//:rapidjson",
        "//src/kfserving_api:kfserving_api_cpp",
        "//src:libovmsprofiler",
        ":llm_engine",
        ":llmnoderesources",
        "//src:httppayload",
        "//src:libhttpclientconnection",
        ":text_processor",
        ":openai_completions_api_handler"
    ],
    visibility = ["//visibility:public"],
    local_defines = COMMON_LOCAL_DEFINES,
    copts = COPTS_ADJUSTED,
    linkopts = LINKOPTS_ADJUSTED,
    alwayslink = 1,
)

mediapipe_proto_library(
    name = "llmcalculator_proto", # llmcalculator_cc_proto - just mediapipe stuff with mediapipe_proto_library adding nonvisible target
    srcs = ["llm_calculator.proto"],
    visibility = ["//visibility:private"],
    deps = [
        "@mediapipe//mediapipe/framework:calculator_options_proto",
        "@mediapipe//mediapipe/framework:calculator_proto",
    ],
)

cc_library(
    name = "openai_completions_api_handler",
    hdrs = ["apis/openai_completions.hpp"],
    srcs = ["apis/openai_completions.cpp"],
    deps = [
        "@com_github_tencent_rapidjson//:rapidjson",
        "@mediapipe//mediapipe/framework:calculator_framework", # required for absl status
        ":llm_engine",
        "//src:libovmsprofiler"
    ],
    visibility = ["//visibility:public"],
    copts = COPTS_ADJUSTED,
    linkopts = LINKOPTS_ADJUSTED,
    alwayslink = 1,
)

cc_library(
    name = "llmnoderesources",
    hdrs = ["llmnoderesources.hpp", "llm_executor.hpp",],
    srcs = ["llmnoderesources.cpp",],
    deps = [
        "//third_party:openvino",
        "@mediapipe//mediapipe/framework:calculator_framework",
        "@com_github_tencent_rapidjson//:rapidjson",
        "//src:libmodelconfigjsonparser",
        "//src:libovmslogging",
        "//src:libovmsstatus",
        "//src:libovmsmediapipe_utils",
        "//src:libovmsprofiler",
        "llmcalculator_cc_proto",
        ":llm_engine",
        "//src/python:utils",
        ":text_processor",
    ] + PYBIND_DEPS,
    visibility = ["//visibility:public"],
    local_defines = COMMON_LOCAL_DEFINES,
    copts = COPTS_ADJUSTED,
    linkopts = LINKOPTS_ADJUSTED,
    alwayslink = 1,
)

cc_library(
    name = "text_processor",
    hdrs = ["text_processor.hpp"],
    srcs = ["text_processor.cpp"],
    deps = ["@mediapipe//mediapipe/framework:calculator_framework",
            "//third_party:openvino",
            "//src:libovmslogging",
            "//src/python:utils",
    ] + PYBIND_DEPS,
    visibility = ["//visibility:public"],
    copts = COPTS_ADJUSTED,
    linkopts = LINKOPTS_ADJUSTED,
    alwayslink = 1,
)

