# Copyright (c) 2021 Triad National Security, LLC, as operator of Los Alamos
# National Laboratory with the U.S. Department of Energy/National Nuclear
# Security Administration. All Rights Reserved.
#
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# with the Software without restriction, including without limitation the
# rights to use, copy, modify, merge, publish, distribute, sublicense, and/or
# sell copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
#
# 1. Redistributions of source code must retain the above copyright notice,
#    this list of conditions and the following disclaimer.
# 2. Redistributions in binary form must reproduce the above copyright notice,
#    this list of conditions and the following disclaimer in the documentation
#    and/or other materials provided with the distribution.
# 3. Neither the name of TRIAD, Los Alamos National Laboratory, LANL, the
#    U.S. Government, nor the names of its contributors may be used to endorse
#    or promote products derived from this software without specific prior
#    written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS "AS IS" AND ANY EXPRESS OR
# IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF
# MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO
# EVENT SHALL THE COPYRIGHT HOLDERS OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT,
# INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES
# (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES;
# LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND
# ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
# (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
# SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
cmake_minimum_required (VERSION 3.14)
project (MPI)

set (CXX_STANDARD_REQUIRED FALSE)
set (CMAKE_CXX_STANDARD 11)

# setup cache variables for ccmake
set (CMAKE_PREFIX_PATH "" CACHE STRING "External dependencies path")
if (NOT CMAKE_BUILD_TYPE)
    set (CMAKE_BUILD_TYPE RelWithDebInfo CACHE STRING
            "Choose the type of build." FORCE)
    set_property (CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS
            "Debug" "Release" "RelWithDebInfo"
            "MinSizeRel")
endif ()

add_executable (main main.cpp)

set (CMAKE_THREAD_PREFER_PTHREAD TRUE)
set (THREADS_PREFER_PTHREAD_FLAG TRUE)
find_package (Threads REQUIRED)
target_link_libraries (main Threads::Threads)

find_package (MPI MODULE REQUIRED)

foreach (lcv ${MPI_CXX_COMPILE_DEFINITIONS})
    target_compile_definitions (main
            PUBLIC $<BUILD_INTERFACE:${lcv}>)
endforeach ()

foreach (lcv ${MPI_CXX_COMPILE_OPTIONS})
    target_compile_options (main
            PUBLIC $<BUILD_INTERFACE:${lcv}>)
endforeach ()

foreach (lcv ${MPI_CXX_INCLUDE_DIRS})
    target_include_directories (main
            PUBLIC $<BUILD_INTERFACE:${lcv}>)
endforeach ()

foreach (lcv ${MPI_CXX_LIBRARIES})
    target_link_libraries (main $<BUILD_INTERFACE:${lcv}>)
endforeach ()

set_property (TARGET main APPEND PROPERTY LINK_FLAGS
        ${MPI_CXX_LINK_FLAGS})
