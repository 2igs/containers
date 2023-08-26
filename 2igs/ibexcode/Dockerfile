# Create ibexcode image which is based on code-server
FROM node:20-bookworm AS ibexcode
ENV VERSION="4.16.1"
USER root
WORKDIR root
## Update npm
RUN npm install -g npm@latest
# ## Get code-server
RUN mkdir -p .local/lib .local/bin
RUN curl -fL https://github.com/coder/code-server/releases/download/v$VERSION/code-server-$VERSION-linux-amd64.tar.gz \
  | tar -C /root/.local/lib -xz \
  && mv /root/.local/lib/code-server-$VERSION-linux-amd64 /root/.local/lib/code-server-$VERSION \
  && ln -s /root/.local/lib/code-server-$VERSION/bin/code-server /root/.local/bin/code-server \
  ;
# Add code-server to PATH
RUN printf "export PATH=\"$PATH:%s\"" "/root/.local/bin" >> .bashrc
## Configure code-server
COPY code-server/settings.json /root/.local/share/code-server/Machine/settings.json
## Install system tools
RUN apt update && apt install mold nano python3-ipykernel podman curl -y && apt clean
## Set code-server command
ENTRYPOINT [ "/bin/bash", "--login", "-c" ]
CMD ["code-server --auth=none --bind-addr=0.0.0.0"]

# Add rust components
## Install rust
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
## Configure rust
COPY rust/config.toml rust/rustfmt.toml /root/.cargo
## Set cargo binaries environment variables
ENV PATH="$PATH:/root/.cargo/bin"

# Add Kubernetes tools
## Install helm
RUN wget https://get.helm.sh/helm-v3.12.3-linux-arm64.tar.gz \
  && tar -zxvf helm-v3.12.2-linux-amd64.tar.gz \
  && mv linux-amd64/helm /usr/local/bin \
  && rm -rf helm-v3.12.2-linux-amd64.tar.gz linux-amd64 \
  ;
## Install OPA(Open Policy Agent)
RUN curl -L -o opa https://openpolicyagent.org/downloads/v0.55.0/opa_linux_amd64_static \
&&  chmod +x opa && mv opa /usr/local/bin
## Install gator
RUN curl -fL https://github.com/open-policy-agent/gatekeeper/releases/download/v3.13.0/gator-v3.13.0-linux-amd64.tar.gz \
| tar -C /usr/local/bin -xz
## Install konstraint
RUN curl -L -o konstraint https://github.com/plexsystems/konstraint/releases/download/v0.30.0/ \
&& chmod +x konstraint && mv konstraint /usr/local/bin
## Install kubectl
RUN curl -L -o kubectl https://dl.k8s.io/release/v1.28.0/bin/linux/amd64/kubectl \
&& chmod +x kubectl && mv kubectl /usr/local/bin