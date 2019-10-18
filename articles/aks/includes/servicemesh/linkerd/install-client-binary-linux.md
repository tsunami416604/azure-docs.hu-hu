---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 1729eabca75ed7c7a3a43ea2c0b1617efd337f2c
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530107"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Töltse le és telepítse a Linkerd Linkerd-ügyfél bináris fájlját

A Linux vagy a [Linux rendszerhez készült Windows alrendszer][install-wsl]bash-alapú rendszerhéjában a következő módon töltheti le az Linkerd-kiadást a `curl` használatával:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-linux"
```

A `linkerd`-ügyfél bináris fájlja fut az ügyfélszámítógépen, és lehetővé teszi a Linkerd szolgáltatás hálójának kezelését. A következő parancsokkal telepítheti a Linkerd `linkerd`-ügyfél bináris fájlját egy bash-alapú rendszerhéjban Linuxon vagy [Linuxon futó Windows alrendszeren][install-wsl]. Ezek a parancsok a `linkerd` ügyfél bináris fájljait a `PATH` normál felhasználói program mappájába másolják.

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Ha a Linkerd parancssori kiegészítést szeretne végrehajtani `linkerd` ügyfél bináris fájlját, a következőképpen állítsa be:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10