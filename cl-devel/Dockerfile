FROM ubuntu:xenial
MAINTAINER SANO Masatoshi
ARG user=dev

ENV PATH /usr/local/bin:$PATH

RUN apt-get update && apt-get -y upgrade && \
    apt-get -y install libcurl4-gnutls-dev git build-essential automake autoconf \
    openssh-server emacs24-nox docker.io sudo && \
    apt-get autoremove && apt-get clean

RUN groupadd -g 1000 ${user} && \
    useradd -g ${user} -G sudo -m -s /bin/bash ${user} && \
    echo ${user}:password | chpasswd && \
    echo "${user} ALL=NOPASSWD: ALL" >>  /etc/sudoers

#sshd
RUN mkdir /var/run/sshd && \
    locale-gen en_US.UTF-8 && \
    sed -i 's/^AcceptEnv LANG LC_\*$//g' /etc/ssh/sshd_config

## install roswell
WORKDIR /usr/local/src
RUN git clone -b release https://github.com/roswell/roswell && \
    cd roswell && \
    sh ./bootstrap && \
    ./configure && \
    make && \
    make install && \
    rm -rf /usr/local/src/roswell

ARG sbcl_version=
ARG swank_version=2.18

USER ${user}
RUN ros install sbcl-bin/${sbcl_version} && \
    ros setup && \
    ros swank install ${swank_version} && \
    ros install qlot && \
    ros install sbcl/`ros config show sbcl-bin.version` --without-install && \
    echo "export PATH=${HOME}/.roswell/bin:${PATH}" >> ${HOME}/.bashrc && \
    echo "export LANG=en_US.UTF-8" >> ${HOME}/.bashrc

# personal
USER root
#emacs
ARG EMACS_D=/home/${user}/.emacs.d
COPY .emacs.d/ ${EMACS_D}/
RUN chown -R ${user}:${user} ${EMACS_D} |true

#ssh keys
ARG SSH_HOME=/home/${user}/.ssh
COPY .ssh/ ${SSH_HOME}/
RUN chown ${user}:${user} ${SSH_HOME} && \
    chmod 700 ${SSH_HOME} && \
    cp ${SSH_HOME}/id_rsa.pub ${SSH_HOME}/authorized_keys || true && \
    chown ${user}:${user} ${SSH_HOME}/* && \
    chmod 600 ${SSH_HOME}/*

#gitconfig
COPY .gitconfig /home/${user}/.gitconfig

EXPOSE 22 5000 4005
CMD ["/usr/sbin/sshd", "-D"]
