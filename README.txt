TAL Engines have been implemented as Karaf (4.0.8) OSGi Blueprint ([9]). Before, however, hot deploying them by copying the binaries into the dedicated ({karaf home}/deploy) folder a number of features and other bundles on which the implementation is based must be first installed and/or activated on a running Karaf instance through the Karaf console: 
An environment variable - JAVA_HOME - should be defined to indicate the installation of a JDK-1.8 installation.
Apache CXF repository should be added and a number of cxf features should be activated 
feature:repo-add cxf 3.1.9
feature:install cxf cxf-rs-description-swagger2 hibernate
A number of bundles, easily located in the local maven repository, should be installed and started
bundle:install http://central.maven.org/maven2/com/fasterxml/jackson/core/jackson-annotations/2.8.4/jackson-annotations-2.8.4.jar
bundle:install http://central.maven.org/maven2/com/fasterxml/jackson/core/jackson-core/2.8.4/jackson-core-2.8.4.jar
bundle:install http://central.maven.org/maven2/com/fasterxml/jackson/core/jackson-databind/2.8.4/jackson-databind-2.8.4.jar
bundle:install http://central.maven.org/maven2/org/mongodb/bson/3.4.0/bson-3.4.0.jar
bundle:install http://central.maven.org/maven2/org/mongodb/mongo-java-driver/3.4.0/mongo-java-driver-3.4.0.jar
bundle:install http://central.maven.org/maven2/com/rabbitmq/amqp-client/4.0.2/amqp-client-4.0.2.jar
bundle:install http://central.maven.org/maven2/org/apache/commons/commons-compress/1.12/commons-compress-1.12.jar
bundle:install http://central.maven.org/maven2/org/mongodb/morphia/morphia/1.3.2/morphia-1.3.2.jar


There are a few bundles that are not installed through the maven repositories but they need to be prepared (manifest modification) to be compliant with OSGi: 

install file:///{SELFNET DEV}/commons-io-1.3.2.jar
install file:///{SELFNET DEV}/proxytoys-0.2.jar

At this point the list of running bundles in Karaf looks like:
START LEVEL 100 , List Threshold: 50
 ID | State  | Lvl | Version             | Name
----------------------------------------------------------------------------------------------------------------------
 52 | Active |  80 | 1.1.0               | ClassMate
 68 | Active |  80 | 2.1.0.v201304241213 | Java Persistence API 2.1
117 | Active |  80 | 1.1.1               | geronimo-jms_1.1_spec
121 | Active |  80 | 2.0.13              | Apache MINA Core
124 | Active |  80 | 1.8.2.2             | Apache ServiceMix :: Bundles :: ant
125 | Active |  80 | 2.7.7.5             | Apache ServiceMix :: Bundles :: antlr
126 | Active |  80 | 1.6.1.5             | Apache ServiceMix :: Bundles :: dom4j
174 | Active |  80 | 4.0.4.Final         | hibernate-commons-annotations
175 | Active |  80 | 4.3.6.Final         | hibernate-core
176 | Active |  80 | 4.3.6.Final         | hibernate-entitymanager
177 | Active |  80 | 4.3.6.Final         | hibernate-osgi
178 | Active |  80 | 1.2.2.Final         | Java Annotation Indexer
179 | Active |  80 | 3.1.4.GA            | JBoss Logging 3
180 | Active |  80 | 1.0.2.Final         | JACC 1.4 API
191 | Active |  80 | 2.8.4               | Jackson-annotations
192 | Active |  80 | 2.8.4               | Jackson-core
193 | Active |  80 | 2.8.4               | jackson-databind
194 | Active |  80 | 3.4.0               | bson
195 | Active |  80 | 4.0.2               | RabbitMQ Java Client
196 | Active |  80 | 1.12.0              | Apache Commons Compress
197 | Active |  80 | 1.3.2               | Morphia Core
198 | Active |  80 | 1.3.2               | org.apache.commons-io
199 | Active |  80 | 1.0                 | com.thoughtworks.proxy
202 | Active |  80 | 3.4.0               | mongo-java-driver

The installation of the above set of bundles resolves all the dependencies for the TAL Engines implementation. 
TAL ENgines persist information on a MongoDB document database. Therefore a MongoDB daemon should be launched with a specific storage folder:
mongodb-linux-x86_64-3.2.10/bin/mongod  --dbpath sdno_mongo

TAL Engines are further configured by two cfg files located in /opt/{apache karaf installation/etc/

The core part is configured by the file TALCORE.cfg with the following contents:

TAL_ENGINE_WP4 = 127.0.0.1
TAL_ENGINE_WP5 = 127.0.0.1
KAFKA_BUS      = 192.168.89.226
KAFKA_TOPIC    = monasca-notifications
REPUBLISH_TACTICS    = false
PUBLISH_TACTICS    = true
CACHE_TACTICS    = false
BYPASSANALYSER    = false
SENT_DIRECTLY_TO_SO    = false
AE_CALLBACK = http://192.168.89.128:18183/tal/engine/callback
SO_IP = http://192.168.89.129:9004
SO_PATH = /ptinovacao/serviceOrderingManagement/serviceOrder/

The TAL connector part is configured by the file TALENGINE.cfg with the following comments:

TAL_ENGINE_WP4 = 127.0.0.1
TAL_ENGINE_WP5 = 127.0.0.1
KAFKA_BUS      = 192.168.89.226
KAFKA_TOPIC    = monasca-notifications
KAFKA_CEP_TOPIC    = cep_output



