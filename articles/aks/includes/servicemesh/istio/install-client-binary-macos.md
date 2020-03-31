---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 74f5b22ccc822a188059b29d9c661a15cf8412bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593970"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Az Istio istioctl kliens bináris letöltése és telepítése

A MacOS bash-alapú rendszerhéjában töltse `curl` le az Istio `tar` kiadást, majd kivonatold ki az alábbiak szerint:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

Az `istioctl` ügyfél bináris fut az ügyfélgépen, és lehetővé teszi, hogy az Istio szolgáltatás hálója. A következő parancsokkal telepítse az `istioctl` Istio ügyfél bináris egy bash-alapú rendszerhéj a MacOS. Ezek a `istioctl` parancsok az ügyfél bináris fájlját `PATH`a normál felhasználói program helyére másolják a rendszerben.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Ha az Istio `istioctl` ügyfél bináris parancssori befejezését szeretné, állítsa be a következőképpen:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```