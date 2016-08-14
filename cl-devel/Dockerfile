FROM ubuntu:trusty
MAINTAINER SANO Masatoshi
ARG user=dev

ENV PATH /home/${user}/.roswell/bin:/usr/local/bin:$PATH

RUN apt-get update && \
    apt-get -y install libcurl4-gnutls-dev git build-essential automake autoconf \
    openssh-server emacs24-nox docker.io && \
    apt-get autoremove && apt-get clean

RUN groupadd -g 1000 ${user} && \
    useradd -g ${user} -G sudo -m -s /bin/bash ${user} && \
    echo ${user}:password | chpasswd

#sshd
ARG SSH_HOME=/home/${user}/.ssh
RUN mkdir /var/run/sshd
RUN mkdir ${SSH_HOME} && \
    chown ${user}:${user} ${SSH_HOME} && \
    chmod 700 ${SSH_HOME}

COPY .ssh/ ${SSH_HOME}/
RUN cp ${SSH_HOME}/id_rsa.pub ${SSH_HOME}/authorized_keys || true && \
    chown ${user}:${user} ${SSH_HOME}/* && \
    chmod 600 ${SSH_HOME}/*

## install roswell
WORKDIR /usr/local/src
RUN git clone -b release https://github.com/roswell/roswell
WORKDIR /usr/local/src/roswell
RUN sh ./bootstrap && \
       ./configure && \
       make && \
       make install && \
       rm -rf /usr/local/src/roswell

USER ${user}
RUN ros setup && \
    ros swank install 2.18 && \
    rm -rf /home/${user}/.roswell/src/ /home/${user}/.roswell/tmp/

USER root
#emacs
ARG EMACS_D=/home/${user}/.emacs.d
COPY .emacs.d/ ${EMACS_D}/
RUN chown -R ${user}:${user} ${EMACS_D} |true

EXPOSE 22 5000 4005
CMD ["/usr/sbin/sshd", "-D"]
