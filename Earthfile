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
    bat \
    ripgrep

  ENV USER_HOME /home/ubuntu
  RUN mkdir -p $USER_HOME/.ssh && curl -o $USER_HOME/.ssh/authorized_keys  https://github.com/alexghr.keys

  RUN mkdir -p /etc/systemd/system/ssh.socket.d && \
    echo "[Socket]" > /etc/systemd/system/ssh.socket.d/override.conf && \
    echo "ListenStream=" >> /etc/systemd/system/ssh.socket.d/override.conf && \
    echo "ListenStream=2222" >> /etc/systemd/system/ssh.socket.d/override.conf

  RUN curl -fsSL https://tailscale.com/install.sh | sh

  RUN mkdir -p $USER_HOME/.local/bin $USER_HOME/.config && \
    curl -sS  https://starship.rs/install.sh > /tmp/starship.sh && \
    chmod +x /tmp/starship.sh && \
    /tmp/starship.sh --bin-dir $USER_HOME/.local/bin --yes && \
    echo 'export PATH="$HOME/.local/bin:$PATH"' >> $USER_HOME/.bashrc && \
    echo 'eval "$(starship init bash)"' >> $USER_HOME/.bashrc && \
    $USER_HOME/.local/bin/starship preset pure-preset -o $USER_HOME/.config/starship.toml

  SAVE IMAGE --push $registry/$image:latest
