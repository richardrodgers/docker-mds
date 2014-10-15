# mds-bits: Asset (Bitstream) storage service using Seaweed-FS
# https://github.com/chrislusf/weed-fs

FROM cydev/go

MAINTAINER Richard Rodgers <rrodgers@mit.edu> <orcid:0000-0003-1412-5595>

RUN go get code.google.com/p/weed-fs/go/weed

EXPOSE 8080
EXPOSE 9333

VOLUME /data

ENTRYPOINT ["weed"]
