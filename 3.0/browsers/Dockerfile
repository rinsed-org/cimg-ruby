# vim:set ft=dockerfile:

FROM cimg/ruby:3.0.2

LABEL maintainer="CircleCI Community & Partner Engineering Team <community-partner@circleci.com>"

# Dockerfile will pull the latest LTS release from cimg-node.
RUN curl -sSL "https://raw.githubusercontent.com/CircleCI-Public/cimg-node/master/ALIASES" -o nodeAliases.txt && \
    NODE_VERSION=$(grep "lts" ./nodeAliases.txt | cut -d "=" -f 2-) && \
    curl -L -o node.tar.xz "https://nodejs.org/dist/v${NODE_VERSION}/node-v${NODE_VERSION}-linux-x64.tar.xz" && \
    sudo tar -xJf node.tar.xz -C /usr/local --strip-components=1 && \
    rm node.tar.xz nodeAliases.txt && \
    sudo ln -s /usr/local/bin/node /usr/local/bin/nodejs

ENV YARN_VERSION 1.22.5
RUN curl -L -o yarn.tar.gz "https://yarnpkg.com/downloads/${YARN_VERSION}/yarn-v${YARN_VERSION}.tar.gz" && \
    sudo tar -xzf yarn.tar.gz -C /opt/ && \
    rm yarn.tar.gz && \
    sudo ln -s /opt/yarn-v${YARN_VERSION}/bin/yarn /usr/local/bin/yarn && \
    sudo ln -s /opt/yarn-v${YARN_VERSION}/bin/yarnpkg /usr/local/bin/yarnpkg

# Install Selenium
ENV SELENIUM_VER=3.141.59
RUN curl -sSL -o selenium-server-standalone-${SELENIUM_VER}.jar "https://selenium-release.storage.googleapis.com/${SELENIUM_VER%.*}/selenium-server-standalone-${SELENIUM_VER}.jar" && \
    sudo cp selenium-server-standalone-${SELENIUM_VER}.jar /usr/local/bin/selenium.jar && \
    rm selenium-server-standalone-${SELENIUM_VER}.jar

RUN sudo apt-get update && \

    # Install Java only if it's not already available
    # Java is installed for Selenium
    if ! command -v java > /dev/null; then \
        echo "Java not found in parent image, installing..." && \
        sudo apt-get install -y --no-install-recommends --no-upgrade openjdk-11-jre; \
    fi && \

    # Firefox deps
    sudo apt-get install -y --no-install-recommends --no-upgrade \
		libdbus-glib-1-2 \
		libgtk-3-dev \
		libxt6 \
	&& \

    # Google Chrome deps
	# Some of these packages should be pulled into their own section
    sudo apt-get install -y --no-install-recommends --no-upgrade \
        fonts-liberation \
        libappindicator3-1 \
        libasound2 \
        libatk-bridge2.0-0 \
        libatspi2.0-0 \
        libcairo2 \
        libcups2 \
        libgbm1 \
        libgdk-pixbuf2.0-0 \
        libgtk-3-0 \
        libpango-1.0-0 \
        libpangocairo-1.0-0 \
        libxcursor1 \
		libxss1 \
        xdg-utils \
		xvfb \
	&& \
	sudo rm -rf /var/lib/apt/lists/*

# Below is setup to allow xvfb to start when the container starts up.
# The label in particular allows this image to override what CircleCI does
# when booting the image.
LABEL com.circleci.preserve-entrypoint=true
ENV DISPLAY=":99"
#RUN	printf '#!/bin/sh\nXvfb :99 -screen 0 1280x1024x24 &\nexec "$@"\n' > /tmp/entrypoint && \
#	chmod +x /tmp/entrypoint && \
#	sudo mv /tmp/entrypoint /docker-entrypoint.sh
RUN	printf '#!/bin/sh\nXvfb :99 -screen 0 1280x1024x24 &\nexec "$@"\n' | sudo tee /docker-entrypoint.sh && \
	sudo chmod +x /docker-entrypoint.sh

ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["/bin/sh"]
