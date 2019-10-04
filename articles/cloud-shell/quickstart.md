---
title: Azure Cloud Shell rövid útmutató | Microsoft Docs
description: Azure Cloud Shell
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
ms.openlocfilehash: 8151013f263c6cf2f90e89fa1c3b0b3025f2ea38
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68741989"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>A bash gyors üzembe helyezése Azure Cloud Shell

Ez a dokumentum részletesen ismerteti, hogyan használható a bash a [Azure Portal](https://ms.portal.azure.com/)Azure Cloud Shellban.

> [!NOTE]
> Azure Cloud Shell rövid útmutatóban is elérhető egy [PowerShell](quickstart-powershell.md) .

## <a name="start-cloud-shell"></a>Kezdés Cloud Shell
1. **Cloud Shell** elindítása a Azure Portal legfelső navigációs sávján. <br>
![](media/quickstart/shell-icon.png)

2. Válasszon egy előfizetést, és hozzon létre egy Storage-fiókot, és Microsoft Azure a fájlok megosztását.
3. Válassza a "tároló létrehozása" lehetőséget.

> [!TIP]
> A rendszer minden munkamenetben automatikusan hitelesíti az Azure CLI-t.

### <a name="select-the-bash-environment"></a>Bash-környezet kiválasztása
Győződjön meg arról, hogy a környezet legördülő menüje a rendszerhéj ablakának bal oldali `Bash`részén látható. <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Előfizetés beállítása
1. Azon előfizetések listázása, amelyekhez hozzáférése van.
   ```azurecli-interactive
   az account list
   ```

2. Az előnyben részesített előfizetés beállítása: <br>
```azurecli-interactive
az account set --subscription 'my-subscription-name'
```

> [!TIP]
> Az előfizetést a későbbi munkamenetek `/home/<user>/.azure/azureProfile.json`esetén is megjegyezjük.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Hozzon létre egy új erőforráscsoportot a "MyRG" nevű WestUS.
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Linux rendszerű virtuális gép készítése
Hozzon létre egy Ubuntu virtuális gépet az új erőforráscsoporthoz. Az Azure CLI létrehozza az SSH-kulcsokat, és beállítja velük a virtuális gépet. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> A `--generate-ssh-keys` használata arra utasítja az Azure CLI-t, hogy nyilvános és titkos kulcsokat hozzon `$Home` létre és állítson be a virtuális gépen és a címtárban. Alapértelmezés szerint a kulcsok a `/home/<user>/.ssh/id_rsa` és `/home/<user>/.ssh/id_rsa.pub`a Cloud Shell kerülnek. A `.ssh` mappát a rendszer a csatolt fájlmegosztás 5 GB-os rendszerképében `$Home`őrzi meg.

A virtuális gépen lévő felhasználóneve a Cloud Shell ($User@Azure:) által használt Felhasználónév.

### <a name="ssh-into-your-linux-vm"></a>SSH-t a linuxos virtuális gépre
1. Keresse meg a virtuális gép nevét a Azure Portal keresési sávjában.
2. Kattintson a "kapcsolódás" gombra a virtuális gép nevének és nyilvános IP-címének lekéréséhez. <br>
   ![](media/quickstart/sshcmd-copy.png)

3. SSH-t a virtuális gépre `ssh` a cmd fájllal.
   ```
   ssh username@ipaddress
   ```

Az SSH-kapcsolatok létrehozásakor az Ubuntu Welcome promptot kell látnia. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Takarítás 
1. Lépjen ki az SSH-munkamenetből.
   ```azurecli-interactive
   exit
   ```

2. Törölje az erőforráscsoportot és a benne található összes erőforrást.
   ```azurecli-interactive
   az group delete -n MyRG
   ```

## <a name="next-steps"></a>További lépések
[Tudnivalók a bash-fájlok megőrzéséről Cloud Shell](persisting-shell-storage.md) <br>
[További tudnivalók az Azure CLI-ről](https://docs.microsoft.com/cli/azure/) <br>
[Tudnivalók a Azure Files Storage szolgáltatásról](../storage/files/storage-files-introduction.md) <br>
