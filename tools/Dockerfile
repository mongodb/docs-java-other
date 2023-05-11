FROM ubuntu:latest

LABEL maintainer="chris.cho@mongodb.com"
LABEL version="0.1"
LABEL description="JVM driver GitHub pages publishing repo with hugo 0.25.x."

WORKDIR /usr/local/docs

ENV PATH /usr/local/docs/:$PATH


RUN apt update

RUN apt install -y wget git vim make gnupg openjdk-11-jdk && \
    rm -rf /var/lib/apt/lists/* && \
    apt clean

RUN wget https://github.com/gohugoio/hugo/releases/download/v0.25.1/hugo_0.25.1_Linux-ARM64.tar.gz && tar -xzvf hugo_0.25.1_Linux-ARM64.tar.gz

RUN git clone https://github.com/mongodb/docs-java-other.git
RUN git clone https://github.com/mongodb/mongo-java-driver.git

