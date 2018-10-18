FROM fiware/stream-oriented-kurento:6.8.0



RUN	echo "deb http://ubuntu.kurento.org/ trusty kms6" | tee /etc/apt/sources.list.d/kurento.list \
	&& wget -O - http://ubuntu.kurento.org/kurento.gpg.key | apt-key add - \
	&& apt-get update \
    && apt-get -y install kms-pointerdetector-6.0 \
    && apt-get -y install kms-crowddetector-6.0 \
    && apt-get -y install kms-platedetector-6.0 \
	&& apt-get -y install kms-chroma-6.0 \
	&& apt-get clean \
    && rm -rf /var/lib/apt/lists/*



ENTRYPOINT ["/entrypoint.sh"]