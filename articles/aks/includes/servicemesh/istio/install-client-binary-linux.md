---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: b310de560f9791e1fc49d54dfbf0789c38d37f57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593969"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Az Istio istioctl kliens bináris letöltése és telepítése

Egy bash-alapú shell Linux vagy [Windows Alrendszer Linux][install-wsl], használja, `curl` hogy töltse `tar` le az Istio kiadás, majd kivonat az alábbiak szerint:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

Az `istioctl` ügyfél bináris fut az ügyfélgépen, és lehetővé teszi, hogy az Istio szolgáltatás hálója. A következő parancsokkal telepítheti `istioctl` az Istio kliens bináris rendszerét egy bash-alapú rendszerhéjban Linux vagy [Windows Alrendszer Linuxra.][install-wsl] Ezek a `istioctl` parancsok az ügyfél bináris fájlját `PATH`a normál felhasználói program helyére másolják a rendszerben.

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

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10