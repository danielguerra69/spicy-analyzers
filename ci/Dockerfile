# This file is adapted from Spicy upstream's `docker/Dockerfile.ubuntu-20`.
# Fixes here should probably also happen in that file.

FROM ubuntu:focal

# Increase this to force a rebuild of the Docker image (in Cirrus, in particular).
ENV IMAGE_VERSION=2

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

ARG SKIP_BUILD=
ARG ZEEK_LTS=1
ARG ZEEK_VERSION=4.0.3-0

CMD ["sh"]
ENV DEBIAN_FRONTEND=noninteractive
ENV PATH="/opt/spicy/bin:/opt/zeek/bin:${PATH}"

RUN apt-get update \
 && apt-get install -y --no-install-recommends curl ca-certificates gnupg2 less sudo python python3 python3-pip python3-semantic-version python3-git \
 && pip3 install --no-cache-dir pre-commit \
 # Install Zeek.
 && mkdir -p /tmp/zeek-packages \
 && cd /tmp/zeek-packages \
 && if [ -n "${ZEEK_LTS}" ]; then ZEEK_LTS="-lts"; fi && export ZEEK_LTS \
 && apt-get install -y --no-install-recommends libpcap0.8 libpcap-dev libssl-dev zlib1g-dev libmaxminddb0 libmaxminddb-dev \
 && curl -L --remote-name-all \
    https://download.zeek.org/binary-packages/xUbuntu_20.04/amd64/zeek${ZEEK_LTS}_${ZEEK_VERSION}_amd64.deb \
    https://download.zeek.org/binary-packages/xUbuntu_20.04/amd64/zeek${ZEEK_LTS}-core_${ZEEK_VERSION}_amd64.deb \
    https://download.zeek.org/binary-packages/xUbuntu_20.04/amd64/zeekctl${ZEEK_LTS}_${ZEEK_VERSION}_amd64.deb \
    https://download.zeek.org/binary-packages/xUbuntu_20.04/amd64/zeek${ZEEK_LTS}-core-dev_${ZEEK_VERSION}_amd64.deb \
    https://download.zeek.org/binary-packages/xUbuntu_20.04/amd64/libbroker${ZEEK_LTS}-dev_${ZEEK_VERSION}_amd64.deb \
    https://download.zeek.org/binary-packages/xUbuntu_20.04/amd64/zeek${ZEEK_LTS}-libcaf-dev_${ZEEK_VERSION}_amd64.deb \
 && if [[ ${ZEEK_VERSION} = 4.* ]]; then \
        curl -L --remote-name-all \
            https://download.zeek.org/binary-packages/xUbuntu_20.04/amd64/zeek${ZEEK_LTS}-btest_${ZEEK_VERSION}_amd64.deb \
            https://download.zeek.org/binary-packages/xUbuntu_20.04/amd64/zeek${ZEEK_LTS}-zkg_${ZEEK_VERSION}_amd64.deb; \
        if [[ ${ZEEK_VERSION} = 4.1.* ]]; then \
            curl -L --remote-name-all \
                https://download.zeek.org/binary-packages/xUbuntu_20.04/amd64/zeek${ZEEK_LTS}-btest-data_${ZEEK_VERSION}_amd64.deb; \
        fi \
    else \
        pip3 install --no-cache-dir "btest>=0.66" zkg; \
    fi \
 && dpkg -i ./*.deb \
 && cd - \
 && rm -rf /tmp/zeek-packages \
 # Spicy build and test dependencies.
 && apt-get install -y --no-install-recommends git cmake ninja-build ccache bison flex g++ libfl-dev zlib1g-dev jq locales-all make ruby \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/* \
 # Configure zkg.
 && zkg autoconfig \
 && echo "@load packages" >>"$(zeek-config --site_dir)"/local.zeek
