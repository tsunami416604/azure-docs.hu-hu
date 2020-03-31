---
title: A cloud-init használatával bash-parancsfájlt futtategy Linux os virtuális gépen az Azure-ban
description: Hogyan használható a cloud-init egy bash szkript futtatásához egy Linux virtuális gép létrehozása során az Azure CLI
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: e2f19ceb6c7f19ba749b46a3553036587be6a71a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969217"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>A felhőalapú init használatával bash-parancsfájlt futtategy Linux os virtuális gépben az Azure-ban
Ez a cikk bemutatja, hogyan használhatja a [cloud-init](https://cloudinit.readthedocs.io) egy meglévő bash-parancsfájl linuxos virtuális gépen (VM) vagy virtuálisgép-méretezési készletek (VMSS) kiépítése kor az Azure-ban. Ezek a felhőalapú init-parancsfájlok az azure-beli erőforrások kiépítése után futnak az első rendszerindításkor. Ha többet szeretne tudni arról, hogy a cloud-init hogyan működik natívan az Azure-ban és a támogatott Linux-disztribúciókban, tekintse meg a [cloud-init áttekintését.](using-cloud-init.md)

## <a name="run-a-bash-script-with-cloud-init"></a>Bash-parancsfájl futtatása felhőalapú init
A cloud-init nem kell átalakítani a meglévő parancsfájlok egy felhő-config, cloud-init fogad el több bemeneti típusok, amelyek közül az egyik egy bash script.

Ha a Linux egyéni parancsfájl Azure-bővítményt használja a parancsfájlok futtatásához, áttelepítheti őket a felhőalapú init használatához. Azonban az Azure Extensions integrált jelentéskészítési riasztást parancsfájl hibák, a felhő-init rendszerkép központi telepítése nem fog sikerülni, ha a parancsfájl sikertelen lesz.

Ha működés közben szeretné látni ezt a funkciót, hozzon létre egy egyszerű bash-parancsfájlt tesztelésre. A felhő-init `#cloud-config` fájlhoz hasonlóan ez a parancsfájl helyi, ahol az AzureCLI-parancsokat fogja futtatni a virtuális gép kiépítése.  Ebben a példában hozza létre a fájlt a Cloud Shell nem a helyi gépen. Bármelyik szerkesztőt használhatja. Írja be a `sensible-editor simple_bash.sh` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. Válassza ki #1 a **nanoszerkesztő** használatához. Győződjön meg arról, hogy a teljes felhő-init fájl másolása helyesen, különösen az első sorban.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

A lemezkép telepítése előtt létre kell hoznia egy erőforráscsoportot az [az csoport létrehozása](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuális gép [az vm létrehozása,](/cli/azure/vm) és adja meg a bash `--custom-data simple_bash.sh` parancsfájlt az alábbiak szerint:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>A bash parancsfájl futásának ellenőrzése
SSH a virtuális gép nyilvános IP-címét az előző parancs kimenetén látható. Adja meg saját **nyilvánosIpAddress** az alábbiak szerint:

```bash
ssh <publicIpAddress>
```

Módosítsa a **/tmp** könyvtárat, és ellenőrizze, hogy létezik-e myScript.txt fájl, és benne van-e a megfelelő szöveg.  Ha nem, akkor ellenőrizze a **/var/log/cloud-init.log** további részletekért.  Keresse meg a következő bejegyzést:

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>További lépések
A konfigurációs módosításokra vonatkozó további felhőalapú init példákat az alábbi témakörben talál:
 
- [További Linux-felhasználó hozzáadása a virtuális géphez](cloudinit-add-user.md)
- [Csomagkezelő futtatása meglévő csomagok frissítéséhez az első rendszerindításkor](cloudinit-update-vm.md)
- [A virtuális gép helyi állomásnevének módosítása](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítése, konfigurációs fájlok frissítése és kulcsok befecskendezése](tutorial-automate-vm-deployment.md)
