FROM ubuntu:24.04

# Set environment variables for noninteractive installs and pip override.
ENV DEBIAN_FRONTEND=noninteractive
ENV PIP_BREAK_SYSTEM_PACKAGES=1

# Copy configuration files and scripts.
COPY unified-supervisord.conf supervisord.conf /etc/supervisord/
COPY mc.ini /home/abc/.config/mc/ini
COPY zsh-in-docker.sh /tmp/zsh-in-docker.sh

# Install common packages and dependencies.
RUN apt-get update && \
    apt-get install -y \
      sudo jq git \
      curl wget telnet netcat-traditional dnsutils \
      software-properties-common \
      zip gzip tar \
      gdebi-core acl psmisc \
      tzdata openssl \
      python3-pip python3-setuptools python-is-python3 \
      yarn cron supervisor systemd \
      nano vim tilde mc tmux ncdu htop && \
    rm -rf /var/lib/apt/lists/*

# Configure timezone.
RUN ln -fs /usr/share/zoneinfo/Asia/Singapore /etc/localtime && \
    apt-get update && apt-get install --reinstall -y tzdata && rm -rf /var/lib/apt/lists/*

# Create user 'abc' and setup home directories.
RUN useradd -u 8877 -p "$(openssl passwd -1 abc)" abc && \
    mkdir -p /home/abc/apps && \
    chown -R abc:abc /home && \
    setfacl -m u:abc:rwx /etc

# Create Nix folder.
RUN mkdir -m 0750 /nix && chown abc:abc /nix

# Download docker systemctl replacement.
RUN wget https://raw.githubusercontent.com/gdraheim/docker-systemctl-replacement/master/files/docker/systemctl3.py -O /usr/local/bin/systemctl && \
    chmod +x /usr/local/bin/systemctl && \
    chown abc:abc /usr/local/bin/systemctl

# Upgrade required Python packages (excluding pip) and install nodeenv.
RUN pip install --upgrade setuptools --break-system-packages && \
    pip install --upgrade distlib --break-system-packages && \
    pip install nodeenv --break-system-packages

# Add abc user to sudoers.
RUN usermod -a -G sudo abc && \
    echo "abc ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

# Configure Cron and Supervisor.
RUN mkdir -p /var/log/supervisord/ && \
    chown -R abc:abc /var/spool/cron/crontabs /var/log /etc/supervisord/ && \
    chmod gu+rw /var/run && \
    chmod gu+s /usr/sbin/cron && \
    apt-get remove -y cmdtest && \
    pip3 install supervisor==4.2.2 --break-system-packages

# Configure ZSH for both root and abc users.
RUN chmod +x /tmp/zsh-in-docker.sh && \
    HOME=/root /tmp/zsh-in-docker.sh \
      -t https://github.com/pascaldevink/spaceship-zsh-theme \
      -a 'SPACESHIP_PROMPT_ADD_NEWLINE="false"' \
      -a 'SPACESHIP_PROMPT_SEPARATE_LINE="false"' \
      -a 'export LS_COLORS="$LS_COLORS:ow=1;34:tw=1;34:"' \
      -a 'SPACESHIP_USER_SHOW="false"' \
      -a 'SPACESHIP_TIME_SHOW="true"' \
      -a 'SPACESHIP_TIME_COLOR="grey"' \
      -a 'SPACESHIP_DIR_COLOR="cyan"' \
      -a 'SPACESHIP_GIT_SYMBOL="⇡"' \
      -a 'SPACESHIP_BATTERY_SHOW="false"' \
      -a 'if [[ $(pwd) != /root ]]; then cd /root; fi' \
      -a 'hash -d r=/root' \
      -p git \
      -p https://github.com/zsh-users/zsh-autosuggestions \
      -p https://github.com/zsh-users/zsh-completions \
      -p https://github.com/zsh-users/zsh-history-substring-search \
      -p https://github.com/zsh-users/zsh-syntax-highlighting \
      -p 'history-substring-search' \
      -p https://github.com/bobthecow/git-flow-completion \
      -a 'bindkey "\$terminfo[kcuu1]" history-substring-search-up' \
      -a 'bindkey "\$terminfo[kcud1]" history-substring-search-down' && \
    printf '%s\n%s\n' "export ZSH_DISABLE_COMPFIX=true" "$(cat /root/.zshrc)" > /root/.zshrc && \
    mkdir -p /home/project && \
    HOME=/home/abc /tmp/zsh-in-docker.sh \
      -t https://github.com/pascaldevink/spaceship-zsh-theme \
      -a 'DISABLE_UPDATE_PROMPT="true"' \
      -a 'SPACESHIP_PROMPT_ADD_NEWLINE="false"' \
      -a 'SPACESHIP_PROMPT_SEPARATE_LINE="false"' \
      -a 'export LS_COLORS="$LS_COLORS:ow=1;34:tw=1;34:"' \
      -a 'SPACESHIP_USER_SHOW="true"' \
      -a 'SPACESHIP_TIME_SHOW="true"' \
      -a 'SPACESHIP_TIME_COLOR="grey"' \
      -a 'SPACESHIP_DIR_COLOR="cyan"' \
      -a 'SPACESHIP_GIT_SYMBOL="⇡"' \
      -a 'SPACESHIP_BATTERY_SHOW="false"' \
      -a 'if [[ $(pwd) != /home/project ]]; then cd /home/project; fi' \
      -a 'hash -d p=/home/project' \
      -p git \
      -p https://github.com/zsh-users/zsh-autosuggestions \
      -p https://github.com/zsh-users/zsh-completions \
      -p https://github.com/zsh-users/zsh-history-substring-search \
      -p https://github.com/zsh-users/zsh-syntax-highlighting \
      -p 'history-substring-search' \
      -p https://github.com/bobthecow/git-flow-completion \
      -a 'bindkey "\$terminfo[kcuu1]" history-substring-search-up' \
      -a 'bindkey "\$terminfo[kcud1]" history-substring-search-down' && \
    rm /tmp/zsh-in-docker.sh && \
    printf '%s\n%s\n' "export ZSH_DISABLE_COMPFIX=true" "$(cat /home/abc/.zshrc)" > /home/abc/.zshrc && \
    echo 'alias python="python3"' >> /root/.zshrc && \
    echo 'alias python="python3"' >> /home/abc/.zshrc

# Install additional tools and clean up.
RUN cd /tmp && \
    wget https://github.com/tsl0922/ttyd/releases/download/1.7.1/ttyd.x86_64 && \
    mv ttyd.x86_64 /usr/bin/ttyd && \
    chmod +x /usr/bin/ttyd && \
    chown abc:abc /home/project && \
    mkdir -p /home/abc/bin /home/abc/.local/bin && \
    chown -R abc:abc /home/abc/bin /home/abc/.local && \
    find /home -type d -exec chown abc:abc {} \; && \
    find /home -type f -exec chown abc:abc {} \; && \
    apt-get autoremove -y && apt-get clean -y && apt-get autoclean -y && \
    rm -rf /home/abc/.oh-my-zsh/.git /home/abc/.oh-my-zsh/.github \
           /home/abc/.oh-my-zsh/custom/plugins/git-flow-completion/.git \
           /home/abc/.oh-my-zsh/custom/plugins/zsh-autosuggestions/.git \
           /home/abc/.oh-my-zsh/custom/plugins/zsh-completions/.git \
           /home/abc/.oh-my-zsh/custom/plugins/zsh-history-substring-search/.git \
           /home/abc/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting/.git \
           /home/abc/.oh-my-zsh/custom/themes/spaceship-zsh-theme/.git

# Switch to abc user.
USER abc

# Configure Nix and Pipx.
RUN git config --global credential.helper cache && \
    curl -L https://nixos.org/nix/install > /tmp/nix.sh && \
    chmod +x /tmp/nix.sh && \
    sh /tmp/nix.sh --no-daemon && \
    rm /tmp/nix.sh && \
    sudo apt-get update && sudo apt-get install -y python3-venv && \
    python3 -m pip install --user pipx --break-system-packages && \
    python3 -m pipx ensurepath

# Update PATH for custom installs.
ENV PATH="/home/abc/bin:${PATH}"
ENV PATH="/home/abc/.local/bin:${PATH}"
ENV PATH="/home/abc/.nix-profile/bin:${PATH}"

# Install additional packages via pipx and asdf.
RUN echo "------------------------------------------------------ after PATH updates" && \
    pipx install rich-cli --break-system-packages && \
    echo 'alias p="rich"' >> /home/abc/.zshrc && \
    nix-env -iA cachix -f https://cachix.org/api/v1/install && \
    git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.11.3 && \
    rm -rf /home/abc/.asdf/.git && \
    echo '. "$HOME/.asdf/asdf.sh"' >> ~/.zshrc && \
    echo 'fpath=(${ASDF_DIR}/completions $fpath)' >> ~/.zshrc && \
    echo 'autoload -Uz compinit && compinit' >> ~/.zshrc

# Use exec form for ENTRYPOINT so signals are forwarded correctly.
ENTRYPOINT ["/bin/sh", "-c", "/etc/init.d/cron start && exec supervisord -c '/etc/supervisord/unified-supervisord.conf'"]
