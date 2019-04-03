---
title: Az Azure Cloud Shell rövid bash |} A Microsoft Docs
description: 'Gyors útmutató: a Cloud Shell Bash'
services: ''
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: damaerte
ms.openlocfilehash: b8f96de7214a46c9e38182c141343a46c0e28139
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58882089"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Rövid útmutató a Bash Azure Cloud shellben

Ez a dokumentum részletesen bemutatja az Azure Cloud Shellben lévő Bash használata a [az Azure portal](https://ms.portal.azure.com/).

> [!NOTE]
> A [PowerShell az Azure Cloud Shellben](quickstart-powershell.md) a rövid útmutató esetében is elérhető.

## <a name="start-cloud-shell"></a>A Cloud Shell indítása
1. Indítsa el a **Cloud Shell** az Azure Portal felső navigációs. <br>
![](media/quickstart/shell-icon.png)

2. Válasszon ki egy előfizetést, hozzon létre egy tárfiókot, és a Microsoft Azure-fájlok megosztása.
3. Válassza a "Tároló létrehozása"

> [!TIP]
> Azure CLI-hez a hitelesítés minden munkamenetben automatikusan.

### <a name="select-the-bash-environment"></a>Válassza ki a Bash-környezetet
Ellenőrizze, hogy a környezet legördülő a bal oldalon a rendszerhéj ablakát, ugyanakkor `Bash`. <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Az előfizetés beállítása
1. Lista előfizetések hozzáfér.
   ```azurecli-interactive
   az account list
   ```

2. Állítsa be az előnyben részesített előfizetést: <br>
```azurecli-interactive
az account set --subscription 'my-subscription-name'
```

> [!TIP]
> Az előfizetés használatával későbbi munkamenet során a rendszer megjegyzi `/home/<user>/.azure/azureProfile.json`.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
A "MyRG" nevű WestUS hozzon létre egy új erőforráscsoportot.
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Linux rendszerű virtuális gép létrehozása
Egy Ubuntu virtuális gép létrehozása az új erőforráscsoportban. Az Azure CLI-vel SSH-kulcsok létrehozása, és a velük a virtuális gép beállítása. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> Használatával `--generate-ssh-keys` arra utasítja a hozhat létre, és állítsa be a nyilvános és titkos kulcsokat a virtuális gép az Azure CLI-vel és `$Home` könyvtár. Alapértelmezés szerint a kulcsok kerülnek, a Cloud shellben `/home/<user>/.ssh/id_rsa` és `/home/<user>/.ssh/id_rsa.pub`. A `.ssh` mappa megőrizni használt a mellékletfájl-megosztás 5 GB-os lemezképet a rendszer megőrzi `$Home`.

A felhasználónév, a virtuális gépen lesznek a Cloud shellben használt felhasználónév ($User@Azure:).

### <a name="ssh-into-your-linux-vm"></a>SSH a Linux rendszerű virtuális Gépre való
1. Keresse meg a virtuális gép nevét, az Azure portal keresősávba.
2. Kattintson a "Csatlakozás" a virtuális gép nevét és a nyilvános IP-címet. <br>
   ![](media/quickstart/sshcmd-copy.png)

3. SSH és a virtuális Gépre való a `ssh` cmd-ből.
   ```
   ssh username@ipaddress
   ```

Esetén az SSH-kapcsolat létrehozásakor, az Ubuntu üdvözlő üzenet megjelenik. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Takarítás 
1. Kilépés az ssh munkamenet.
   ```azurecli-interactive
   exit
   ```

2. Törölje az erőforráscsoportot és a benne lévő erőforrásokat.
   ```azurecli-interactive
   az group delete -n MyRG
   ```

## <a name="next-steps"></a>További lépések
[További információk a fájlokat a Cloud Shell Bash](persisting-shell-storage.md) <br>
[További tudnivalók az Azure CLI](https://docs.microsoft.com/cli/azure/) <br>
[További tudnivalók az Azure Files storage:](../storage/files/storage-files-introduction.md) <br>
