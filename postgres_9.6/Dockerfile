FROM debian:jessie

MAINTAINER to.gnatuk@gmail.com

RUN groupadd -r postgres --gid=999 && useradd -r -g postgres --uid=999 postgres

RUN apt-get update \
	&& apt-get install -y git build-essential libreadline-dev zlib1g-dev \
	&& mkdir -p /temp/postgres_9.6/sources

ADD https://ftp.postgresql.org/pub/source/v9.6.6/postgresql-9.6.6.tar.gz /temp/postgres_9.6/sources
RUN cd /temp/postgres_9.6/sources && gunzip postgresql-9.6.6.tar.gz && tar xf postgresql-9.6.6.tar \
	&& cd /temp/postgres_9.6/sources/postgresql-9.6.6 \
	&& ./configure \
	&& make \
	&& make install \
	&& mkdir /usr/local/pgsql/data \
	&& chown postgres /usr/local/pgsql/data

ADD init.sh /init.sh
RUN chmod +x /init.sh && chown postgres /init.sh

RUN apt-get update && apt-get install -y locales && rm -rf /var/lib/apt/lists/* \
	&& localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8
ENV LANG en_US.utf8

USER postgres

ENV PGDATA /usr/local/pgsql/data
ENV PGHOME /usr/local/pgsql
ENV PATH $PATH:/usr/local/pgsql/bin

RUN $PGHOME/bin/initdb -D $PGDATA
RUN echo "host all  all    0.0.0.0/0  md5" >> /usr/local/pgsql/data/pg_hba.conf
RUN echo "listen_addresses='*'" >> /usr/local/pgsql/data/postgresql.conf

VOLUME /usr/local/pgsql/data/volume
EXPOSE 5432

RUN /init.sh

CMD ["-D $PGDATA >logfile 2>&1 &"]
ENTRYPOINT postgres