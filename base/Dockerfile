FROM centos

ARG HADOOP_VERSION=3.1.1
ENV ROOT_SSH /root/.ssh
ENV SSH_CONFIG /etc/sshd/sshd_config
ENV SSH_DEAMON /usr/sbin/sshd
ENV KEYS http://www-eu.apache.org/dist/hadoop/common/KEYS
ENV KEYS_TMP /tmp/KEYS
ENV HADOOP_INSTALLER hadoop-$HADOOP_VERSION.tar.gz
ENV HADOOP_NAME hadoop-$HADOOP_VERSION
ENV HADOOP_HOME /usr/lib/$HADOOP_NAME
ENV HADOOP_ASC $HADOOP_INSTALLER.asc
ENV HADOOP_ASC_URL http://www-eu.apache.org/dist/hadoop/common/$HADOOP_NAME/$HADOOP_ASC
ENV HADOOP_INSTALLER_URL http://ftp.man.poznan.pl/apache/hadoop/common/$HADOOP_NAME/$HADOOP_INSTALLER
ENV HADOOP_TMP_DIR /tmp/hadoop
ENV HADOOP_ENV $HADOOP_HOME/etc/hadoop/hadoop-env.sh
ENV HADOOP_MAPRED_HOME $HADOOP_HOME
ENV HADOOP_ETC $HADOOP_HOME/etc/hadoop

# 1. proxy workaround
RUN echo timeout=200 >> /etc/yum.conf

# 2. install ssh and configure ssh
RUN yum update -y 2>&1 && \
    yum install -y openssh-clients \
        openssh-server \
        mc \
        rsync \
        vim \
        wget 2>&1 && \
    /usr/bin/ssh-keygen -A && \
    $SSH_DEAMON && \
    ssh-keygen -t rsa -P '' -f $ROOT_SSH/id_rsa && \
    cat $ROOT_SSH/id_rsa.pub >> $ROOT_SSH/authorized_keys && \
    chmod 0600 $ROOT_SSH/authorized_keys && \
    echo "$SSH_DEAMON" >>~/.bash_profile

# 3. install java
RUN yum update -y 2>&1 && yum install -y java-1.8.0-openjdk-devel 2>&1
ENV JAVA_HOME /etc/alternatives/java

# 4. download and extract hadoop
RUN echo "--$HADOOP_INSTALLER_URL--"
RUN wget --no-check-certificate -c $KEYS -O $KEYS_TMP 2>&1 && \
    gpg --import $KEYS_TMP 2>&1 && \
    mkdir $HADOOP_TMP_DIR && \
    wget --no-check-certificate -c $HADOOP_INSTALLER_URL -O $HADOOP_TMP_DIR/$HADOOP_INSTALLER 2>&1 && \
    wget --no-check-certificate -c $HADOOP_ASC_URL -O $HADOOP_TMP_DIR/$HADOOP_ASC 2>&1 && \
    tar -xvzf $HADOOP_TMP_DIR/$HADOOP_INSTALLER -C /usr/lib && \
    rm -rf /tmp/*

# 5. hadoop configuration
ADD base/config/core-site.xml $HADOOP_ETC/core-site.xml
ADD base/config/hadoop-env.sh $HADOOP_ETC/hadoop-env.sh
ADD base/config/hdfs-site.xml $HADOOP_ETC/hdfs-site.xml
ADD base/config/mapred-env.sh $HADOOP_ETC/mapred-env.sh
ADD base/config/mapred-site.xml $HADOOP_ETC/mapred-site.xml
ADD base/config/yarn-env.sh $HADOOP_ETC/yarn-env.sh
ADD base/config/yarn-site.xml $HADOOP_ETC/yarn-site.xml

# 6. avoid unnecessary warnings
RUN mkdir $HADOOP_HOME/logs

WORKDIR $HADOOP_HOME
CMD /bin/bash
