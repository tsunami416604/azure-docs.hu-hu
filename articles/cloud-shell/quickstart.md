---
title: Az Azure Cloud Shell rövid útmutatója – Bash
description: Ismerje meg, hogyan használhatja a Bash parancssort a böngészőben az Azure Cloud Shell használatával.
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/12/2018
ms.author: damaerte
ms.openlocfilehash: 58d795a5aee79e4149864a79a923ce34950b31d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458069"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Rövid útmutató a Bash szolgáltatáshoz az Azure Cloud Shellben

Ez a dokumentum részletezi, hogyan használhatja a Bash az Azure Cloud Shell az [Azure Portalon.](https://ms.portal.azure.com/)

> [!NOTE]
> Az [Azure Cloud Shell-rövid útmutatóban](quickstart-powershell.md) egy PowerShell is elérhető.

## <a name="start-cloud-shell"></a>Cloud Shell indítása
1. Indítsa el a **Cloud Shell-t** az Azure Portal felső navigációs sávjáról. <br>
![](media/quickstart/shell-icon.png)

2. Válasszon ki egy előfizetést egy tárfiók és a Microsoft Azure Files megosztás létrehozásához.
3. Válassza a "Tároló létrehozása" lehetőséget

> [!TIP]
> A rendszer minden munkamenetben automatikusan hitelesíti az Azure CLI-t.

### <a name="select-the-bash-environment"></a>Válassza ki a Bash környezetet
Ellenőrizze, hogy a rendszerhéj ablakának bal oldaláról `Bash`a környezet legördülő menüje azt mondja- e. <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Az előfizetés beállítása
1. Listázhatja azokat az előfizetéseket, amelyekhez hozzáférése van.
   ```azurecli-interactive
   az account list
   ```

2. Állítsa be a kívánt előfizetést: <br>
```azurecli-interactive
az account set --subscription 'my-subscription-name'
```

> [!TIP]
> A későbbi munkamenetek során `/home/<user>/.azure/azureProfile.json`a program emlékezni fog az előfizetésére.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy új erőforráscsoportot a WestUS nevű "MyRG".
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Linux rendszerű virtuális gép létrehozása
Hozzon létre egy Ubuntu virtuális gép az új erőforráscsoportban. Az Azure CLI sSH-kulcsokat hoz létre, és velük együtt állítja be a virtuális gép. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> Használata `--generate-ssh-keys` arra utasítja az Azure CLI-t, hogy hozzon létre és állítson be nyilvános és személyes kulcsokat a virtuális gépben és `$Home` a címtárban. Alapértelmezés szerint a kulcsok a `/home/<user>/.ssh/id_rsa` `/home/<user>/.ssh/id_rsa.pub`Cloud Shell-ben vannak elhelyezve a és a helyen. A `.ssh` mappa megmarad a csatolt fájlmegosztás 5 GB-os `$Home`lemezképében.

A virtuális gépfelhasználóneve a Cloud Shellben ($User@Azure:) használt felhasználónév lesz.

### <a name="ssh-into-your-linux-vm"></a>SSH a Linux VM-be
1. Keresse meg a virtuális gép nevét az Azure Portal keresősávjában.
2. Kattintson a "Csatlakozás" a virtuális gép nevét és nyilvános IP-címét. <br>
   ![](media/quickstart/sshcmd-copy.png)

3. SSH-t a virtuális `ssh` gépbe a cmd-vel.
   ```
   ssh username@ipaddress
   ```

Az SSH kapcsolat létrehozásakor látnia kell az Ubuntu üdvözlő promptot. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Takarítás 
1. Lépjen ki az ssh munkamenetből.
   ```
   exit
   ```

2. Törölje az erőforráscsoportot és a benne lévő erőforrásokat.
   ```azurecli-interactive
   az group delete -n MyRG
   ```

## <a name="next-steps"></a>További lépések
[További információ a Bash a Cloud Shellben továbbra is fennálló fájlokról](persisting-shell-storage.md) <br>
[További információ az Azure CLI-ről](https://docs.microsoft.com/cli/azure/) <br>
[További információ az Azure Files-tárhelyről](../storage/files/storage-files-introduction.md) <br>
