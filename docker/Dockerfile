FROM ubuntu:24.04

LABEL maintainer="YOSHIMRUA Hikaru <yyu [at] mental.poker>"

RUN apt-get update

# Install basic dependencies
ENV PERSISTENT_DEPS \
    wget \
    make \
    perl \
    ghostscript \
    bash \
    git \
    tar \
    fontconfig \
    ca-certificates
RUN apt-get install -y --no-install-recommends $PERSISTENT_DEPS

# Set timezone to Tokyo
ENV TZ=Asia/Tokyo
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y tzdata && \
    ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone \
    dpkg-reconfigure --frontend noninteractive tzdata

# Install TeXLive
ARG TEXLIVE_OPTIONS=""
ENV TEXLIVE_PATH /usr/local/texlive
ARG TARGETARCH
RUN mkdir /tmp/install-tl-unx && \
    wget -qO- http://mirror.ctan.org/systems/texlive/tlnet/install-tl-unx.tar.gz | \
      tar -xz -C /tmp/install-tl-unx --strip-components=1 && \
    printf "%s\n" \
      "TEXDIR $TEXLIVE_PATH" \
      "selected_scheme scheme-small" \
      "option_doc 0" \
      "option_src 0" \
      > /tmp/install-tl-unx/texlive.profile && \
    if [[ "$TARGETARCH" = "arm64" ]]; then \
      /tmp/install-tl-unx/install-tl \
        -profile /tmp/install-tl-unx/texlive.profile \
        $TEXLIVE_OPTIONS \
        -force-platform aarch64-linux; \
    else \
      /tmp/install-tl-unx/install-tl \
        -profile /tmp/install-tl-unx/texlive.profile \
        $TEXLIVE_OPTIONS; \
    fi

ENV PATH $TEXLIVE_PATH/bin/aarch64-linux:$TEXLIVE_PATH/bin/x86_64-linux:/bin:$PATH
RUN tlmgr install latexmk collection-luatex collection-langjapanese \
      collection-fontsrecommended type1cm mdframed needspace fontaxes \
      boondox everyhook svn-prov framed placeins adjustbox \
      collectbox fncychap sourcesanspro sourceserifpro cm-unicode \
      zref && \
    luaotfload-tool -u -vvv && \
    rm -fr /tmp/install-tl-unx && \
    apt-get -y clean

VOLUME ["/workdir"]

WORKDIR /workdir

ENTRYPOINT ["/bin/bash", "-c", "make japanese"]
