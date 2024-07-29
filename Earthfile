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

  ENV AZTEC_HOME /home/ubuntu
  RUN mkdir -p $AZTEC_HOME/.local/bin $AZTEC_HOME/.config && \
    curl -sS  https://starship.rs/install.sh > /tmp/starship.sh && \
    chmod +x /tmp/starship.sh && \
    /tmp/starship.sh --bin-dir $AZTEC_HOME/.local/bin --yes && \
    echo 'export PATH="$HOME/.local/bin:$PATH"' >> $AZTEC_HOME/.bashrc && \
    echo 'eval "$(starship init bash)"' >> $AZTEC_HOME/.bashrc && \
    $AZTEC_HOME/.local/bin/starship preset pure-preset -o $AZTEC_HOME/.config/starship.toml && \
    echo "add_newline = false" >> $AZTEC_HOME/.config/starship.toml

  SAVE IMAGE --push $registry/$image:latest
