FROM alpine:3.15.0 AS base

ARG ZIP_VERSION
ARG DL_VERSION

RUN apk add --update-cache \
    unzip

# add the bootstrap file
COPY bootstrap.sh /terraria-server/bootstrap.sh

ENV DL_LINK=https://terraria.org/api/download/pc-dedicated-server/terraria-server-${ZIP_VERSION}.zip
ENV DL_VERSION=${DL_VERSION}
ENV DL_FILE=terraria-server-${ZIP_VERSION}.zip

ADD $DL_LINK /$DL_FILE

RUN unzip /$DL_FILE -d /terraria && \
    mv /terraria/$DL_VERSION/Linux/* /terraria-server && \
    #Linux subfolder does not include any config text file, oddly.
    mv /terraria/$DL_VERSION/Windows/serverconfig.txt /terraria-server/serverconfig-default.txt && \
    chmod +x /terraria-server/TerrariaServer && \
    chmod +x /terraria-server/TerrariaServer.bin.x86_64

FROM mono:6.10.0.104-slim
LABEL maintainer="Ryan Sheehan <rsheehan@gmail.com>"

# documenting ports
EXPOSE 7777

# env used in the bootstrap
ENV LOGPATH=/terraria-server/logs
ENV WORLDPATH=/terraria-server/Worlds
ENV WORLD_FILENAME="poobloxs.wld"
ENV CONFIGPATH=/terraria-server/config
ENV CONFIG_FILENAME="serverconfig.txt"

COPY --from=base /terraria-server/ /terraria-server/

# Set working directory to server
WORKDIR /terraria-server

ENTRYPOINT [ "/bin/sh", "bootstrap.sh" ]
