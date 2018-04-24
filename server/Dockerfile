

#from alpine

#run apk update && apk add openjdk8 bash curl git make
#run curl -O -L https://raw.githubusercontent.com/technomancy/leiningen/stable/bin/lein && chmod 755 lein  && mv lein /usr/local/bin
#run lein
#run git clone https://github.com/puppetlabs/puppetserver
#run cd puppetserver; lein uberjar


# The prod container

from agent
run apk update && apk add openjdk8 dumb-init rpm coreutils
RUN adduser -D puppet
run rpm -Uvh --nodeps http://yum.puppetlabs.com/puppet5/el/7/x86_64/puppetserver-5.3.0-1.el7.noarch.rpm
run gem install --no-rdoc --no-ri r10k

COPY puppetserver /etc/default/puppetserver
COPY logback.xml /etc/puppetlabs/puppetserver/
COPY request-logging.xml /etc/puppetlabs/puppetserver/
RUN puppet config set autosign true --section master

COPY docker-entrypoint.sh /
RUN chmod 755 /docker-entrypoint.sh

EXPOSE 8140

#ENTRYPOINT ["dumb-init", "/docker-entrypoint.sh"]
#CMD ["foreground" ]

HEALTHCHECK --interval=10s --timeout=10s --retries=90 CMD \
  curl --fail -H 'Accept: pson' \
  --resolve 'puppet:8140:127.0.0.1' \
  --cert   $(puppet config print hostcert) \
  --key    $(puppet config print hostprivkey) \
  --cacert $(puppet config print localcacert) \
  https://puppet:8140/${PUPPET_HEALTHCHECK_ENVIRONMENT}/status/test \
  |  grep -q '"is_alive":true' \
  || exit 1

COPY Dockerfile /

