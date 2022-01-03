FROM ghcr.io/nmaya/centos6-vault:latest


RUN yum install -y rpm-build && \
    useradd -s /sbin/nologin mockbuild && \
    yum install -y gcc && \
    yum install -y wget


RUN cd /root && \
    wget --no-check-certificate https://vault.centos.org/6.10/os/Source/SPackages/openssh-5.3p1-123.el6_9.src.rpm && \
    rpm -ivh openssh-5.3p1-123.el6_9.src.rpm

# https://bugzilla.mindrot.org/show_bug.cgi?id=1340
RUN cd /root/rpmbuild/SOURCES/ && \
    wget --no-check-certificate https://bugzilla.mindrot.org/attachment.cgi?id=1902 -O openssh-4.7p1-camellia.patch && \
    sed -i -e 's/^ struct Cipher {/ struct Cipher ciphers[] = {/' \
        /root/rpmbuild/SOURCES/openssh-4.7p1-camellia.patch

COPY ./openssh-5.3p1-ssh1-des.patch /root/rpmbuild/SOURCES/

RUN yum install -y autoconf automake zlib-devel \
                   audit-libs-devel util-linux man pam-devel \
                   tcp_wrappers-devel fipscheck-devel openssl-devel \
                   openldap-devel krb5-devel libedit-devel \
                   ncurses-devel nss-devel libselinux-devel xauth\
                   gtk2-devel libX11-devel

RUN cd /root/rpmbuild/SPECS/ && \
    sed -i "/openssh-5.3p1-CVE-2016-6210.patch/a# Camellia support\nPatch171: openssh-4.7p1-camellia.patch" \
        openssh.spec && \
    sed -i "/%patch170 -p1 -b .user-enumeration/a%patch171 -p0 -b .camellia" \
        openssh.spec && \
    sed -i "/openssh-4.7p1-camellia.patch/a# Enable DES on SSH1\nPatch172: openssh-5.3p1-ssh1-des.patch" \
        openssh.spec && \
    sed -i "/%patch171 -p0 -b .camellia/a%patch172 -p0 -b .ssh1-des" \
        openssh.spec && \
    rpmbuild -bb openssh.spec

RUN cd /root/rpmbuild/RPMS/x86_64/ && \
    rpm -ivh openssh-5.3p1-123.el6.x86_64.rpm openssh-server-5.3p1-123.el6.x86_64.rpm

RUN cd /etc/ssh/ && \
    sed -i -e 's/^Protocol 2/Protocol 2,1/' \
        sshd_config && \
    sed -i "/# Example of overriding settings on a per-user basis$/iCiphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc,blowfish-cbc,cast128-cbc,aes192-cbc,aes256-cbc,arcfour,camellia128-cbc,camellia192-cbc,camellia256-cbc,camellia128-ctr,camellia192-ctr,camellia256-ctr\n" \
        sshd_config && \
    chkconfig sshd on

RUN useradd -m ttssh2 && \
    echo 'ttssh2:ttssh2' | chpasswd


EXPOSE 22
