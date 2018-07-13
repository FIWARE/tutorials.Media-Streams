FROM ubuntu:16.04

ENV KURENTO_HOST=kurento
ENV KURENTO_PORT=8888
ENV WEB_APP_HOST=localhost
ENV WEB_APP_PORT=8443
ENV TUTORIAL_NAME=hello-world

RUN apt-get update && apt-get install -y \
aufs-tools \
automake \
build-essential \
curl \
git \
python
RUN curl -sL https://deb.nodesource.com/setup_4.x | bash - \
&& apt-get install -y nodejs \
&& rm -rf /var/lib/apt/lists/*

RUN echo '{ "allow_root": true }' > /root/.bowerrc

# Bundle app source
COPY ./nodejs kurento-tutorial-node



WORKDIR kurento-tutorial-node

RUN npm install -g bower && \
  cd kurento-chroma && \
  git init && \
  npm install --unsafe-perm && \
  cd ../kurento-crowddetector && \
  git init && \
  npm install --unsafe-perm && \
  cd ../kurento-hello-world && \
  git init && \
  npm install --unsafe-perm && \
  cd ../kurento-magic-mirror && \
  git init && \
  npm install --unsafe-perm && \
  cd ../kurento-module-tests-api && \
  git init && \
  npm install --unsafe-perm && \
  cd ../kurento-one2many-call && \
  git init && \
  npm install --unsafe-perm && \
  cd ../kurento-one2one-call && \
  git init && \
  npm install --unsafe-perm && \
  cd ../kurento-platedetector && \
  git init && \
  npm install --unsafe-perm && \
  cd ../kurento-pointerdetector && \
  git init && \
  npm install --unsafe-perm


EXPOSE ${WEB_APP_PORT}
ENTRYPOINT cd kurento-${TUTORIAL_NAME}; npm start -- --ws_uri=ws://${KURENTO_HOST}:${KURENTO_PORT}/kurento --as_uri=https://${WEB_APP_HOST}:${WEB_APP_PORT}/