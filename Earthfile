VERSION 0.8

sysbox:
  ARG registry
  FROM $registry/base-sysbox:latest

  RUN apt-get install -y --no-install-recommends \
    bash-completion \
    less \
    inotify-tools \
    fzf \
    btop \
    lsof \
    apt-transport-https \
    ca-certificates gnupg \
    curl

  RUN echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | tee -a /etc/apt/sources.list.d/google-cloud-sdk.list && \
    curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /usr/share/keyrings/cloud.google.gpg && apt-get update -y && apt-get install google-cloud-cli -y && \
    apt-get update && \
    apt-get install -y --no-install-recommends \
      kubectl \
      google-cloud-cli \
      google-cloud-cli-gke-gcloud-auth-plugin

  RUN mkdir -p /etc/systemd/system/ssh.socket.d && \
    echo "[Socket]" > /etc/systemd/system/ssh.socket.d/override.conf && \
    echo "ListenStream=" >> /etc/systemd/system/ssh.socket.d/override.conf && \
    echo "ListenStream=2222" >> /etc/systemd/system/ssh.socket.d/override.conf && \
    echo "StreamLocalBindUnlink yes" > /etc/ssh/sshd_config.d/gpg-agent.conf

  ENV CARGO_HOME=/opt/rust/cargo
  RUN chown -R ubuntu:ubuntu /opt/rust
  RUN rustup install stable && rustup default stable
  RUN curl -L https://foundry.paradigm.xyz | bash

  RUN cargo --locked install \
    zoxide \
    ripgrep \
    bat \
    eza \
    starship

  USER ubuntu

  COPY .bashrc_extra $HOME/.bashrc_extra
  # https://medium.com/@sergiu.savva/simplify-your-aws-cli-experience-with-mfa-a-handy-bash-script-940d8517ad4d
  COPY awsmfa $HOME/.local/bin/awsmfa

  RUN mkdir -p $HOME/.npm-global && \
    npm config set prefix '$HOME/.npm-global'
  
  # can't install starship with cargo because rustc versions
  #RUN mkdir -p $HOME/.local/bin $HOME/.config && \
  #  curl -sS  https://starship.rs/install.sh > /tmp/starship.sh && \
  #  chmod +x /tmp/starship.sh && \
  #  /tmp/starship.sh --bin-dir $HOME/.local/bin --yes && \
  #  $HOME/.local/bin/starship preset pure-preset -o $HOME/.config/starship.toml

  RUN mkdir -p $HOME/.config/git $HOME/.config/tmux $HOME/.ssh && \
    curl -o $HOME/.ssh/authorized_keys  https://github.com/alexghr.keys && \
    curl -o $HOME/.config/git/config https://raw.githubusercontent.com/alexghr/nix/refactor/flake.parts/hosts/palpatine/ag/config/gitconfig && \
    curl -o $HOME/.config/tmux/tmux.conf https://raw.githubusercontent.com/alexghr/nix/refactor/flake.parts/hosts/palpatine/ag/config/tmux.conf

  RUN echo 'source $HOME/.bashrc_extra' >> $HOME/.bashrc

  WORKDIR /workspaces
  RUN rustup override set 1.74.1

  # switch back to root so that systemd can start
  USER root
  
  # https://github.com/neovim/neovim/releases/latest
  WORKDIR /tmp
  RUN curl -LO https://github.com/neovim/neovim/releases/download/v0.10.2/nvim-linux64.tar.gz && \
    mkdir -p /usr/local && \
    tar xzvf nvim-linux64.tar.gz -C /usr/local --strip-components 1 && \
    rm nvim-linux64.tar.gz

  WORKDIR /usr/local
  RUN curl --proto '=https' --tlsv1.2 -sSf https://raw.githubusercontent.com/nektos/act/master/install.sh | sudo bash

  WORKDIR /root
  SAVE IMAGE --push $registry/sysbox:latest

base-sysbox:
  ARG registry
  FROM github.com/AztecProtocol/aztec-packages/build-images+sysbox
  # save a copy of the base image
  SAVE IMAGE --push $registry/base-sysbox:latest
