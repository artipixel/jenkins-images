FROM jenkins/jnlp-slave:4.13.2-1-jdk11
USER root
RUN apt-get update -qq \
    && apt-get install -qqy software-properties-common apt-transport-https ca-certificates curl zip nano git

ARG USER_HOME_DIR="/home/jenkins"

# Install maven and openjdk
ARG MAVEN_VERSION=3.8.6
ARG BASE_URL=https://apache.osuosl.org/maven/maven-3/${MAVEN_VERSION}/binaries

RUN mkdir -p /usr/share/maven /usr/share/maven/ref \
  && curl -fsSL -o /tmp/apache-maven.tar.gz ${BASE_URL}/apache-maven-${MAVEN_VERSION}-bin.tar.gz \
  && tar -xzf /tmp/apache-maven.tar.gz -C /usr/share/maven --strip-components=1 \
  && rm -f /tmp/apache-maven.tar.gz \
  && ln -s /usr/share/maven/bin/mvn /usr/bin/mvn

ENV MAVEN_HOME /usr/share/maven
ENV MAVEN_CONFIG "$USER_HOME_DIR/.m2"

RUN apt-add-repository 'deb http://security.debian.org/debian-security stretch/updates main'
RUN apt-get update
# RUN apt-get install openjdk-8-jdk -y

RUN chown -R jenkins:jenkins /home/jenkins &&  chmod -R 777 /home/jenkins
# COPY --chown=jenkins:jenkins ./settings.xml /home/jenkins/.m2/settings.xml

# Install python 3 
RUN apt update -y && apt -y install python3 python3-pip
RUN ln -s /usr/bin/python3 /usr/bin/python & \
    ln -s /usr/bin/pip3 /usr/bin/pip
RUN pip3 install virtualenv

# Install aws cli
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
RUN unzip awscliv2.zip
RUN ./aws/install

USER jenkins

# Install EB
RUN git clone https://github.com/aws/aws-elastic-beanstalk-cli-setup.git
RUN python ./aws-elastic-beanstalk-cli-setup/scripts/ebcli_installer.py
ENV PATH="${PATH}:/home/jenkins/.ebcli-virtual-env/executables"

ENTRYPOINT ["/usr/local/bin/jenkins-agent"]