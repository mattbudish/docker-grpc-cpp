# Copyright 2019 Carlos O'Ryan
#
# Licensed under the Apache License, Version 2.0 (the "License"); you may not
# use this file except in compliance with the License. You may obtain a copy of
# the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
# WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the
# License for the specific language governing permissions and limitations under
# the License.
#
# Modifications copyright 2020 Matt Budish

ARG TOOLS_VERSION=latest
ARG ALPINE_VERSION=3.11.3
FROM mattbudish/grpc-cpp-devtools:${TOOLS_VERSION} AS build
LABEL maintainer="Matt Budish <mtbudish@gmail.com>"

# Copy the project source code into the intermediate 'build' image.
COPY . /opt/code/

# Configure the project using CMake. Some of the options are unusual:
#   -GNinja: use Ninja as the backend as this is often faster than Make.
#   -CMAKE_TOOLCHAIN_FILE=...: use the packages installed via vcpkg.
#   -DBoost_USE_STATIC_LIBS=ON: prefer static over shared libraries for Boost.
#   -DCMAKE_EXE_LINKER_FLAGS=...: create statically linked binaries.
WORKDIR /opt/code/
RUN cmake -H. -B.build \
    -GNinja \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_TOOLCHAIN_FILE=/opt/tools/vcpkg/scripts/buildsystems/vcpkg.cmake \
    -DBoost_USE_STATIC_LIBS=ON \
    -DCMAKE_EXE_LINKER_FLAGS="-static"

# Compile the project
RUN cmake --build .build
RUN strip .build/examples/echo/echo_server .build/examples/echo/echo_client

FROM alpine:${ALPINE_VERSION}
LABEL maintainer="Matt Budish <mtbudish@gmail.com>"

COPY --from=build \
    /opt/code/.build/examples/echo/echo_server \
    /opt/code/.build/examples/echo/echo_client \
    /opt/app/

EXPOSE 7000
CMD ["/opt/app/echo_server"]
