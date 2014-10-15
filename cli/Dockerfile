# mds-cli: Command-line interface

FROM dockerfile/java:oracle-java7

MAINTAINER Richard Rodgers <rrodgers@mit.edu> <orcid:0000-0003-1412-5595>

ENV MDS_SOURCE /mds/source
ENV MDS_STAGE /mds/stage
#ENV MDS_RUNTIME /mds/rt
ENV MDS_SITE_HOME /mds/rt

# using source distribution: obtain build tools
RUN apt-get update && apt-get install -y git maven

# obtain source and build selected modules
RUN git clone https://github.com/richardrodgers/mds.git ${MDS_SOURCE}
RUN cd ${MDS_SOURCE}/mdsa; mvn install; cd ../kernel; mvn install; cd ../admin; mvn package; cd ../cts-gen; mvn package

# install built code modules into staging area
RUN mkdir ${MDS_STAGE}; cd ${MDS_STAGE}; cp ${MDS_SOURCE}/kernel/target/*.zip .; unzip *.zip; chmod +x ${MDS_STAGE}/bin/*; mkdir ${MDS_STAGE}/modules; \
mkdir ${MDS_STAGE}/modules/admin; cd ${MDS_STAGE}/modules/admin; cp ${MDS_SOURCE}/admin/target/*.zip .; unzip *.zip; \
mkdir ${MDS_STAGE}/modules/cts-gen; cd ${MDS_STAGE}/modules/cts-gen; cp ${MDS_SOURCE}/cts-gen/target/*.zip .; unzip *.zip

# stage code to site home
RUN mkdir ${MDS_SITE_HOME}; cd ${MDS_STAGE}/bin; ./dspace stage kernel; ./dspace stage admin; ./dspace stage cts-gen

# default command is 'dspace'
WORKDIR /mds/rt/bin
ENTRYPOINT ["./dspace"]
