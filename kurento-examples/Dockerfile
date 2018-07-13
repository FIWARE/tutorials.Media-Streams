FROM node:carbon

ENV MEDIA_SERVER_HOST=kurento
ENV MEDIA_SERVER_PORT=8888
ENV APP_SERVER_HOST=localhost
ENV APP_SERVER_PORT=8443
ENV TUTORIAL_NAME=hello-world

RUN apt-get update && apt-get install -y \
aufs-tools \
automake \
build-essential \
curl \
git \
python


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


EXPOSE ${APP_SERVER_PORT}
ENTRYPOINT cd kurento-${TUTORIAL_NAME}; npm start -- --ws_uri=ws://${MEDIA_SERVER_HOST}:${MEDIA_SERVER_PORT}/kurento --as_uri=https://${APP_SERVER_HOST}:${APP_SERVER_PORT}/