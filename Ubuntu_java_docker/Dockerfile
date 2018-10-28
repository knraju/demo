FROM ubuntu:16.04
LABEL MAINTAINER knraju

ENV JAVA_HOME       /usr/lib/jvm/java-8-oracle

RUN apt-get update && \
	apt-get install -y --no-install-recommends locales unzip wget curl ca-certificates&& \
	curl -L -o terraform.zip https://releases.hashicorp.com/terraform/0.11.10/terraform_0.11.10_linux_amd64.zip && \  
	unzip terraform.zip  -d /usr/local/bin && \ 
	rm  terraform.zip
	
RUN apt-get dist-upgrade -y && \
	apt-get --purge remove openjdk* && \
	echo "oracle-java8-installer shared/accepted-oracle-license-v1-1 select true" | debconf-set-selections && \
	echo "deb http://ppa.launchpad.net/webupd8team/java/ubuntu xenial main" > /etc/apt/sources.list.d/webupd8team-java-trusty.list && \
	apt-key adv --keyserver keyserver.ubuntu.com --recv-keys EEA14886 && \
	apt-get update && \
	apt-get install -y --no-install-recommends oracle-java8-installer oracle-java8-set-default && \
	apt-get clean all

