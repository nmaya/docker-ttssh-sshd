FROM ghcr.io/nmaya/centos6-vault:latest


RUN yum install -y rpm-build && \
    useradd -s /sbin/nologin mockbuild && \
    yum install -y wget


RUN cd /root && \
    wget --no-check-certificate https://vault.centos.org/6.10/os/Source/SPackages/python-twisted-conch-8.2.0-3.2.el6.src.rpm && \
    rpm -ivh python-twisted-conch-8.2.0-3.2.el6.src.rpm

# blowfish-ctr is 256bit, not 128bit.
COPY ./TwistedConch-8.2.0-blowfish-ctr.patch /root/rpmbuild/SOURCES/

RUN yum install -y python-devel python-twisted-core python-crypto tkinter

RUN cd /root/rpmbuild/SPECS/ && \
    sed -i "/^Source0/aPatch0:         TwistedConch-8.2.0-blowfish-ctr.patch" \
        python-twisted-conch.spec && \
    sed -i "/^# Fix doc file dependencies/i%patch0 -p0 -b .blowfish-ctr\n" \
        python-twisted-conch.spec && \
    rpmbuild -bb python-twisted-conch.spec

RUN cd /root/rpmbuild/RPMS/x86_64/ && \
    rpm -ivh python-twisted-conch-8.2.0-3.2.el6.x86_64.rpm

RUN cd /root && \
    wget --no-check-certificate https://github.com/twisted/twisted/archive/refs/tags/twisted-8.2.0.tar.gz && \
    tar zxvf twisted-8.2.0.tar.gz && \
    cp twisted-twisted-8.2.0/doc/conch/examples/sshsimpleserver.py . && \
    sed -i -e "s/'user', 'password'/'ttssh2', 'ttssh2'/" \
        sshsimpleserver.py


EXPOSE 5022
