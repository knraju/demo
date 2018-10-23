FROM centos:centos7

ENV FILER_HOME https://s3.amazonaws.com/spvss-binrepo
ENV PATH=$PATH:/usr/local/bin/
ENV JAVA_HOME=/usr/lib/jvm/jre-1.8.0-openjdk
ENV JRE_HOME=/usr/lib/jvm/jre

RUN yum update -y && \
    yum -y install sudo epel-release unzip && \
    yum install -y make-1:3.82-23.el7.x86_64 \
        git-1.8.3.1-13.el7.x86_64 \
        python2-pip-0:8.1.2-6.el7.noarch \
        python-devel-2.7.5-69.el7_5.x86_64 \
        mariadb-devel-1:5.5.56-2.el7.x86_64 \
        python2-boto-2.45.0-3.el7.noarch \
        gcc-4.8.5-28.el7.x86_64 \
        zip-3.0-11.el7.x86_64 \
        nmap-ncat-6.40-13.el7.x86_64 \
        java-1.8.0-openjdk.x86_64 \
        java-1.8.0-openjdk-devel \
        elinks \
        gcc-c++ patch readline readline-devel zlib zlib-devel which && \
    yum install -y perl-5.16.3-292.el7.x86_64 perl-JSON perl-libwww-perl && \
    yum install -y libyaml-devel libffi-devel openssl-devel make && \
    yum install -y bzip2 autoconf automake libtool bison iconv-devel sqlite-devel jq && \
    yum install -y Xvfb gtk3 xorg-x11-xauth wget xdg-utils lsb && \
    yum clean all

ENV SCALA_FILE=scala-2.10.1.rpm
ENV SBT_FILE=sbt-0.13.13.rpm

RUN curl -o /tmp/$SCALA_FILE $FILER_HOME/$SCALA_FILE \
    && rpm -i /tmp/$SCALA_FILE \
    && curl -o /tmp/$SBT_FILE $FILER_HOME/$SBT_FILE \
    && rpm -i /tmp/$SBT_FILE

ENV TERRAFORM_FILE=terraform_0.11.7_linux_amd64.zip
ENV TERRAFORM_MD5SUM=3dce305de201522423cc388d57693794
ARG CMAKE_VERSION=3.8.2

RUN curl -o /tmp/$TERRAFORM_FILE $FILER_HOME/$TERRAFORM_FILE \
    && echo "$TERRAFORM_MD5SUM  /tmp/$TERRAFORM_FILE" > /tmp/MD5SUM \
    && md5sum -c /tmp/MD5SUM \
    && unzip /tmp/$TERRAFORM_FILE \
    && mv terraform /usr/local/bin \
    && curl -o /tmp/cmake.tar.gz $FILER_HOME/cmake/cmake-${CMAKE_VERSION}-Linux-x86_64.tar.gz \
    && echo "ab02cf61915e1ad15b8523347ad37c46 /tmp/cmake.tar.gz" > /tmp/MD5SUM \
    && md5sum -c /tmp/MD5SUM \
    && tar -xf /tmp/cmake.tar.gz -C /tmp \
    && mv /tmp/cmake-${CMAKE_VERSION}-Linux-x86_64 /lib/cmake \
    && ln -s /lib/cmake/bin/cmake /bin/cmake \
    && rm -rf /tmp/cmake.tar.gz

ENV VAULT_FILE=vault_0.5.3_linux_amd64.zip

RUN curl -o /tmp/$VAULT_FILE $FILER_HOME/vault/$VAULT_FILE \
   && unzip /tmp/$VAULT_FILE \
   && mv vault /usr/local/bin \
   && rm -rf /tmp/$VAULT_FILE

ENV PHANTOMJS_VERSION=2.1.1
ENV PHANTOMJS_NAME=phantomjs-2.1.1-linux-x86_64
ENV PHANTOMJS_FILE=${PHANTOMJS_NAME}.tar.bz2
ENV PHANTOMJS_MD5_FILE=${PHANTOMJS_NAME}.tar.bz2.md5

RUN curl -o $PHANTOMJS_FILE $FILER_HOME/phantomjs/$PHANTOMJS_VERSION/$PHANTOMJS_FILE \
   && curl -o $PHANTOMJS_MD5_FILE $FILER_HOME/phantomjs/$PHANTOMJS_VERSION/$PHANTOMJS_MD5_FILE \
   && md5sum -c $PHANTOMJS_MD5_FILE \
   && tar xvf $PHANTOMJS_FILE -C /usr/local \
   && rm $PHANTOMJS_FILE $PHANTOMJS_MD5_FILE

ENV PATH /usr/local/$PHANTOMJS_NAME/bin:$PATH

RUN yum install -y chromium

ENV FIREFOX_VERSION=57.0
ENV FIREFOX_NAME=firefox-57.0
ENV FIREFOX_FILE=${FIREFOX_NAME}.tar.bz2

RUN curl -o $FIREFOX_FILE $FILER_HOME/firefox/$FIREFOX_VERSION/$FIREFOX_FILE \
   && tar xvf $FIREFOX_FILE -C /usr/lib64/ \
   && ln -s /usr/lib64/firefox/firefox-bin /usr/bin/firefox \
   && rm -rf $FIREFOX_FILE

ENV GECKODRIVER_VERSION=0.20.0
ENV GECKODRIVER_NAME=geckodriver-v0.20.0-linux64
ENV GECKODRIVER_FILE=${GECKODRIVER_NAME}.tar.gz

RUN curl -o $GECKODRIVER_FILE $FILER_HOME/geckodriver/$GECKODRIVER_VERSION/$GECKODRIVER_FILE \
   && tar xvf $GECKODRIVER_FILE -C /usr/local/bin \
   && rm -rf $GECKODRIVER_FILE

RUN wget https://chromedriver.storage.googleapis.com/2.36/chromedriver_linux64.zip \
    && unzip chromedriver_linux64.zip -d /usr/local \
    && rm -rf chromedriver_linux64.zip

ENV GOOGLE_CHROME_FILE=google-chrome-stable_current_x86_64.rpm

RUN curl -o $GOOGLE_CHROME_FILE $FILER_HOME/google-chrome/$GOOGLE_CHROME_FILE \
    && rpm --install $GOOGLE_CHROME_FILE \
    && rm -rf $GOOGLE_CHROME_FILE

ADD root /

RUN pip install -r /etc/requirements.txt

RUN groupadd jenkinsuser -g 25 \
    && useradd --shell /bin/bash -u 912296 -g 25 -o -d /home/jenkins -c "" jenkins

#Present working directory is set as the ROOT directory
WORKDIR /root

RUN command curl -sSL https://rvm.io/mpapis.asc | gpg2 --import - && \
    curl -L get.rvm.io | bash -s stable && \
    /bin/bash -c -l "source /etc/profile.d/rvm.sh && rvm install 2.4.1 && gem install github_changelog_generator -v 1.14.3  && gem install github-markup -v 1.6.0 && gem install commonmarker -v 0.16.3"

RUN curl -o /tmp/dockerize-linux-amd64-v0.0.4.tar.gz ${FILER_HOME}/dockerize/dockerize-linux-amd64-v0.0.4.tar.gz && \
    tar -C /usr/local/bin -xvzf /tmp/dockerize-linux-amd64-v0.0.4.tar.gz && \
    rm -rf /tmp/dockerize-linux-amd64-v0.0.4.tar.gz
