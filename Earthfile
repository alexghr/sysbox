VERSION 0.8

sysbox:
  ARG registry
  FROM $registry/base-sysbox:latest

  RUN apt-get install -y --no-install-recommends \
    bash-completion \
    less \
    inotify-tools \
    fzf

  RUN mkdir -p /etc/systemd/system/ssh.socket.d && \
    echo "[Socket]" > /etc/systemd/system/ssh.socket.d/override.conf && \
    echo "ListenStream=" >> /etc/systemd/system/ssh.socket.d/override.conf && \
    echo "ListenStream=2222" >> /etc/systemd/system/ssh.socket.d/override.conf && \
    echo "StreamLocalBindUnlink yes" > /etc/ssh/sshd_config.d/gpg-agent.conf && \

  RUN curl -fsSL https://tailscale.com/install.sh | sh
  
  RUN cargo --locked install \
    zoxide \
    ripgrep \
    bat \
    eza

  USER ubuntu

  RUN mkdir -p $HOME/{.ssh,.local/bin,.config,.npm-global} && \
    npm config set prefix '$HOME/.npm-global'
  
  # can't install starship with cargo because rustc versions
  RUN mkdir -p $HOME/.local/bin $HOME/.config && \
    curl -sS  https://starship.rs/install.sh > /tmp/starship.sh && \
    chmod +x /tmp/starship.sh && \
    /tmp/starship.sh --bin-dir $HOME/.local/bin --yes && \
    $HOME/.local/bin/starship preset pure-preset -o $HOME/.config/starship.toml

  COPY --chown=ubuntu:ubuntu .bashrc_extra $HOME/.bashrc_extra
  # https://medium.com/@sergiu.savva/simplify-your-aws-cli-experience-with-mfa-a-handy-bash-script-940d8517ad4d
  COPY --chown=ubuntu:ubuntu awsmfa $HOME/.local/bin/awsmfa
  RUN echo 'source $HOME/.bashrc_extra' >> $HOME/.bashrc


  RUN curl -o $HOME/.ssh/authorized_keys  https://github.com/alexghr.keys

  SAVE IMAGE --push $registry/sysbox:latest

base-sysbox:
  ARG registry
  FROM github.com/AztecProtocol/aztec-packages/build-images+sysbox
  # save a copy of the base image
  SAVE IMAGE --push $registry/base-sysbox:latest
