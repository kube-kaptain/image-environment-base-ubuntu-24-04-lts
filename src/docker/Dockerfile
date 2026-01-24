FROM ubuntu:noble-20260113
# Always use the most specific tag possible

# Key tools apt can't provide
ENV KUBECTL_VERSION=1.34.3
ENV YQ4_VERSION=4.50.1

# Strong checksums to match the above
ENV KUBECTL_SHA512=6e6011b64bacce24d0c63e92e97e6125bfadfe16352cc0bf96497dc41881c485817b299fa3efd95d3e6744c47ce6e2c4496f28c240545b441596de9f1b126b29
ENV YQ4_SHA512=b1043cf55e440e7bd45c9c74b7bd2cdac76bbe9d6de6aff5eaa41159cc6c20c9225d9f7a80874f124a3e55b5732303e6f6164ed7aa7d32fa0d4077cb261782ce

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
    dnsutils \
    iputils-ping \
    netcat-openbsd \
    procps \
    time \
    -y --no-install-recommends
RUN apt-get clean
RUN rm -rf /var/lib/apt/lists/*

# Install kubectl
RUN curl -sLo /usr/local/bin/kubectl "https://dl.k8s.io/release/v${KUBECTL_VERSION}/bin/linux/amd64/kubectl"
RUN sha512sum /usr/local/bin/kubectl
RUN echo "${KUBECTL_SHA512}  /usr/local/bin/kubectl" | sha512sum -c -
RUN chmod +x /usr/local/bin/kubectl

# Install yq 4
RUN curl -sLo /usr/local/bin/yq "https://github.com/mikefarah/yq/releases/download/v${YQ4_VERSION}/yq_linux_amd64"
RUN sha512sum /usr/local/bin/yq
RUN echo "${YQ4_SHA512}  /usr/local/bin/yq" | sha512sum -c -
RUN chmod +x /usr/local/bin/yq

# Create deployment user
RUN mkdir -p /home/kaptain
RUN groupadd -g 4242 kaptain
RUN useradd -u 4242 -g kaptain -d /home/kaptain -s /bin/bash kaptain
RUN chown kaptain:kaptain /home/kaptain

# Create directory structure for deployment needs
RUN mkdir -p /run/bin        # Deployment scripts
RUN mkdir -p /run/manifests  # Env declaration manifests
RUN mkdir -p /run/secrets    # Encrypted secret values
RUN mkdir -p /run/work       # Working dir for processing, logging, etc

# Put the deploy scripts on the path so we can run them naturally
ENV PATH="/run/bin:${PATH}"

# Make the working dir writeable and set it as the workdir
RUN chown kaptain:kaptain /run/work
WORKDIR /run/work

ENV ENVIRONMENT="NOT_SET"
ENV PS1="\u@\h:\w \${ENVIRONMENT}\\$ "

# No entry point or command since end use can take multiple forms
