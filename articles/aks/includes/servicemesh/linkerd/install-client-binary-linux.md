---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 7a22256c88f9cee3ce62c68a2de4a5974d76f026
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593720"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>A Linkerd linkerd ügyfél bináris fájljának letöltése és telepítése

A bash-alapú shell Linux vagy [Windows Alrendszer Linux][install-wsl], használja, `curl` hogy töltse le a Linkerd kiadás az alábbiak szerint:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-linux"
```

Az `linkerd` ügyfél bináris fut az ügyfélgépen, és lehetővé teszi, hogy a Linkerd szolgáltatás háló. A következő parancsokkal telepítheti `linkerd` a Linkerd kliens bináris rendszerét egy bash-alapú rendszerhéjban Linux vagy [Windows Alrendszer Linux ra.][install-wsl] Ezek a `linkerd` parancsok az ügyfél bináris fájlját `PATH`a normál felhasználói program helyére másolják a rendszerben.

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Ha a Linkerd ügyfél bináris parancssori befejezését `linkerd` szeretné, állítsa be a következőképpen:

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