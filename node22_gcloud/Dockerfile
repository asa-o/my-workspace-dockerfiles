FROM node:22-bookworm-slim as git-builder
ARG GIT_VERSION=2.46.1

RUN apt-get update && apt-get install -y \
        build-essential \
        libssl-dev \
        libcurl4-gnutls-dev \
        libexpat1-dev \
        zlib1g-dev \
        gettext \
        curl \
    && rm -rf /var/lib/apt/lists/* \
    && curl -L https://github.com/git/git/archive/refs/tags/v${GIT_VERSION}.tar.gz | tar xz \
    && cd git-${GIT_VERSION} \
    && make prefix=/usr all \
    && make prefix=/usr install

# 最終ステージ
FROM node:22-bookworm-slim
WORKDIR /tmp/

COPY --from=git-builder /usr/bin/git /usr/bin/
COPY --from=git-builder /usr/libexec/git-core /usr/libexec/git-core

RUN apt-get update \
    && apt-get install -y --no-install-recommends tzdata \
    && apt-get install -y \
        unzip \
        wget \
        curl \
        openssh-server \
        openjdk-17-jdk \
    && ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*

# SSH公開鍵認証の設定
COPY ./keys/* /root/.ssh/
RUN chmod 0700 /root/.ssh && \
    chmod 0644 /root/.ssh/authorized_keys && \
    chmod 600 /root/.ssh/id_ed25519 && \
    sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin prohibit-password/' /etc/ssh/sshd_config

# Android SDKのインストール
ENV ANDROID_SDK_ROOT=/opt/android-sdk
RUN mkdir -p $ANDROID_SDK_ROOT && \
    wget -q "https://dl.google.com/android/repository/commandlinetools-linux-11076708_latest.zip" -O android-sdk.zip && \
    unzip android-sdk.zip -d $ANDROID_SDK_ROOT && \
    rm android-sdk.zip && \
    yes | $ANDROID_SDK_ROOT/cmdline-tools/bin/sdkmanager --sdk_root=$ANDROID_SDK_ROOT --licenses && \
    $ANDROID_SDK_ROOT/cmdline-tools/bin/sdkmanager --sdk_root=$ANDROID_SDK_ROOT "platform-tools" "platforms;android-34" "build-tools;34.0.0"

RUN git config --global gpg.format ssh && \
    git config --global user.signingkey /root/.ssh/id_ed25519.pub && \
    git config --global commit.gpgsign true && \
    git config --global core.editor "cursor --wait" && \
    git config --global merge.tool 'cursor --wait "$MERGED"' && \
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
    && echo 'export JAVA_HOME="/usr/lib/jvm/java-17-openjdk-amd64/"' >> ~/.bashrc \
    && echo 'export TZ="Asia/Tokyo"' >> ~/.bashrc \
    && echo 'export PATH="$PATH:/opt/flutter/bin:/opt/android-sdk/platform-tools"' >> ~/.bashrc

RUN npm install -g firebase-tools
