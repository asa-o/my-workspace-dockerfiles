FROM node:20.11-bookworm-slim

RUN apt-get update && \
    apt-get install -y --no-install-recommends tzdata && \
    apt-get install -y \
    build-essential \
    libssl-dev \
    libcurl4-gnutls-dev \
    libexpat1-dev \
    gettext \
    unzip \
    openssh-server \
    zlib1g-dev \
    autoconf && \
    ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime && \
    apt-get clean -y && \
    rm -rf /var/lib/apt/lists/*

# SSH公開鍵認証の設定
COPY ./keys/* /root/.ssh/
RUN chmod 0700 /root/.ssh && \
    chmod 0644 /root/.ssh/authorized_keys && \
    chmod 600 /root/.ssh/id_ed25519 && \
    sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin prohibit-password/' /etc/ssh/sshd_config

RUN cd /tmp && \
    curl -o v2.43.0.tar.gz -L https://github.com/git/git/archive/refs/tags/v2.43.0.tar.gz && \
    tar -zxvf v2.43.0.tar.gz && \
    cd git-2.43.0 && \
    make configure && \
    ./configure --prefix=/usr && \
    make all && \
    make install && \
    cd .. && \
    rm -rf git-2.43.0 v2.43.0.tar.gz

RUN git config --global gpg.format ssh && \
    git config --global user.signingkey /root/.ssh/id_ed25519.pub && \
    git config --global commit.gpgsign true && \
    git config --global core.editor "code --wait" && \
    git config --global merge.tool 'code --wait "$MERGED"' && \
    git config --global user.name "asa-o" && \
    git config --global user.email "asao.develop@gmail.com" && \
    git config --global alias.ss "status" && \
    git config --global alias.br "branch" && \
    git config --global alias.co "checkout" && \
    git config --global alias.adu "add -u" && \
    git config --global alias.mg "merge --no-ff" && \
    git config --global alias.log1 "log -1" && \
    git config --global alias.log2 "log -2" && \
    git config --global alias.log3 "log -3" && \
    cd && \
    curl -o .git-completion.sh https://raw.githubusercontent.com/git/git/master/contrib/completion/git-completion.bash && \
    curl -o .git-prompt.sh https://raw.githubusercontent.com/git/git/master/contrib/completion/git-prompt.sh && \
    echo "" >> ~/.bashrc \
    && echo "if [ -f ~/.ssh-agent ]; then" >> ~/.bashrc \
    && echo "    . ~/.ssh-agent" >> ~/.bashrc \
    && echo "fi" >> ~/.bashrc \
    && echo "if [ -z \"\$SSH_AGENT_PID\" ] || ! kill -0 \$SSH_AGENT_PID; then" >> ~/.bashrc \
    && echo "    ssh-agent > ~/.ssh-agent" >> ~/.bashrc \
    && echo "    . ~/.ssh-agent" >> ~/.bashrc \
    && echo "fi" >> ~/.bashrc \
    && echo "ssh-add -l >& /dev/null || ssh-add" >> ~/.bashrc \
    && echo "" >> ~/.bashrc \
    && echo "if [ -f ~/.git-completion.sh ]; then" >> ~/.bashrc \
    && echo "    source ~/.git-completion.sh" >> ~/.bashrc \
    && echo "fi" >> ~/.bashrc \
    && echo "if [ -f ~/.git-prompt.sh ]; then" >> ~/.bashrc \
    && echo "    source ~/.git-prompt.sh" >> ~/.bashrc \
    && echo "fi" >> ~/.bashrc \
    && echo "GIT_PS1_SHOWDIRTYSTATE=true" >> ~/.bashrc \
    && echo "GIT_PS1_SHOWUNTRACKEDFILES=true" >> ~/.bashrc \
    && echo "GIT_PS1_SHOWSTASHSTATE=true" >> ~/.bashrc \
    && echo "GIT_PS1_SHOWUPSTREAM=auto" >> ~/.bashrc \
    && printf "PS1='[\\\\[\\\\033[32m\\\\]\\\\u@\\\\h\\\\[\\\\033[00m\\\\] \\\\[\\\\033[33m\\\\]\\\\w\\\\[\\\\033[1;36m\\\\]\$(__git_ps1 \" (%s)\")\\\\[\\\\033[00m\\\\]]\\\\n\\\\$ '\n" "%s" >> ~/.bashrc \
    && echo 'export TZ="Asia/Tokyo"' >> ~/.bashrc

RUN npm install -g firebase-tools
