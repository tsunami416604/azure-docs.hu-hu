---
title: A cloud-init használatával egy bash-szkript futtatása Linux rendszerű virtuális gépen az Azure-ban |} A Microsoft Docs
description: A cloud-init használata egy bash-szkript futtatása a Linux rendszerű virtuális gép létrehozásakor az Azure CLI-vel
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 8e8950cbd7927cb6b0543866ab976b550c9ec043
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46959546"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>A cloud-init használatával egy bash-szkript futtatása Linux rendszerű virtuális gép az Azure-ban
Ez a cikk bemutatja, hogyan használható [a cloud-init](https://cloudinit.readthedocs.io) futtatásához egy meglévő bash-szkript egy Linux rendszerű virtuális gépre (VM) vagy virtuálisgép-méretezési csoportok (VMSS), a kiépítés ideje az Azure-ban. Ezen a cloud-init parancsfájlok futtatása az első rendszerindításkor az Azure-ban kiépített erőforrások után. A cloud-init működése natív módon az Azure és a támogatott Linux-disztribúciók kapcsolatos további információkért lásd: [cloud-init áttekintése](using-cloud-init.md)

## <a name="run-a-bash-script-with-cloud-init"></a>A bash-szkript futtatása a cloud-Init használatával
Cloud-Init használatával nem kell a meglévő parancsfájlok alakítható át egy felhőkonfigurációt a cloud-init több bemeneti típusai, amelyek egyike a bash-szkript fogad el.

Ha már használja a Linux Azure Szkriptbővítmény a parancsfájlok futtatásához, áttelepítheti azokat a cloud-init használatával. Azonban az Azure-bővítmények integrálva van a parancsfájl esetén riasztást a jelentéskészítés, a cloud-init lemezkép telepítésének sikeresek lesznek, ha a parancsfájl futtatása sikertelen.

Ez a funkció működésének megtekintéséhez hozzon létre egy egyszerű bash parancsfájl tesztelési. A cloud-Init eszközt, például `#cloud-config` fájl, ez a szkript helyinek kell lennie, ahol fog futni a AzureCLI-parancsok a virtuális gép üzembe helyezése.  Ebben a példában a Cloud shellben, nem a helyi gépén hozzon létre a fájlt. Bármelyik szerkesztőt használhatja. Írja be a `sensible-editor simple_bash.sh` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. Válassza ki a használandó #1 a **nano** szerkesztő. Győződjön meg arról, hogy a teljes cloud-init-fájlt, másolja, különösen az első sort.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

Ez a rendszerkép üzembe helyezése előtt hozzon létre egy erőforráscsoportot kell a [az csoport létrehozása](/cli/azure/group#az_group_create) parancsot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuális Gépet a [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) , és adja meg a bash parancsfájl- `--custom-data simple_bash.sh` módon:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>Ellenőrizze a bash-szkript futtatása
SSH nyilvános IP-címet a virtuális gép az előző parancs kimenetében látható. Adja meg a saját **publicIpAddress** módon:

```bash
ssh <publicIpAddress>
```

Módosítsa a **ügynökszámítógépen** könyvtárat, és győződjön meg arról, hogy myScript.txt fájl létezik, és rendelkezik a megfelelő szöveg belül azt.  Ha nem jelenik meg, ellenőrizze a **/var/log/cloud-init.log** további részletekért.  Keresse meg a következő bejegyzést:

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>További lépések
Konfigurációs módosítások további a cloud-init példákért tekintse meg a következőket:
 
- [További Linux-felhasználó hozzáadása virtuális Géphez](cloudinit-add-user.md)
- [Az első rendszerindításkor a meglévő csomagokat frissíteni Csomagkezelő futtatása](cloudinit-update-vm.md)
- [Módosítsa a virtuális gép helyi gazdagépnév](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítése, a konfigurációs fájlokat és a kulcsok beszúrása](tutorial-automate-vm-deployment.md)