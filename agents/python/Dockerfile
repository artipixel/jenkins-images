FROM jenkins/jnlp-slave:4.13.2-1-jdk11
USER root
RUN apt-get update -qq \
    && apt-get install -qqy apt-transport-https ca-certificates curl gnupg2 nano zip software-properties-common
RUN curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -
RUN add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/debian \
   $(lsb_release -cs) \
   stable"
RUN apt-get update  -qq \
    && apt-get install docker-ce=17.12.1~ce-0~debian -y
RUN usermod -aG docker jenkins

# Install python 3
RUN apt update -y && apt -y install python3 python3-pip
RUN pip3 install virtualenv 
#Install EB cli
RUN git clone https://github.com/aws/aws-elastic-beanstalk-cli-setup.git
RUN python3 ./aws-elastic-beanstalk-cli-setup/scripts/ebcli_installer.py

# Install aws cli
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
RUN unzip awscliv2.zip
RUN ./aws/install

USER jenkins