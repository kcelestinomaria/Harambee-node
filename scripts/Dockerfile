# building the Harambee Blockchain node
FROM paritytech/ci-linux:production as builder


LABEL maintainer="Harambee-dev"
ARG RUST_VERSION=nightly-2020-10-01
ARG PROFILE=release
ARG GIT_REPO="https://github.com/kcelestinomaria/Harambee-node"
ARG GIT_BRANCH="develop"


RUN git clone --recursive ${GIT_REPO}
WORKDIR /builds/harambee-node
RUN sed -i s/localhost/Harambee-data-proxy/g /builds/Harambee-node/pallets/Harambee-ocw/src/lib.rs
RUN sed -i s/localhost/Harambee-node/g /builds/Harambee-node/scripts/insert_alice_key.sh
RUN rustup default stable
RUN rustup uninstall nightly
RUN rustup toolchain install ${RUST_VERSION}
RUN rustup target add wasm32-unknown-unknown --toolchain ${RUST_VERSION}

RUN cargo +${RUST_VERSION} build --${PROFILE}
RUN cp target/${PROFILE}/Harambee-node /Harambee-node

# build sample data fetcher
FROM paritytech/ci-linux:production as data_fetcher_builder
ARG DATA_FETECHER_REPO="https://github.com/Harambee-Network/sample-data-fetcher.git"
ARG DATA_FETECHER_BRANCH="master"
ARG PROFILE="debug"

RUN git clone --recursive ${DATA_FETECHER_REPO}
WORKDIR /builds/sample-data-fetcher
RUN cargo build
RUN cp target/${PROFILE}/data_fetcher /data_fetcher

# build Harambee-frontend
FROM paritytech/ci-linux:production as frontend_builder

LABEL maintainer="Harambee-dev"
ARG FRONT_REPO="https://github.com/kcelestinomaria/Harambee-frontend.git"
ARG FRONT_BRANCH="main"

RUN git clone --recursive ${FRONT_REPO}
WORKDIR /builds/Harambee-frontend
RUN git checkout 0e69f019

RUN apt update && apt install gnupg2 curl ca-certificates -y
RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - && echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list
RUN apt update && apt install yarn -y
RUN YARN_CHECKSUM_BEHAVIOR=update yarn && yarn build:www

FROM ubuntu
ENV DEBIAN_FRONTEND noninteractive
RUN apt-get update && apt-get install curl ca-certificates npm nodejs -y
RUN npm install -g serve
COPY --from=builder /Harambee-node /
COPY --from=builder /builds/Harambee-node/scripts/insert_alice_key.sh /

COPY --from=data_fetcher_builder /builds/sample-data-fetcher/target/debug/data_fetcher /data_fetcher
COPY --from=data_fetcher_builder /builds/sample-data-fetcher/data/es_index.json /es_index.json
COPY --from=data_fetcher_builder /builds/sample-data-fetcher/data/kibana.ndjson /kibana.ndjson
COPY --from=data_fetcher_builder /builds/sample-data-fetcher/data/init_es_index.sh /init_es_index.sh
COPY --from=data_fetcher_builder /builds/sample-data-fetcher/data/init_kibana_dashboard.sh /init_kibana_dashboard.sh

COPY --from=frontend_builder /builds/Harambee-frontend/packages/apps /apps

EXPOSE 30333 9933 9944 8080 3001