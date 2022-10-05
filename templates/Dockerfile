FROM ubuntu:latest
SHELL ["/bin/bash", "-c"]

ARG TZ="UTC"
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && \
    echo $TZ > /etc/timezone

WORKDIR /usr/src/app

ADD . .
RUN if [ -z "$(ls plugins/stockpile)" ]; then echo "stockpile plugin not downloaded - please ensure you recursively cloned the caldera git repository and try again."; exit 1; fi

RUN su root && echo "nameserver 8.8.4.4" | tee -a /etc/resolv.conf
RUN apt-get update && \ 
RUN apt-get -y install python3 python3-pip git curl && \
RUN apt-get -y install mingw-w64 && \
RUN pip3 install --no-cache-dir -r requirements.txt && \
RUN grep -v "\- atomic" conf/default.yml > conf/local.yml
RUN curl -L https://go.dev/dl/go1.17.6.linux-amd64.tar.gz -o go1.17.6.linux-amd64.tar.gz
RUN rm -rf /usr/local/go && tar -C /usr/local -xzf go1.17.6.linux-amd64.tar.gz;
ENV PATH="${PATH}:/usr/local/go/bin"
RUN go version;
WORKDIR /usr/src/app/plugins/sandcat
RUN ./update-agents.sh
RUN mkdir /tmp/gocatextensionstest
RUN cp -R ./gocat /tmp/gocatextensionstest/gocat
RUN cp -R ./gocat-extensions/* /tmp/gocatextensionstest/gocat/
RUN cp ./update-agents.sh /tmp/gocatextensionstest/update-agents.sh
WORKDIR /tmp/gocatextensionstest
RUN mkdir /tmp/gocatextensionstest/payloads
RUN ./update-agents.sh
RUN if [ ! -d "/usr/src/app/plugins/atomic/data/atomic-red-team" ]; then   \
    git clone --depth 1 https://github.com/redcanaryco/atomic-red-team.git \
        /usr/src/app/plugins/atomic/data/atomic-red-team;                  \
fi

WORKDIR /usr/src/app
EXPOSE 8888
EXPOSE 8443
EXPOSE 7010
EXPOSE 7011/udp
EXPOSE 7012
EXPOSE 8853
EXPOSE 8022
EXPOSE 2222
ENTRYPOINT ["python3", "server.py"]

