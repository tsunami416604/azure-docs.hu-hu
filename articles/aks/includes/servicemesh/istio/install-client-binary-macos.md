---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 06479b4396ab26c280cc6246d774bc30b5ea1c76
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530393"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Töltse le és telepítse a Istio istioctl-ügyfél bináris fájlját

A MacOS rendszerű bash-alapú rendszerhéjban `curl` használatával töltse le a Istio-kiadást, majd bontsa ki a `tar` a következő módon:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.3.2

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

A `istioctl`-ügyfél bináris fájlja fut az ügyfélszámítógépen, és lehetővé teszi a Istio szolgáltatás hálójának kezelését. A következő parancsokkal telepítheti a Istio `istioctl`-ügyfél bináris fájlját egy bash-alapú rendszerhéjba MacOS rendszeren. Ezek a parancsok a `istioctl` ügyfél bináris fájljait a `PATH` normál felhasználói program mappájába másolják.

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