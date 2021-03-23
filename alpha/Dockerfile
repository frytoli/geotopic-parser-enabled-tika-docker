FROM maven:3-jdk-8

ENV TIKA_VERSION=$tika_version

# Update, install requirements
RUN apt-get update && \
    apt-get install -y \
        git \
        supervisor \
        unzip \
        wget

# Clone and build Lucene Geo-Gazetteer repo
RUN git clone --depth=1 https://github.com/chrismattmann/lucene-geo-gazetteer.git && \
    mvn -f /lucene-geo-gazetteer install assembly:assembly

# Download and extract allCountries.zip
RUN wget http://download.geonames.org/export/dump/allCountries.zip && \
    unzip allCountries.zip

# Create a geoIndex with allCountries
RUN /lucene-geo-gazetteer/src/main/bin/lucene-geo-gazetteer -i geoIndex -b allCountries.txt

# Download Apache Tika Server v 2.0.0
RUN mkdir /TIKA && \
    wget https://downloads.apache.org/tika/tika-server-classic-2.0.0-ALPHA.jar -P /TIKA/

# Download NER model
RUN mkdir -p /TIKA/ner-model/org/apache/tika/parser/geo/topic && \
    wget http://opennlp.sourceforge.net/models-1.5/en-ner-location.bin -P /TIKA/ner-model/org/apache/tika/parser/geo/topic/

# Download GeoTopicParser
RUN mkdir -p /TIKA/geotopic-mime/org/apache/tika/mime && \
    wget https://raw.githubusercontent.com/chrismattmann/geotopicparser-utils/master/mime/org/apache/tika/mime/custom-mimetypes.xml -P /TIKA/geotopic-mime/org/apache/tika/mime/

# Expose the ports
EXPOSE 8765
EXPOSE 9998

# Add supervisord config files
ADD conf/supervise-* /etc/supervisor/conf.d/

# Start supervisord
CMD /usr/bin/supervisord -n
