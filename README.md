# Dockerized GeoTopicParser-Enabled Apache Tika Server

Container-ized [Lucene Geo-Gazetteer](https://github.com/chrismattmann/lucene-geo-gazetteer) and [GeoTopicParser](https://cwiki.apache.org/confluence/display/tika/GeoTopicParser)-Enabled [Apache Tika](https://tika.apache.org/) Server.

## Pull and Run
I recommend this method -- the build takes a minute. This installs the latest stable version of Apache Tika, version 1.25.
### Pull
```
docker pull frytoli/gtp-tika:latest
```

### Run
Run with just the Tika Server accessible by host:
```
docker run -d -p 127.0.0.1:9998:9998 frytoli/gtp-tika:latest
```

Run with both the Tika Server and Lucene Geo-Gazetteer Server accessible by host:
```
docker run -d -p 127.0.0.1:9998:9998 -p 127.0.0.1:8765:8765 frytoli/gtp-tika:latest
```

## Build and Run
### Build
Build with the default version 1.25 of the Apache Tika Server:
```
docker build . --build-arg --tag gtp-tika
```

There is an optional build argument "tika_version" that defaults to "1.25". This can be changed to specify the desired version of the Apache Tika Server.
```
docker build . --build-arg "tika_version=1.22" --tag gtp-tika
```

### Run
Run with just the Tika Server accessible by host:
```
docker run -d -p 127.0.0.1:9998:9998 gtp-tika
```

Run with both the Tika Server and Lucene Geo-Gazetteer Server accessible by host:
```
docker run -d -p 127.0.0.1:9998:9998 -p 127.0.0.1:8765:8765 gtp-tika
```

## Test
Test that the GeoTopicParser is working as expected by parsing the provided "polar.geot" file (from the [GeoTopicParser-Utils repo](https://github.com/chrismattmann/geotopicparser-utils)):
```
curl -T polar.geot -H "Content-Disposition: attachment; filename=polar.geot" http://localhost:9998/rmeta
```

Expected output:
```
[
   {
      "Content-Type":"application/geotopic",
      "Geographic_LATITUDE":"39.76",
      "Geographic_LONGITUDE":"-98.5",
      "Geographic_NAME":"United States",
      "Optional_LATITUDE1":"27.33931",
      "Optional_LONGITUDE1":"-108.60288",
      "Optional_NAME1":"China",
      "X-Parsed-By":[
         "org.apache.tika.parser.DefaultParser",
         "org.apache.tika.parser.geo.topic.GeoParser"
      ],
      "X-TIKA:parse_time_millis":"1634",
      "resourceName":"polar.geot"
   }
]
```
