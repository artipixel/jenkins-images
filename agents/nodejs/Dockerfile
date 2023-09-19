FROM jenkins/jnlp-slave:4.13.2-1-jdk11
USER root
RUN apt-get update -qq \
    && apt-get install -qqy apt-transport-https ca-certificates curl zip nano git

# Install nodejs and yarn
RUN curl -fsSL https://deb.nodesource.com/setup_18.x | bash -
RUN curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
RUN apt update -y && apt -y install nodejs
RUN npm install -g yarn
# Install aws cli
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
RUN unzip awscliv2.zip
RUN ./aws/install

USER jenkins