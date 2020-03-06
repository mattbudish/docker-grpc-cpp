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

# This is a multi-stage build. This file creates intermediate images and
# discards some intermediate artifacts to produce an overall smaller image.

ARG DISTRO_VERSION=3.11.3
FROM alpine:${DISTRO_VERSION} AS devtools

LABEL maintainer="Matt Budish <mtbudish@gmail.com"

RUN apk update && apk upgrade && \
    apk --no-cache add \
        boost-dev \
        boost-static \
        build-base \
        clang \
        cmake \
        curl \
        gcc \
        git \
        g++ \
        libc-dev \
        ninja \
        perl \
        tar \
        unzip

FROM devtools AS with_vcpkg

WORKDIR /opt/tools/

RUN  git clone https://github.com/Microsoft/vcpkg.git && \
    cd vcpkg && \
    ./bootstrap-vcpkg.sh --useSystemBinaries && \
    ./vcpkg install --triplet x64-linux grpc

FROM devtools

WORKDIR /opt/tools

RUN git clone https://github.com/Microsoft/vcpkg.git

COPY --from=with_vcpkg /opt/tools/vcpkg/installed /opt/tools/vcpkg/installed
COPY --from=with_vcpkg /opt/tools/vcpkg/vcpkg /opt/tools/vcpkg

CMD ["/opt/tools/vcpkg/vcpkg", "list"]