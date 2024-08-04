VERSION 0.8

alexghr-sysbox:
  ARG registry
  ARG image

  BUILD github.com/AztecProtocol/aztec-packages/build-images+sysbox
  FROM github.com/AztecProtocol/aztec-packages/build-images+sysbox

  RUN apt-get install -y --no-install-recommends \
    bash-completion \
    less \
    inotify-tools \

  RUN mkdir -p /etc/systemd/system/ssh.socket.d && \
    echo "[Socket]" > /etc/systemd/system/ssh.socket.d/override.conf && \
    echo "ListenStream=" >> /etc/systemd/system/ssh.socket.d/override.conf && \
    echo "ListenStream=2222" >> /etc/systemd/system/ssh.socket.d/override.conf && \
    echo "StreamLocalBindUnlink yes" > /etc/ssh/sshd_config.d/gpg-agent.conf && \

  RUN curl -fsSL https://tailscale.com/install.sh | sh
  
  RUN cargo --locked install \
    starship \
    zoxide \
    ripgrep \
    bat \
    fzf \
    eza

  USER ubuntu

  RUN mkdir -p $HOME/{.ssh,.local/bin,.config,.npm-global} && \
    npm config set prefix '$HOME/.npm-global' && \
    starship preset pure-preset -o $HOME/.config/starship.toml

  COPY --chown=ubuntu:ubuntu .bashrc_extra $HOME/.bashrc_extra
  # https://medium.com/@sergiu.savva/simplify-your-aws-cli-experience-with-mfa-a-handy-bash-script-940d8517ad4d
  COPY --chown=ubuntu:ubuntu awsmfa $HOME/.local/bin/awsmfa
  RUN echo 'source $HOME/.bashrc_extra' >> $HOME/.bashrc


  RUN curl -o $HOME/.ssh/authorized_keys  https://github.com/alexghr.keys

  SAVE IMAGE --push $registry/$image:latest
