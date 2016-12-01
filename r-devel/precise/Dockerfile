## Emacs, make this -*- mode: sh; -*-

## start with the Docker 'base R' Debian-based image
FROM rocker/r-apt:precise

## This handle reaches Carl and Dirk
MAINTAINER "Carl Boettiger and Dirk Eddelbuettel" rocker-maintainers@eddelbuettel.com

## Remain current
RUN apt-get update -qq \
	&& apt-get dist-upgrade -y

## From the Build-Depends of the Debian R package, plus subversion
RUN apt-get update -qq \
	&& apt-get install -y --no-install-recommends \
		bash-completion \
		bison \
		debhelper \
		default-jdk \
		g++ \
		gcc \
		gdb \
		gfortran \
		groff-base \
		libblas-dev \
		libbz2-dev \
		libcairo2-dev \
		libcurl4-openssl-dev \
		libjpeg-dev \
		liblapack-dev \
		liblzma-dev \
		libncurses5-dev \
		libpango1.0-dev \
		libpcre3-dev \
		libpng-dev \
		libreadline-dev \
		libx11-dev \
		libxt-dev \
		mpack \
		subversion \
		tcl-dev \
		texinfo \
		texlive-base \
		texlive-extra-utils \
		texlive-fonts-extra \
		texlive-fonts-recommended \
		texlive-generic-recommended \
		texlive-latex-base \
		texlive-latex-extra \
		texlive-latex-recommended \
		tk-dev \
		x11proto-core-dev \
		xauth \
		xdg-utils \
		xfonts-base \
		xvfb \
		zlib1g-dev

## Install local verson of zlib, curl and pcre
ENV CURL_VERSION 7.43.0
ENV ZLIB_VERSION 1.2.8
ENV PCRE_VERSION 8.38
ENV PREFIX /opt/R-bin

## Check out R-devel
RUN cd /tmp \
			&& svn co https://svn.r-project.org/R/trunk R-devel

RUN cd /tmp/R-devel \
			&& mkdir -p extra \
			&& cd extra \
			&& wget --no-check-certificate http://zlib.net/zlib-$ZLIB_VERSION.tar.gz \
			&& tar xzf zlib-$ZLIB_VERSION.tar.gz \
			&& cd "zlib-$ZLIB_VERSION" \
			&& ./configure --prefix=$PREFIX \
			&& make \
			&& make install \
			&& cd - \
			&& wget --no-check-certificate "http://curl.haxx.se/download/curl-$CURL_VERSION.tar.gz" \
			&& tar zxf "curl-$CURL_VERSION.tar.gz" \
			&& cd "curl-$CURL_VERSION" \
			&& ./configure --prefix=$PREFIX \
			&& make \
			&& make install \
			&& cd - \
			&& wget https://sourceforge.net/projects/pcre/files/pcre/8.38/pcre-$PCRE_VERSION.tar.bz2 \
			&& tar xjf pcre-$PCRE_VERSION.tar.bz2 \
			&& cd pcre-$PCRE_VERSION \
			&& ./configure --prefix=$PREFIX --enable-utf --enable-unicode-properties \
			&& make \
			&& make install


## Build and install according the standard 'recipe' I emailed/posted years ago
RUN cd /tmp/R-devel \
	&& R_PAPERSIZE=letter \
		R_BATCHSAVE="--no-save --no-restore" \
		R_BROWSER=xdg-open \
		PAGER=/usr/bin/pager \
		PERL=/usr/bin/perl \
		R_UNZIPCMD=/usr/bin/unzip \
		R_ZIPCMD=/usr/bin/zip \
		R_PRINTCMD=/usr/bin/lpr \
		LIBnn=lib \
		AWK=/usr/bin/awk \
		CFLAGS=$(R CMD config CFLAGS) \
		CXXFLAGS=$(R CMD config CXXFLAGS) \
	./configure  CPPFLAGS=-I$PREFIX/include \
               LDFLAGS=-L$PREFIX/lib \
               --without-blas \
               --without-lapack \
               --with-readline \
               --without-recommended-packages \
               --program-suffix=dev \
	&& make \
	&& make install \
	&& rm -rf /tmp/R-devel

## Set Renviron to get libs from base R install
RUN echo "R_LIBS=\${R_LIBS-'/usr/local/lib/R/site-library:/usr/local/lib/R/library:/usr/lib/R/library'}" >> /usr/local/lib/R/etc/Renviron

## Set default CRAN repo
RUN echo 'options(repos = c(CRAN = "https://cran.rstudio.com/"), download.file.method = "libcurl")' >> /usr/local/lib/R/etc/Rprofile.site

RUN ln -s /usr/local/bin/RD $PREFIX/bin/R && ln -s /usr/local/bin/RDscript $PREFIX/bin/Rscript
