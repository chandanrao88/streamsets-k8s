{\rtf1\ansi\ansicpg1252\cocoartf2512
\cocoatextscaling0\cocoaplatform0{\fonttbl\f0\fswiss\fcharset0 Arial-BoldMT;\f1\fswiss\fcharset0 ArialMT;}
{\colortbl;\red255\green255\blue255;\red25\green60\blue255;\red255\green255\blue255;\red28\green31\blue35;
\red255\green255\blue255;\red27\green31\blue35;\red0\green0\blue0;\red255\green255\blue255;\red27\green31\blue35;
\red255\green255\blue11;\red27\green31\blue35;\red19\green94\blue207;}
{\*\expandedcolortbl;;\cssrgb\c12594\c35385\c100000;\cssrgb\c100000\c100000\c99971\c0;\cssrgb\c14325\c16307\c18144;
\cssrgb\c100000\c100000\c99985\c0;\cssrgb\c14296\c16274\c18129;\cssrgb\c0\c0\c0;\cssrgb\c100000\c100000\c100000\c0;\cssrgb\c14237\c16209\c18099;
\cssrgb\c100000\c100000\c0;\cssrgb\c14266\c16242\c18114;\cssrgb\c6913\c45986\c84718;}
\paperw11900\paperh16840\margl1440\margr1440\vieww26400\viewh13940\viewkind0
\deftab720
\pard\pardeftab720\partightenfactor0

\f0\b\fs48 \cf2 \cb3 \expnd0\expndtw0\kerning0
Custom StreamSets Data Collector Docker Image Exampl\cf2 \cb5 e
\f1\b0\fs36 \cf7 \cb8 \
\
This example shows how to build a custom StreamSets Data Collector Docker image pre-loaded with a set of Stage Libraries\
and other resources, suitable for Kubernetes-based deployment using StreamSets Control Hub.\kerning1\expnd0\expndtw0 \
\pard\tx566\tx1133\tx1700\tx2267\tx2834\tx3401\tx3968\tx4535\tx5102\tx5669\tx6236\tx6803\pardirnatural\partightenfactor0
\cf7 \
\
1) Create a new directory name as \'93sdc-docker\'94 at your home directory\
\
\cf10 \cb5 cd ~/sdc-docker\cf7 \cb8 \
\
2) Set your $PROJECT_HOME\
\
export PROJECT_HOME=$HOME/sdc-docker\
\
3) Create a Docker file and place the below details. SDC_VERSION i have taken 3.15.0. You can mentioned any available versions.\
\
vi Dockerfile\
\
\pard\tx560\tx1120\tx1680\tx2240\tx2800\tx3360\tx3920\tx4480\tx5040\tx5600\tx6160\tx6720\pardirnatural\partightenfactor0
\cf10 \cb5 \CocoaLigature0 ARG SDC_VERSION=3.15.0\
FROM streamsets/datacollector:$\{SDC_VERSION\}\
ARG SDC_LIBS\
RUN "$\{SDC_DIST\}/bin/streamsets" stagelibs -install="$\{SDC_LIBS\}"\
COPY --chown=sdc:sdc resources/ $\{SDC_RESOURCES\}/\
COPY --chown=sdc:sdc sdc-extras/ $\{STREAMSETS_LIBRARIES_EXTRA_DIR\}/\cf7 \cb8 \
\
4) Add additional libraries \
\
\pard\pardeftab720\partightenfactor0
\cf7 \expnd0\expndtw0\kerning0
\CocoaLigature1 To include external libraries in the image, like the JDBC driver mentioned above, add the file(s) to an sdc-extras directory at the root of the project, nested within the appropriate <stage-lib>/lib subdirectories, like this:\
\
\cf10 \cb5 $PROJECT_HOME/sdc-extras/streamsets-datacollector-jdbc-lib/lib/\cb5 \kerning1\expnd0\expndtw0 \CocoaLigature0 mysql-connector-java-8.0.19.jar\cf7 \cb8 \expnd0\expndtw0\kerning0
\CocoaLigature1 \
\pard\pardeftab720\partightenfactor0
\cf7 \
\pard\pardeftab720\partightenfactor0
\cf7 5) Add external resources\
Similarly, to include external resources, add them to a resources directory located here:\
\cf10 \cb5 $PROJECT_HOME/resources/\cf7 \cb8 \
\pard\pardeftab720\partightenfactor0
\cf7 \
\pard\pardeftab720\partightenfactor0
\cf7 6) Build the image using the SDC_LIBS arg\
Switch to the root of the project and build the image using the SDC_LIBS arg to specify a comma-delimited set of stage libs to include, using a command like this:\
docker build \\\
-t <your org name>/<your repo name> \\\
--build-arg SDC_LIBS=\\\
streamsets-datacollector-jdbc-lib,\\\
streamsets-datacollector-apache-kafka_1_0-lib,\\\
streamsets-datacollector-elasticsearch_5-lib \\\
.\
\
\
On my system, using my own org and repo name, I'll use the command:\
\pard\tx560\tx1120\tx1680\tx2240\tx2800\tx3360\tx3920\tx4480\tx5040\tx5600\tx6160\tx6720\pardirnatural\partightenfactor0
\cf10 \cb5 \kerning1\expnd0\expndtw0 \CocoaLigature0 bash-3.2$ \cb5 \expnd0\expndtw0\kerning0
\CocoaLigature1 docker build \\\
\pard\pardeftab720\partightenfactor0
\cf10 -t chandan091285/my_sdc \\\
--build-arg SDC_LIBS=\\\
streamsets-datacollector-jdbc-lib,\\\
streamsets-datacollector-apache-kafka_1_0-lib,\\\
streamsets-datacollector-elasticsearch_5-lib \\\
.\cf7 \cb8 \
\
\
New image after the build completes:\
\
\pard\tx560\tx1120\tx1680\tx2240\tx2800\tx3360\tx3920\tx4480\tx5040\tx5600\tx6160\tx6720\pardirnatural\partightenfactor0
\cf7 \kerning1\expnd0\expndtw0 \CocoaLigature0 bash-3.2$ docker images\
REPOSITORY                     TAG                 IMAGE ID            CREATED             SIZE\
\cf10 \cb5 chandan/my_sdc                 latest              33be2ef44dde        12 hours ago        782MB\cf7 \cb8 \
chandan091285/streamset-sdc    my_sdc              33be2ef44dde        12 hours ago        782MB\
chandan091285/streamsets-sdc   my_sdc              33be2ef44dde        12 hours ago        782MB\
streamsets/datacollector       3.15.0              6fa33c7645b7        3 weeks ago         661MB\
\pard\pardeftab720\partightenfactor0
\cf7 \expnd0\expndtw0\kerning0
\CocoaLigature1 \
\
7) Push the new image to your Docker Hub\
\
Use a $ docker login command if you have not yet logged into your Docker Hub account.\
\
Create a tag using the image\
\
\pard\tx560\tx1120\tx1680\tx2240\tx2800\tx3360\tx3920\tx4480\tx5040\tx5600\tx6160\tx6720\pardirnatural\partightenfactor0
\cf10 \cb5 \kerning1\expnd0\expndtw0 \CocoaLigature0 docker tag chandan/my_sdc:latest chandan091285/streamset-sdc:my_sdc\cf7 \cb8 \
\
Push the docker Image\
\
\cf10 \cb5 docker push chandan091285/streamsets-sdc:my_sdc}