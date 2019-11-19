---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: e2dd45f778bd5e596b5bcc91c63984742237ad4c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170839"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Töltse le és telepítse a Istio istioctl-ügyfél bináris fájlját

A Linux vagy [Windows rendszerű Linux][install-wsl]rendszeren futó bash-alapú rendszerhéjban `curl` segítségével töltse le a Istio-kiadást, majd bontsa ki a `tar` a következő módon:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

A `istioctl`-ügyfél bináris fájlja fut az ügyfélszámítógépen, és lehetővé teszi a Istio szolgáltatás hálójának kezelését. A következő parancsokkal telepítheti a Istio `istioctl`-ügyfél bináris fájlját egy bash-alapú rendszerhéjban Linuxon vagy [Linuxon futó Windows alrendszeren][install-wsl]. Ezek a parancsok a `istioctl` ügyfél bináris fájljait a `PATH`normál felhasználói program mappájába másolják.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Ha a Istio parancssori kiegészítést szeretne végrehajtani `istioctl` ügyfél bináris fájlját, a következőképpen állítsa be:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10