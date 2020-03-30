---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 876e05d7b18ac193edbc9cf842ea2c1bf0555d54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593722"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>A Linkerd linkerd ügyfél bináris fájljának letöltése és telepítése

A MacOS bash-alapú rendszerhéjában a Linkerd kiadás letöltéséhez az alábbiak szerint használható: `curl`

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-darwin"
```

Az `linkerd` ügyfél bináris fut az ügyfélgépen, és lehetővé teszi, hogy a Linkerd szolgáltatás háló. A következő parancsokkal telepítse a `linkerd` Linkerd ügyfél bináris egy bash-alapú rendszerhéj a MacOS. Ezek a `linkerd` parancsok az ügyfél bináris fájlját `PATH`a normál felhasználói program helyére másolják a rendszerben.

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-darwin /usr/local/bin/linkerd
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
