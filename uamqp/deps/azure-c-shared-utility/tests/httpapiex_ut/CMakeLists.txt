#Copyright (c) Microsoft. All rights reserved.
#Licensed under the MIT license. See LICENSE file in the project root for full license information.

#this is CMakeLists.txt for httpapiex_ut
cmake_minimum_required(VERSION 2.8.11)

if(NOT ${use_http})
	message(FATAL_ERROR "httpapiex_ut being generated without HTTP support")
endif()

compileAsC11()
set(theseTestsName httpapiex_ut )

include_directories(${SHARED_UTIL_REAL_TEST_FOLDER})

set(${theseTestsName}_test_files
${theseTestsName}.c
)

set(${theseTestsName}_c_files
../../src/httpapiex.c
../real_test_files/real_vector.c
../real_test_files/real_crt_abstractions.c
)

set(${theseTestsName}_h_files
)

build_c_test_artifacts(${theseTestsName} ON "tests/azure_c_shared_utility_tests")