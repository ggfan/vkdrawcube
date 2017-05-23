#
# Copyright (C) The Android Open Source Project
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

cmake_minimum_required(VERSION 3.4.1)

# Build Vkutils Lib: variables are used by VkUtils lib build
set(UTILS_NAME  vkutils)
get_filename_component(SHADERC_DIR
                       ${ANDROID_NDK}/sources/third_party/shaderc
                       ABSOLUTE)
set(GLSLANG_SPIRV_INCLUDE_DIR ${SHADERC_DIR}/third_party/glslang)
get_filename_component(PROJECT_SOURCE_DIR
                       "${CMAKE_SOURCE_DIR}/../../../.."
                       ABSOLUTE)
get_filename_component(VKUTILS_DIR
                       "${CMAKE_SOURCE_DIR}/../vkutils"
                       ABSOLUTE)
add_library(vkutils_lib STATIC IMPORTED)
add_subdirectory(${CMAKE_SOURCE_DIR}/../vkutils ${CMAKE_BINARY_DIR}/vkutils)

# now build app's shared lib
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -Werror -D VK_USE_PLATFORM_ANDROID_KHR")

add_library(vk-drawcube SHARED
            ${CMAKE_SOURCE_DIR}/../vulkan_wrapper/vulkan_wrapper.cpp
            main.cpp)

# includes should include all support libs from NDK
target_include_directories(vk-drawcube PRIVATE
                           ${NDK_INCLUDE}
                           ${VKUTILS_DIR}
                           ${VKUTILS_DIR/glm}
                           ${CMAKE_SOURCE_DIR}/../data
                           ${CMAKE_SOURCE_DIR}/../vulkan_wrapper)

# add lib dependencies ... vkutils library will pull in Vulkan libraryGood4me33
target_link_libraries(vk-drawcube
                      android
                      log
                      ${UTILS_NAME})
