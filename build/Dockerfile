FROM s390x/ubuntu:20.04

# see https://gist.github.com/skaji/76203327b517cb44da88a4301de118d3
#RUN sed -i 's/override_install_langs=en_US.UTF-8/override_install_langs=en_US.utf8/' /etc/yum.conf

# Update package list and install dependencies
RUN apt-get update && apt-get install -y \
    gcc \
    make \
    tar \
    curl \
    patch \
    xz-utils

RUN mkdir -p \
    /lib \
    /lib/$(uname -m)-linux-gnu \
    /lib64 \
    /usr/lib \
    /usr/lib/$(uname -m)-linux-gnu \
    /usr/lib64 \
    /usr/local/lib \
    /usr/local/lib64
#RUN curl -fsSL https://raw.githubusercontent.com/skaji/relocatable-perl/main/perl-install | bash -s /perl
#RUN curl -fsSL https://www.cpan.org/src/5.0/perl-5.36.0.tar.xz | tar xJf - --strip-components 1 -C /perl
RUN curl -fsSL https://www.cpan.org/src/5.0/perl-5.40.0.tar.gz -o perl.tar.gz \
    && tar -xzf perl.tar.gz \
    && cd perl-5.40.0 \
    && ./Configure -des -Dprefix=/usr/local/perl \
    && make \
    && make install \
    && rm -rf /perl.tar.gz perl-5.40.0
RUN curl -fsSL --compressed -o /cpm https://raw.githubusercontent.com/skaji/cpm/main/cpm
COPY build/relocatable-perl-build build/cpm.yml BUILD_VERSION /
RUN /usr/bin/perl /cpm install -g
RUN /usr/bin/perl /relocatable-perl-build --perl_version $(cat /BUILD_VERSION) --prefix /opt/perl
RUN /opt/perl/bin/perl /cpm install -g App::cpanminus App::ChangeShebang
RUN /opt/perl/bin/change-shebang -f /opt/perl/bin/*
RUN set -eux; \
  cd /tmp; \
  #_ARCHNAME=$(if [[ $(uname -m) = x86_64 ]]; then echo amd64; else echo arm64; fi); \
  _ARCHNAME=$(uname -m); \
  cp -r /opt/perl perl-linux-$_ARCHNAME; \
  tar cf perl-linux-$_ARCHNAME.tar perl-linux-$_ARCHNAME; \
  gzip -9 --stdout perl-linux-$_ARCHNAME.tar > /perl-linux-$_ARCHNAME.tar.gz; \
  xz   -9 --stdout perl-linux-$_ARCHNAME.tar > /perl-linux-$_ARCHNAME.tar.xz; \
  :
