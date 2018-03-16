---
title: "Az Azure-felhőbe rendszerhéj gyors üzembe helyezés bash |} Microsoft Docs"
description: "A felhő rendszerhéj Bash gyors üzembe helyezés"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: juluk
ms.openlocfilehash: e48c54216c5c4ae8e53d4802aafce8883ee97c11
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Az Azure-felhőbe rendszerhéj Bash gyors üzembe helyezés

Ez a dokumentum részletesen Bash használata az Azure felhőalapú Shell a [Azure-portálon](https://ms.portal.azure.com/).

> [!NOTE]
> A [PowerShell Azure Cloud rendszerhéj](quickstart-powershell.md) gyors üzembe helyezés akkor is elérhető.

## <a name="start-cloud-shell"></a>Indítsa el a felhő rendszerhéj
1. Indítsa el **felhő rendszerhéj** a felső navigációs sávon, az Azure portálról. <br>
![](media/quickstart/shell-icon.png)

2. Válasszon ki egy előfizetést hozzon létre egy tárfiókot, és a Microsoft Azure fájlokat megosztani.
3. Válassza a "Create a storage"

> [!TIP]
> Akkor automatikusan megtörténik az Azure CLI 2.0 minden munkamenetben.

### <a name="select-the-bash-environment"></a>Válassza ki a Bash
Ellenőrizze, hogy a környezet legördülő bal oldalán rendszerhéj időszak szerint `Bash`. <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Az előfizetés beállítása
1. Lista előfizetések rendelkezik hozzáféréssel.
```azurecli-interactive
az account list
```

2. Állítsa be az előnyben részesített előfizetés: <br>
```azurecli-interactive
az account set --subscription my-subscription-name`
```

> [!TIP]
> A rendszer tárolja a későbbi kapcsolatok használata az előfizetés `/home/<user>/.azure/azureProfile.json`.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
A "MyRG" nevű WestUS hozzon létre egy új erőforráscsoportot.
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Linux rendszerű virtuális gép létrehozása
Ubuntu virtuális gép létrehozása az új erőforráscsoportban. Az Azure CLI 2.0 SSH-kulcsok létrehozása, és a velük a virtuális gépet. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> Használatával `--generate-ssh-keys` arra utasítja az Azure CLI 2.0 használatával hozzon létre, és állítsa be a virtuális Gépet a nyilvános és titkos kulcsok és `$Home` könyvtár. Alapértelmezés szerint a kulcsok kerülnek, a felhő rendszerhéj `/home/<user>/.ssh/id_rsa` és `/home/<user>/.ssh/id_rsa.pub`. A `.ssh` mappa a mellékletfájl-megosztás 5 GB-os kép használt megőrződjenek `$Home`.

A felhasználónév, a virtuális gép lesz a felhő rendszerhéj használt felhasználónév ($User@Azure:).

### <a name="ssh-into-your-linux-vm"></a>SSH a Linux virtuális gép be
1. Keresse meg a virtuális gép nevét, az Azure portál keresési sávon.
2. Kattintson a "Csatlakozás" le a virtuális gép nevét és a nyilvános IP-cím. <br>
![](media/quickstart/sshcmd-copy.png)

3. SSH t a virtuális Gépre, és a `ssh` cmd
```
ssh username@ipaddress
```

Az SSH-kapcsolatot létesít, akkor meg kell jelennie az Ubuntu üdvözlő kérés. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Takarítás 
1. Kilépés a ssh munkamenet.
```azurecli-interactive
exit
```

2. Törölje az erőforráscsoportot és bármely erőforrása.
```azurecli-interactive
Run `az group delete -n MyRG`
```

## <a name="next-steps"></a>További lépések
[A felhő rendszerhéj Bash tárolásakor fájlok megismerése](persisting-shell-storage.md) <br>
[További tudnivalók az Azure CLI 2.0](https://docs.microsoft.com/cli/azure/) <br>
[Tudnivalók Azure Files storage:](../storage/files/storage-files-introduction.md) <br>
