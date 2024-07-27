VERSION 0.8

alexghr-sysbox:
  ARG registry
  ARG image

  BUILD github.com/AztecProtocol/aztec-packages/build-images+sysbox
  FROM github.com/AztecProtocol/aztec-packages/build-images+sysbox

  SAVE IMAGE --push $registry/$image:latest
