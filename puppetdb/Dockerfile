FROM centos:centos7

# TODO
# http://label-schema.org/rc1/#build-time-labels
LABEL org.label-schema.vendor="" \
      org.label-schema.url="https://github.com/NTTCom-MS" \
      org.label-schema.name="puppetdb" \
      org.label-schema.license="" \
      org.label-schema.version="5.5.6"\
      org.label-schema.vcs-url="https://github.com/NTTCom-MS/docker-puppetmaster5" \
      org.label-schema.vcs-ref="" \
      org.label-schema.build-date="2019-08-19T16:00:00.52Z" \
      org.label-schema.schema-version="1.0"

ENV EYP_POSTGRES_PUPPETDB_PASSWORD 769d147d725f3a3d356f99eda68ed5cd
ENV EYP_PUPPETFQDN=puppet5
ENV HOME /root
ENV PATH /usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/opt/puppetlabs/bin:/opt/puppetlabs/puppet/bin:/opt/bash-puppetmasterless:/root/bin

RUN mkdir /root/.ssh
RUN chmod 0700 /root/.ssh/

RUN mkdir -p /usr/local/bin/
RUN mkdir -p /usr/local/src
COPY runme.sh /usr/local/bin/runme.sh
RUN chmod +x /usr/local/bin/runme.sh

#
# utils
#
RUN yum install openssl git ruby -y
RUN yum install epel-release -y
#
# puppet-masterless
#
RUN mkdir -p /opt
RUN bash -c 'cd /opt; git clone https://github.com/NTTCom-MS/bash-puppetmasterless.git'

#
# puppet repo & agent & r10k
#

#RUN rpm -Uvh https://yum.puppetlabs.com/puppet5/puppet5-release-el-7.noarch.rpm
#RUN yum install puppet-agent -y
#RUN /usr/bin/gem install cri -v 2.15.10
#RUN /usr/bin/gem install multipart-post -v 2.2.0
#RUN /usr/bin/gem install fast_gettext -v 1.1.0
#RUN /usr/bin/gem install semantic_puppet -v 1.0.4
#RUN /usr/bin/gem install jwt -v 1.5.6
#RUN /usr/bin/gem install r10k -v 3.0.0

RUN /bin/bash /opt/bash-puppetmasterless/setup.sh

#
# postgres install
#

# https://github.com/NTTCom-MS/eyp-postgresql
RUN /opt/bash-puppetmasterless/localpuppetmaster.sh -d /tmp/postgres -r https://github.com/NTTCom-MS/eyp-postgresql
COPY postgres.pp /usr/local/src/postgres.pp

#
# postgres install
#

RUN /opt/bash-puppetmasterless/localpuppetmaster.sh -d /tmp/postgres -s /usr/local/src/postgres.pp

#
# puppetDB install
#
RUN yum install puppetdb puppetdb-terminus -y
COPY database.ini /etc/puppetlabs/puppetdb/conf.d/database.ini
COPY jetty.ini /etc/puppetlabs/puppetdb/conf.d/jetty.ini


EXPOSE 8080
EXPOSE 8081

RUN mkdir -p /etc/puppetlabs/puppetdb/ssl
RUN mkdir -p /etc/puppetlabs/puppet/ssl

CMD /bin/bash /usr/local/bin/runme.sh
