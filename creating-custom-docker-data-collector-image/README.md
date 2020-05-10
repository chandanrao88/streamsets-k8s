Custom StreamSets Data Collector Docker Image Example

This example shows how to build a custom StreamSets Data Collector Docker image pre-loaded with a set of Stage Libraries
and other resources, suitable for Kubernetes-based deployment using StreamSets Control Hub.


1) Create a new directory name as “sdc-docker” at your home directory

cd ~/sdc-docker

2) Set your $PROJECT_HOME

export PROJECT_HOME=$HOME/sdc-docker

3) Create a Docker file and place the below details. SDC_VERSION i have taken 3.15.0. You can mentioned any available versions.

vi Dockerfile

ARG SDC_VERSION=3.15.0
FROM streamsets/datacollector:${SDC_VERSION}
ARG SDC_LIBS
RUN "${SDC_DIST}/bin/streamsets" stagelibs -install="${SDC_LIBS}"
COPY --chown=sdc:sdc resources/ ${SDC_RESOURCES}/
COPY --chown=sdc:sdc sdc-extras/ ${STREAMSETS_LIBRARIES_EXTRA_DIR}/

4) Add additional libraries 

To include external libraries in the image, like the JDBC driver mentioned above, add the file(s) to an sdc-extras directory at the root of the project, nested within the appropriate <stage-lib>/lib subdirectories, like this:

$PROJECT_HOME/sdc-extras/streamsets-datacollector-jdbc-lib/lib/mysql-connector-java-8.0.19.jar

5) Add external resources
Similarly, to include external resources, add them to a resources directory located here:
$PROJECT_HOME/resources/

6) Build the image using the SDC_LIBS arg
Switch to the root of the project and build the image using the SDC_LIBS arg to specify a comma-delimited set of stage libs to include, using a command like this:
docker build \
-t <your org name>/<your repo name> \
--build-arg SDC_LIBS=\
streamsets-datacollector-jdbc-lib,\
streamsets-datacollector-apache-kafka_1_0-lib,\
streamsets-datacollector-elasticsearch_5-lib \
.


On my system, using my own org and repo name, I'll use the command:
bash-3.2$ docker build \
-t chandan091285/my_sdc \
--build-arg SDC_LIBS=\
streamsets-datacollector-jdbc-lib,\
streamsets-datacollector-apache-kafka_1_0-lib,\
streamsets-datacollector-elasticsearch_5-lib \
.


New image after the build completes:

bash-3.2$ docker images
REPOSITORY                     TAG                 IMAGE ID            CREATED             SIZE
chandan/my_sdc                 latest              33be2ef44dde        12 hours ago        782MB
chandan091285/streamset-sdc    my_sdc              33be2ef44dde        12 hours ago        782MB
chandan091285/streamsets-sdc   my_sdc              33be2ef44dde        12 hours ago        782MB
streamsets/datacollector       3.15.0              6fa33c7645b7        3 weeks ago         661MB


7) Push the new image to your Docker Hub

Use a $ docker login command if you have not yet logged into your Docker Hub account.

Create a tag using the image

docker tag chandan/my_sdc:latest chandan091285/streamset-sdc:my_sdc

Push the docker Image

docker push chandan091285/streamsets-sdc:my_sdc
