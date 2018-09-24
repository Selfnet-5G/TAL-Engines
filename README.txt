The App Manager has been implemented as a Karaf (4.0.8) OSGi Blueprint ([9]). Before, however, hot deploying it by copying the binaries into the dedicated ({karaf home}/deploy) folder a number of features and other bundles on which the implementation is based must be first installed and/or activated on a running Karaf instance through the Karaf console: 
An environment variable - JAVA_HOME - should be defined to indicate the installation of a JDK-1.8 installation.
Apache CXF repository should be added and a number of cxf features should be activated 
feature:repo-add cxf 3.1.9
feature:install cxf cxf-rs-description-swagger2 hibernate
Additional features:
feature:repo-add pax-jdbc
feature:install pax-jdbc-mysql
A number of bundles, easily located in the local maven repository, should be installed and started
bundle:install http://central.maven.org/maven2/com/fasterxml/jackson/core/jackson-annotations/2.8.4/jackson-annotations-2.8.4.jar
bundle:install http://central.maven.org/maven2/com/fasterxml/jackson/core/jackson-core/2.8.4/jackson-core-2.8.4.jar
bundle:install http://central.maven.org/maven2/com/fasterxml/jackson/core/jackson-databind/2.8.4/jackson-databind-2.8.4.jar
bundle:install http://central.maven.org/maven2/org/mongodb/bson/3.4.0/bson-3.4.0.jar
bundle:install http://central.maven.org/maven2/org/mongodb/mongo-java-driver/3.4.0/mongo-java-driver-3.4.0.jar
bundle:install http://central.maven.org/maven2/com/rabbitmq/amqp-client/4.0.2/amqp-client-4.0.2.jar
bundle:install http://central.maven.org/maven2/org/apache/commons/commons-compress/1.12/commons-compress-1.12.jar
bundle:install http://central.maven.org/maven2/org/mongodb/morphia/morphia/1.3.2/morphia-1.3.2.jar
bundle:install wrap:http://central.maven.org/maven2/com/jcraft/jsch/0.1.54/jsch-0.1.54.jar
bundle:install http://central.maven.org/maven2/mysql/mysql-connector-java/6.0.6/mysql-connector-java-6.0.6.jar
There are a few bundles that are not installed through the maven repositories but they need to be prepared (manifest modification) to be compliant with OSGi: 
install file:///{SELFNET DEV}/docker-client-8.1.2-jar-with-dependencies.jar
install file:///{SELFNET DEV}/commons-io-1.3.2.jar
install file:///{SELFNET DEV}/proxytoys-0.2.jar
install file:///{SELFNET DEV}/onboarding-1.0-SNAPSHOT.jar
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
200 | Active |  80 | 8.1.2               | docker-client
202 | Active |  80 | 3.4.0               | mongo-java-driver
212 | Active |  80 | 0                   | wrap_http___central.maven.org_maven2_com_jcraft_jsch_0.1.54_jsch-0.1.54.jar
219 | Active |  80 | 6.0.6               | Oracle Corporation's JDBC and XDevAPI Driver for MySQL
221 | Active |  80 | 1.0.0.SNAPSHOT      | AppCatalogueModel
The installation of the above set of bundles resolves all the dependencies for the App Manager implementation. 
App Manager persists information on a MongoDB document database. Therefore a MongoDB daemon should be launched with a specific storage folder:
mongodb-linux-x86_64-3.2.10/bin/mongod  --dbpath sdno_mongo
A Docker daemon should be also up and running for the management of the docker images and containers (a dedicated folder is used for the images and containers metadata):
dockerd -d ./data
App Manager is using a specific docker image that is activated every time and SDN APP application implementation has to be instantiated.
For the preparation of such an image a temporary folder should be created and inside it a file with name Dokcerfile must be present. The contents should be:
FROM ubuntu:latest
RUN apt-get -y update
RUN apt-get install -y python3
RUN apt-get install -y python3-requests
RUN apt-get install -y net-tools
RUN apt-get install -y iputils-ping
ADD jre1.8.0_121  /opt/jre1.8.0_121
ENV PATH=$PATH:/opt/jre1.8.0_121/bin
ENV JAVA_HOME=/opt/jre1.8.0_121/
CMD /bin/bash
Java is installed by extracting the indicated jre as obtained from Oracle distribution server. To instruct docker create the image the following command should be used:
docker build -t sdnapp .
“sdnapp" is the name of the image to be created.
The WP3 App Catalogue service should be also up and running so that the Rabbit MQ to which the App Manager registers for events has been initialised. App Manager can thereafter be installed on karaf by copying the jar file in the deploy folder of karaf as already mentioned:
220 | Active |  80 | 1.0  | com.cse.sdn.app.manager Blueprint Bundle
Using a browser the App Manager endpoint is listed by the Karaf internal Jetty engine
