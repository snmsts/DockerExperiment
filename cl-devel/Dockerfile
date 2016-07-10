FROM ubuntu:trusty
MAINTAINER SANO Masatoshi

ENV PATH /root/.roswell/bin:/usr/local/bin:$PATH

RUN apt-get update && \
    apt-get -y install libcurl4-gnutls-dev git build-essential automake autoconf \
    openssh-server emacs24-nox && \
    apt-get autoremove && apt-get clean

#sshd
RUN mkdir /var/run/sshd && \
    echo 'root:root' | chpasswd && \
    sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config && \
    sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd
ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile

COPY .ssh/ /root/.ssh/

## install roswell
WORKDIR /usr/local/src
RUN git clone -b release https://github.com/roswell/roswell
WORKDIR /usr/local/src/roswell
RUN sh ./bootstrap && \
       ./configure && \
       make && \
       make install && \
       rm -rf /usr/local/src/roswell

RUN ros setup && \
    ros emacs --batch && \
    rm -rf /root/.roswell/src/ /root/.roswell/tmp/

EXPOSE 22 5000 4005
CMD ["/usr/sbin/sshd", "-D"]
