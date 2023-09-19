FROM jenkins/jnlp-slave:4.13.2-1-jdk11
USER root
RUN apt-get update -qq \
    && apt-get install -qqy software-properties-common apt-transport-https ca-certificates curl zip git wget unzip

ARG USER_HOME_DIR="/home/jenkins"

# Install maven and openjdk
ARG MAVEN_VERSION=3.8.6
ARG BASE_URL=https://repo.maven.apache.org/maven2/org/apache/maven/apache-maven/${MAVEN_VERSION}

RUN mkdir -p /usr/share/maven /usr/share/maven/ref \
  && curl -fsSL -o /tmp/apache-maven.tar.gz ${BASE_URL}/apache-maven-${MAVEN_VERSION}-bin.tar.gz \
  && tar -xzf /tmp/apache-maven.tar.gz -C /usr/share/maven --strip-components=1 \
  && rm -f /tmp/apache-maven.tar.gz \
  && ln -s /usr/share/maven/bin/mvn /usr/bin/mvn

ENV MAVEN_HOME /usr/share/maven
ENV MAVEN_CONFIG "$USER_HOME_DIR/.m2"

RUN apt-add-repository 'deb http://security.debian.org/debian-security stable-security main'
RUN apt-get update

RUN chown -R jenkins:jenkins /home/jenkins &&  chmod -R 777 /home/jenkins

ENV ANDROID_HOME /opt/android-sdk-linux
ENV TARGET_VERSION=30
ENV ANDROID_BUILD_TOOLS_VERSION=30.0.2

# android command line tools
RUN mkdir .android
RUN chown -R jenkins:jenkins .android && chmod -R 777 .android
RUN touch .android/repositories.cfg
RUN wget "https://dl.google.com/android/repository/commandlinetools-linux-7302050_latest.zip" -O commandlinetools.zip
RUN unzip commandlinetools.zip -d $ANDROID_HOME/
RUN rm commandlinetools.zip
RUN yes | $ANDROID_HOME/cmdline-tools/bin/sdkmanager "platforms;android-${TARGET_VERSION}" --sdk_root=$ANDROID_HOME
RUN yes | $ANDROID_HOME/cmdline-tools/bin/sdkmanager "build-tools;${ANDROID_BUILD_TOOLS_VERSION}" --sdk_root=$ANDROID_HOME

# Install nodejs and yarn
RUN curl -fsSL https://deb.nodesource.com/setup_18.x | bash -
RUN apt update -y && apt -y install nodejs
RUN npm install -g yarn

# Install aws cli
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
RUN unzip awscliv2.zip
RUN rm awscliv2.zip
RUN ./aws/install
RUN chown -R jenkins:jenkins $ANDROID_HOME

# install fastlane  
RUN apt update && apt-get update && apt-get upgrade -y --no-install-recommends --allow-unauthenticated ruby-full
RUN echo 'source "https://rubygems.org"\n\ngem "fastlane"' >> Gemfile
RUN gem install bundler -v 2.3.26
RUN apt-get install build-essential -y
RUN bundle install
RUN gem install fastlane-plugin-huawei_appgallery -v 0.2.4
RUN gem install fastlane-plugin-huawei_appgallery_connect -v 1.0.23

# install apk-mitm
RUN npm install -g apk-mitm
 

# Clean up
RUN rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    apt-get autoremove -y && \
    apt-get clean

USER jenkins
ENTRYPOINT ["/usr/local/bin/jenkins-agent"] 