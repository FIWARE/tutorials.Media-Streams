FROM kurento/kurento-media-server:6.15.0

RUN	echo "deb http://ubuntu.openvidu.io/6.15.0 xenial kms6" | tee /etc/apt/sources.list.d/kurento.list \
	&& apt-get update \
    && apt-get -y install kms-pointerdetector \
    && apt-get -y install kms-crowddetector \
    && apt-get -y install kms-platedetector \
	&& apt-get -y install kms-chroma \
	&& apt-get clean \
    && rm -rf /var/lib/apt/lists/*


ENTRYPOINT ["/entrypoint.sh"]