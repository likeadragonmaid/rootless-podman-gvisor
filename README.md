# Rootless podman gvisor
Guide to setup rootless podman with gvisor for sandbox containers

### Ubuntu

$ ```sudo apt update && sudo apt dist-upgrade -y && sudo apt install wget podman -y```

$ ```(
  set -e
  ARCH=$(uname -m)
  URL=https://storage.googleapis.com/gvisor/releases/release/latest/${ARCH}
  wget ${URL}/runsc ${URL}/runsc.sha512 \
    ${URL}/containerd-shim-runsc-v1 ${URL}/containerd-shim-runsc-v1.sha512
  sha512sum -c runsc.sha512 \
    -c containerd-shim-runsc-v1.sha512
  rm -f *.sha512
  chmod a+rx runsc containerd-shim-runsc-v1
  sudo mv runsc containerd-shim-runsc-v1 /usr/local/bin
)```

$ ```sudo /usr/local/bin/runsc install```

$ ```sudo nano /usr/local/bin/runsc-podman```

Paste the following in the file

```
#!/bin/bash

/usr/local/bin/runsc --network host --ignore-cgroups --debug --debug-log '/tmp/runsc/runsc.log.%TEST%.%TIMESTAMP%.%COMMAND%' "$@"
```

$ ```sudo chmod a+rx /usr/local/bin/runsc-podman```

$ ```podman --runtime /usr/local/bin/runsc-podman run docker.io/library/busybox echo Hello, World```

### If your distro has SELinux instead of apparmor and you have trouble mounting something, you can try disabling SElinux

$ ```podman --runtime /usr/local/bin/runsc-podman run --security-opt=label=disable  docker.io/library/busybox echo Hello, World```

