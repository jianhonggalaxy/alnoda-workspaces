FROM jianhonglee783/ubuntu-workspace:24.04

USER root

RUN echo "------------------------------------------------------ Docker" && \
    apt-get update && \
    apt-get install -y apt-transport-https ca-certificates gnupg lsb-release && \
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg && \
    echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list && \
    apt-get update && \
    apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin && \
    echo "------------------------------------------------------ Lazydocker" && \
    wget -P /tmp https://github.com/jesseduffield/lazydocker/releases/download/v0.16.7/lazydocker_0.16.7_Linux_x86.tar.gz && \
    mkdir /tmp/lazydocker && tar -xzf /tmp/lazydocker_0.16.7_Linux_x86.tar.gz -C /tmp/lazydocker && \
    chmod +x /tmp/lazydocker/lazydocker && mv /tmp/lazydocker/lazydocker /usr/bin/ && \
    rm /tmp/lazydocker_0.16.7_Linux_x86.tar.gz && rm -rf /tmp/lazydocker && \
    echo "------------------------------------------------------ Sen" && \
    pip install sen==0.6.2 && \
    echo "------------------------------------------------------ Dive" && \
    cd /tmp && wget https://github.com/wagoodman/dive/releases/download/v0.10.0/dive_0.10.0_linux_amd64.deb && \
    apt-get install -y /tmp/dive_0.10.0_linux_amd64.deb && rm /tmp/dive_0.10.0_linux_amd64.deb

USER abc
