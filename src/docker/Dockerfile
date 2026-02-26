FROM ubuntu:noble-20260113
# Always use the most specific tag possible

# Architecture from build system (used for binary downloads)
ARG DOCKER_PLATFORM_CPU_ARCHITECTURE

# Key tools apt can't provide
ENV KUBECTL_VERSION=1.34.3
ENV YQ4_VERSION=4.50.1
ENV AGE_VERSION=1.3.1

# Strong checksums to match the above (per architecture)
ENV KUBECTL_SHA512_amd64=6e6011b64bacce24d0c63e92e97e6125bfadfe16352cc0bf96497dc41881c485817b299fa3efd95d3e6744c47ce6e2c4496f28c240545b441596de9f1b126b29
ENV KUBECTL_SHA512_arm64=6babfa4e3897f42baecab73cd9f678e64202a36fc9f81985ede80d19b12e72591a806d6d3c0cb87a77cd39f8cb9191c5457a462795c45fa1984f02ad5019f0f1
ENV YQ4_SHA512_amd64=b1043cf55e440e7bd45c9c74b7bd2cdac76bbe9d6de6aff5eaa41159cc6c20c9225d9f7a80874f124a3e55b5732303e6f6164ed7aa7d32fa0d4077cb261782ce
ENV YQ4_SHA512_arm64=b7b71e9911eb39594aca025f211de2790ba795690b5ac9e26db951f24f1b0bb1358ef7f1b3b6b5bbf4c276e251e906cab6138b621d49803a946f8566ce3c6b01
ENV AGE_SHA512_amd64=425e3f7bcd3bee2ac5e356d45f86b208a15a4ddf0c5fe512047fbe08d77a4b957b3c5db699c49c281bd20f8bc1e76ab93753ab99593ff747742808769dccc070
ENV AGE_SHA512_arm64=7f7920f5f199e46b568bec464e870fd61baae67140ddb5ed104c458ee89cdfbb7c4f48873cfa73c1218dc2a023227e7bacf635480e678a480aeac04e0f330b1f

# Leave a trail of breadcrumbs
LABEL base.project.name="${ProjectName}"
LABEL base.version="${Version}"
LABEL base.docker.tag="${DockerTag}"
LABEL base.docker.image.name="${DockerImageName}"

# Install necessary tools and update packages
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update
RUN apt-get dist-upgrade -y
RUN apt-get install \
    ca-certificates \
    curl \
    debianutils \
    dnsutils \
    iputils-ping \
    netcat-openbsd \
    openssl \
    procps \
    time \
    -y --no-install-recommends
RUN apt-get clean
RUN rm -rf /var/lib/apt/lists/*

# Install kubectl
RUN curl -sLo /usr/local/bin/kubectl "https://dl.k8s.io/release/v${KUBECTL_VERSION}/bin/linux/${DOCKER_PLATFORM_CPU_ARCHITECTURE}/kubectl"
RUN sha512sum /usr/local/bin/kubectl
RUN eval CHECKSUM=\$KUBECTL_SHA512_${DOCKER_PLATFORM_CPU_ARCHITECTURE} && \
    echo "${CHECKSUM}  /usr/local/bin/kubectl" | sha512sum -c -
RUN chmod +x /usr/local/bin/kubectl

# Install yq 4
RUN curl -sLo /usr/local/bin/yq "https://github.com/mikefarah/yq/releases/download/v${YQ4_VERSION}/yq_linux_${DOCKER_PLATFORM_CPU_ARCHITECTURE}"
RUN sha512sum /usr/local/bin/yq
RUN eval CHECKSUM=\$YQ4_SHA512_${DOCKER_PLATFORM_CPU_ARCHITECTURE} && \
    echo "${CHECKSUM}  /usr/local/bin/yq" | sha512sum -c -
RUN chmod +x /usr/local/bin/yq

# Install age (pinned version, not from distro repos)
RUN curl -sLo /tmp/age.tar.gz "https://github.com/FiloSottile/age/releases/download/v${AGE_VERSION}/age-v${AGE_VERSION}-linux-${DOCKER_PLATFORM_CPU_ARCHITECTURE}.tar.gz"
RUN sha512sum /tmp/age.tar.gz
RUN eval CHECKSUM=\$AGE_SHA512_${DOCKER_PLATFORM_CPU_ARCHITECTURE} && \
    echo "${CHECKSUM}  /tmp/age.tar.gz" | sha512sum -c -
RUN tar xzf /tmp/age.tar.gz -C /usr/local/bin --strip-components=1 age/age age/age-keygen
RUN rm /tmp/age.tar.gz

# Create deployment user
ENV KAPTAIN_USER_ID=4242
RUN mkdir -p /home/kaptain
RUN groupadd -g ${KAPTAIN_USER_ID} kaptain
RUN useradd -u ${KAPTAIN_USER_ID} -g kaptain -d /home/kaptain -s /bin/bash kaptain
RUN chown kaptain:kaptain /home/kaptain

# Create directory structure for deployment needs
RUN mkdir -p /kd/bin        # Deployment scripts
RUN mkdir -p /kd/manifests  # Env declaration manifests
RUN mkdir -p /kd/secrets    # Encrypted secret values
RUN mkdir -p /kd/work       # Working dir for processing, logging, etc

# Put the deploy scripts on the path so we can run them naturally
ENV PATH="/kd/bin:${PATH}"

# Make the working dir writeable and set it as the workdir
RUN chown kaptain:kaptain /kd/work
WORKDIR /kd/work

ENV ENVIRONMENT="NOT_SET"
ENV PS1="\u@\h:\w \${ENVIRONMENT}\\$ "

# No entry point or command since end use can take multiple forms
