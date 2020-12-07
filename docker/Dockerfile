FROM debian:buster-slim
LABEL maintainer="Franklin Escobar"
LABEL github="https://github.com/franklines"

RUN apt-get update && \
    apt-get install -y \
    procps \
    python3 \
    python3-pip \
    curl

RUN curl --silent --location \
    "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp && \ 
    mv /tmp/eksctl /usr/local/bin && \
    chmod +x /usr/local/bin/eksctl && \
    pip3 install kube-hunter && \
    mkdir /etc/eks/

COPY eks/eks.pub /etc/eks/eks.pub
COPY eks/cluster.yml /etc/eks/cluster.yml

CMD ["tail", "-f", "/dev/null"]