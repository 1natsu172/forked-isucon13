FROM node:20.9.0-bullseye-slim

WORKDIR /tmp
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update &&\
    apt-get install -y gnupg2 &&\
    apt-key adv --keyserver keyserver.ubuntu.com --recv-keys B7B3B788A8D3785C &&\
    apt-get update && \
    apt-get -y upgrade && \
    apt-get install -y curl wget gcc g++ make sqlite3 locales locales-all && \
    wget -q https://dev.mysql.com/get/mysql-apt-config_0.8.29-1_all.deb && \
    apt-get -y install ./mysql-apt-config_0.8.29-1_all.deb && \
    apt-get -y update && \
    apt-get -y install default-mysql-client pdns-server pdns-backend-mysql

# forked-original: goランタイムを用意する
RUN wget https://go.dev/dl/go1.21.6.linux-amd64.tar.gz && \
    tar -C /usr/local -xzf go1.21.6.linux-amd64.tar.gz

# forked-original: sudoを用意する
RUN apt-get update && \
    apt-get install -y sudo

RUN rm -f /etc/powerdns/pdns.d/bind.conf

RUN locale-gen en_US.UTF-8
RUN useradd --uid=1001 --create-home isucon
# forked-original: isucon ユーザーをパスワード付きユーザーにする
RUN echo 'isucon:isucon' | chpasswd
# forked-original: sudo権限を付与
RUN echo 'isucon ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers
# forked-original: go PATHの設定をする
RUN echo 'export PATH=$PATH:/usr/local/go/bin' >> /home/isucon/.bashrc

USER isucon

RUN mkdir -p /home/isucon/webapp/node
WORKDIR /home/isucon/webapp/node
COPY --chown=isucon:isucon ./package.json ./package-lock.json /home/isucon/webapp/node/
RUN npm install

COPY --chown=isucon:isucon ./ /home/isucon/webapp/node/

ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8

EXPOSE 8080
CMD ["npm", "run", "start"]
