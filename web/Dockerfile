# mds-web: REST API + OAI-PMH service on Tomcat

FROM tutum/tomcat

MAINTAINER Richard Rodgers <rrodgers@mit.edu> <orcid:0000-0003-1412-5595>

ENV MDS_SOURCE /mds/source
ENV MDS_STAGE /mds/stage
ENV MDS_SITE_HOME /mds/rt

# using source distribution: obtain build tools. Also need tools to initialize DB in init script
RUN apt-get update && apt-get install -y git maven postgresql-client

# obtain source and build selected modules
RUN git clone https://github.com/richardrodgers/mds.git ${MDS_SOURCE}
RUN cd ${MDS_SOURCE}/mdsa; mvn install; cd ../kernel; mvn install; cd ../admin; mvn package; cd ../cts-gen; mvn package; cd ../oai-pmh; mvn package; cd ../webapi; mvn package

# install built code modules into staging area
RUN mkdir ${MDS_STAGE}; cd ${MDS_STAGE}; cp ${MDS_SOURCE}/kernel/target/*.zip .; unzip *.zip; chmod +x ${MDS_STAGE}/bin/*; mkdir ${MDS_STAGE}/modules; \
mkdir ${MDS_STAGE}/modules/admin; cd ${MDS_STAGE}/modules/admin; cp ${MDS_SOURCE}/admin/target/*.zip .; unzip *.zip; \
mkdir ${MDS_STAGE}/modules/cts-gen; cd ${MDS_STAGE}/modules/cts-gen; cp ${MDS_SOURCE}/cts-gen/target/*.zip .; unzip *.zip; \
mkdir ${MDS_STAGE}/modules/oai-pmh; cd ${MDS_STAGE}/modules/oai-pmh; cp ${MDS_SOURCE}/oai-pmh/target/*.zip .; unzip *.zip; \
mkdir ${MDS_STAGE}/modules/webapi; cd ${MDS_STAGE}/modules/webapi; cp ${MDS_SOURCE}/webapi/target/*.zip .; unzip *.zip

# stage code to site home
RUN mkdir ${MDS_SITE_HOME}; cd ${MDS_STAGE}/bin; ./dspace stage kernel; ./dspace stage admin; ./dspace stage cts-gen; ./dspace stage oai-pmh; ./dspace stage webapi

# deploy built wars to Tomcat
RUN cp ${MDS_STAGE}/webapps/deploy/*.war ${CATALINA_HOME}/webapps

# fire up Tomcat
CMD ["/run.sh"]
