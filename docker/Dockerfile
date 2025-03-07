# Etherpad Lite Dockerfile
#
# https://github.com/ether/etherpad-docker
#
# Author: muxator
#
# Version 0.1

FROM node:latest
LABEL maintainer="Etherpad team, https://github.com/ether/etherpad-lite"

# git hash of the version to be built.
# If not given, build the latest development version.
ARG ETHERPAD_VERSION=develop

# plugins to install while building the container. By default no plugins are
# installed.
# If given a value, it has to be a space-separated, quoted list of plugin names.
#
# EXAMPLE:
#   ETHERPAD_PLUGINS="ep_codepad ep_author_neat"
ARG ETHERPAD_PLUGINS=

# Set the following to production to avoid installing devDeps
# this can be done with build args (and is mandatory to build ARM version)
ENV NODE_ENV=development

# grab the ETHERPAD_VERSION tarball from github (no need to clone the whole
# repository)
RUN echo "Getting version: ${ETHERPAD_VERSION}" && \
	curl \
		--location \
		--fail \
		--silent \
		--show-error \
		--output /opt/etherpad-lite.tar.gz \
		https://github.com/ether/etherpad-lite/archive/"${ETHERPAD_VERSION}".tar.gz && \
	mkdir /opt/etherpad-lite && \
	tar xf /opt/etherpad-lite.tar.gz \
		--directory /opt/etherpad-lite \
		--strip-components=1 && \
	rm /opt/etherpad-lite.tar.gz

WORKDIR /opt/etherpad-lite

# install node dependencies for Etherpad
RUN bin/installDeps.sh

# Install the plugins, if ETHERPAD_PLUGINS is not empty.
#
# Bash trick: in the for loop ${ETHERPAD_PLUGINS} is NOT quoted, in order to be
# able to split at spaces.
RUN for PLUGIN_NAME in ${ETHERPAD_PLUGINS}; do npm install "${PLUGIN_NAME}"; done

# Copy the custom configuration file, if present. The configuration file has to
# be manually put inside the same directory containing the Dockerfile (we cannot
# directly point to "../settings.json" for Docker's security restrictions).
#
# For the conditional COPY trick, see:
#   https://stackoverflow.com/questions/31528384/conditional-copy-add-in-dockerfile#46801962
COPY nop setting[s].json /opt/etherpad-lite/

EXPOSE 9001
CMD ["node", "node_modules/ep_etherpad-lite/node/server.js"]
