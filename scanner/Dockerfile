FROM adoptopenjdk/openjdk11:ubuntu-jre

ARG SONAR_SCANNER_HOME=/opt/sonar-scanner
ARG SONAR_SCANNER_VERSION=4.6.2.2472
ARG UID=1000
ARG GID=1000
ENV JAVA_HOME=/opt/java/openjdk \
    HOME=/sonarqube-scanner \
    XDG_CONFIG_HOME=/sonarqube-scanner \
    SONAR_SCANNER_HOME=${SONAR_SCANNER_HOME} \
    SONAR_USER_HOME=${SONAR_SCANNER_HOME}/.sonar \
    PATH=/opt/java/openjdk/bin:${SONAR_SCANNER_HOME}/bin:${PATH} \
    NODE_PATH=/usr/lib/node_modules \
    SRC_PATH=/usr/src

## Install dart SDK
RUN apt-get update -y && apt-get install apt-transport-https curl wget git unzip -y

## install nodejs and python
RUN curl -fsSL https://deb.nodesource.com/setup_12.x | bash -
RUN apt-get install -y nodejs

ENV FLUTTER_HOME=${HOME}/sdks/flutter \
    FLUTTER_VERSION=2.0.3
ENV FLUTTER_ROOT=$FLUTTER_HOME

ENV PATH ${PATH}:${FLUTTER_HOME}/bin:${FLUTTER_HOME}/bin/cache/dart-sdk/bin

RUN git clone --depth 1 --branch ${FLUTTER_VERSION} https://github.com/flutter/flutter.git ${FLUTTER_HOME}

RUN yes | flutter doctor --android-licenses \
    && flutter doctor

WORKDIR /opt

RUN set -ex \
    && wget -q -O /opt/sonar-scanner-cli.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-${SONAR_SCANNER_VERSION}-linux.zip \
    && unzip sonar-scanner-cli.zip \
    && rm sonar-scanner-cli.zip \
    && mv sonar-scanner-${SONAR_SCANNER_VERSION}-linux ${SONAR_SCANNER_HOME} \
    && npm install -g typescript@3.7.5 \
    && mkdir -p "${SRC_PATH}" "${SONAR_USER_HOME}" "${SONAR_USER_HOME}/cache"\
    && chmod -R 777 "${SRC_PATH}" "${SONAR_USER_HOME}"

COPY entrypoint.sh /usr/bin/entrypoint.sh

WORKDIR ${SRC_PATH}

ENTRYPOINT ["/usr/bin/entrypoint.sh"]

CMD ["sonar-scanner"]