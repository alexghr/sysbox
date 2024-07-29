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

  RUN mkdir -p /etc/systemd/system/ssh.socket.d && \
    echo "[Socket]" > /etc/systemd/system/ssh.socket.d/override.conf && \
    echo "ListenStream=" >> /etc/systemd/system/ssh.socket.d/override.conf && \
    echo "ListenStream=2222" >> /etc/systemd/system/ssh.socket.d/override.conf

  RUN curl -fsSL https://tailscale.com/install.sh | sh

  USER ubuntu

  RUN mkdir -p $HOME/.ssh && curl -o $HOME/.ssh/authorized_keys  https://github.com/alexghr.keys

  RUN mkdir ~/.npm-global && npm config set prefix '~/.npm-global'

  RUN mkdir -p $HOME/.local/bin $HOME/.config && \
    curl -sS  https://starship.rs/install.sh > /tmp/starship.sh && \
    chmod +x /tmp/starship.sh && \
    /tmp/starship.sh --bin-dir $HOME/.local/bin --yes && \
    echo 'eval "$(starship init bash)"' >> $HOME/.bashrc && \
    $HOME/.local/bin/starship preset pure-preset -o $HOME/.config/starship.toml

  RUN echo 'export PATH=$HOME/.npm-global/bin:$PATH' >> ~/.bashrc && \
    echo 'export PATH=$HOME/.cargo/bin:$PATH' >> ~/.bashrc && \
    echo 'export PATH=$HOME/.aztec/bin:$PATH' >> ~/.bashrc && \
    echo 'export PATH=$HOME/.local/bin:$PATH' >> ~/.bashrc
    

  SAVE IMAGE --push $registry/$image:latest
