﻿# MIT License
#
# Copyright (c) 2016-present The ZLMediaKit project authors. All Rights Reserved.
#
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
#
# The above copyright notice and this permission notice shall be included in all
# copies or substantial portions of the Software.
#
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.
#

file(GLOB MediaServer_SRC_LIST ./*.cpp ./*.h)

set(COMPILE_DEFINITIONS ${MK_COMPILE_DEFINITIONS})

if(ENABLE_SERVER_LIB)
  list(APPEND COMPILE_DEFINITIONS DISABLE_MAIN)
  add_library(MediaServer STATIC ${MediaServer_SRC_LIST})
  target_compile_definitions(MediaServer
    PRIVATE ${COMPILE_DEFINITIONS})
  target_compile_options(MediaServer
    PRIVATE ${COMPILE_OPTIONS_DEFAULT})
  target_link_libraries(MediaServer
    PRIVATE ${MK_LINK_LIBRARIES})
  update_cached_list(MK_LINK_LIBRARIES MediaServer)
  return()
endif()

# IOS 不编译可执行程序，只做依赖库
if(IOS)
  add_library(MediaServer STATIC ${MediaServer_SRC_LIST})
else()
  add_executable(MediaServer ${MediaServer_SRC_LIST})
endif()

target_compile_definitions(MediaServer
  PRIVATE ${COMPILE_DEFINITIONS})
target_compile_options(MediaServer
  PRIVATE ${COMPILE_OPTIONS_DEFAULT})

if(CMAKE_SYSTEM_NAME MATCHES "Linux")
  target_link_libraries(MediaServer -Wl,--start-group ${MK_LINK_LIBRARIES} -Wl,--end-group)
else()
  target_link_libraries(MediaServer ${MK_LINK_LIBRARIES})
endif()

if(MSVC)
  set(RESOURCE_FILE "${CMAKE_SOURCE_DIR}/resource.rc")
  set_source_files_properties(${RESOURCE_FILE} PROPERTIES LANGUAGE RC)  
  target_sources(MediaServer PRIVATE ${RESOURCE_FILE})
else()
  # Android, IOS, macOS ...
  # CLion, GCC ...
endif()

install(TARGETS MediaServer DESTINATION ${INSTALL_PATH_RUNTIME})
