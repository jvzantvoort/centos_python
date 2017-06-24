#===============================================================================
#
#         FILE: centos_python/2.7/centos-7.3/Dockerfile
#
#  DESCRIPTION:  Derived from the upstream python:2.7.13 but built for centos7
#                and added make test to the build.
#
#       AUTHOR:  John van Zantvoort (jvzantvoort), John@vanZantvoort.org
#      CREATED:  24-Jun-2017
#===============================================================================

FROM centos:7.3.1611
MAINTAINER John van Zantvoort "John@vanzantvoort.org"

ENV PATH /usr/local/bin:$PATH
ENV LANG C

RUN set -x && yum makecache fast && yum -y -q groupinstall "Development Tools" && yum clean all

# gpg: key 18ADD4FF: public key "Benjamin Peterson <benjamin@python.org>" imported
# RUN gpg --keyserver ha.pool.sks-keyservers.net --recv-keys C01E1CAD5EA2C4F0B8E3571504C367C218ADD4FF
ENV GPG_KEY C01E1CAD5EA2C4F0B8E3571504C367C218ADD4FF
ENV PYTHON_VERSION 2.7.13

RUN set -x && yum makecache fast && \
        yum -y -q install \
                  binutils-devel \
                  bzip2 \
                  bzip2-devel \
                  expat-devel \
                  findutils \
                  gcc-c++ \
                  gdbm-devel \
                  glibc-devel \
                  gmp-devel \
                  libdb-devel \
                  libffi-devel \
                  libGL-devel \
                  libX11-devel \
                  libxml2-devel \
                  ncurses-devel \
                  openssl-devel \
                  pkgconfig \
                  readline-devel \
                  sqlite-devel \
                  tar \
                  zlib-devel \
        \
	&& curl -SL "https://www.python.org/ftp/python/$PYTHON_VERSION/Python-$PYTHON_VERSION.tar.xz" -o python.tar.xz \
	&& curl -SL "https://www.python.org/ftp/python/$PYTHON_VERSION/Python-$PYTHON_VERSION.tar.xz.asc" -o python.tar.xz.asc \
	&& export GNUPGHOME="$(mktemp -d)" \
	&& gpg --keyserver ha.pool.sks-keyservers.net --recv-keys "$GPG_KEY" \
	&& gpg --batch --verify python.tar.xz.asc python.tar.xz \
	&& rm -rf "$GNUPGHOME" python.tar.xz.asc \
	&& mkdir -p /usr/src/python \
	&& tar -xJC /usr/src/python --strip-components=1 -f python.tar.xz \
	&& rm python.tar.xz \
	&& cd /usr/src/python \
	&& ./configure --enable-shared --enable-unicode=ucs4 \
	&& make -j$(nproc) \
	&& make install \
	&& ldconfig \
	&& yum clean all

# added a skip patch to the source because that test relies on the kernel.
ADD python_test_gdb.patch /tmp/python_test_gdb.patch
RUN set -xv \
	&& cd /usr/src/python \
	&& patch -p 0 < /tmp/python_test_gdb.patch \
	&& make test

#NOTE: unlike the upstream version we keep the develeopment pacakges and
#testfiles for now.

# if this is called "PIP_VERSION", pip explodes with "ValueError: invalid truth value '<VERSION>'"
ENV PYTHON_PIP_VERSION 9.0.1

# install "virtualenv", since the vast majority of users of this image will want it
RUN set -ex; \
	\
	curl -SL -o get-pip.py 'https://bootstrap.pypa.io/get-pip.py'; \
	\
	python get-pip.py \
		--disable-pip-version-check \
		--no-cache-dir \
		"pip==$PYTHON_PIP_VERSION" \
	; \
	pip --version; \
	rm -f get-pip.py; \
	pip install --no-cache-dir virtualenv

CMD ["/bin/bash"]
