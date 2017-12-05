---
title: "Használja a felhő inicializálás bash parancsfájl futtatása a Linux virtuális Gépet az Azure-on |} Microsoft Docs"
description: "Felhő inicializálás bash parancsfájlok futtatására a Linux virtuális gép létrehozása az Azure CLI 2.0 használatával"
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 1fcc432e8437a7fd284a75aa40454848a2af3006
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>Használja a felhő inicializálás bash parancsfájlok futtatására egy Linux virtuális gépre az Azure-ban
Ez a cikk bemutatja, hogyan használható [felhő inicializálás](https://cloudinit.readthedocs.io) futtatásához egy meglévő bash parancsfájl egy Linux virtuális gép (VM) vagy virtuálisgép-méretezési (VMSS) beállítja kiépítés idő az Azure-ban. Ezen felhő inicializálás parancsfájlok futtatása az első betöltés után az erőforrásokat az Azure-ban kiépített. Felhő inicializálás működése natív Azure-ban és a Linux támogatott disztribúciókkal kapcsolatos további információkért lásd: [felhő inicializálás áttekintése](using-cloud-init.md)

## <a name="run-a-bash-script-with-cloud-init"></a>A felhő inicializálás bash parancsfájl futtatása
A felhő inicializálás nem kell a meglévő parancsfájlok alakítani egy felhő-config felhőalapú inicializálás több bemeneti típusai, amelyek egyike a bash parancsfájl fogad el.

Ha használja a Linux egyéni parancsprogramok futtatására szolgáló Azure bővítmény a parancsfájlok futtatásához, áttelepítheti a felhő inicializálás használhatják. Azonban Azure-bővítményekkel rendelkezik integrált jelentéskészítési riasztáshoz parancsfájl sikertelen, a felhő inicializálás lemezkép-telepítés nem meghiúsul, ha a parancsfájl futása sikertelen.

Tekintse meg ezt a funkciót az a művelet, hozzon létre egy egyszerű bash parancsprogram teszteléséhez. A felhő inicializálás, például `#cloud-config` fájl, a parancsfájl kell elhelyezkednie, ahol fog futni a AzureCLI parancsok a virtuális gép kiépítéséhez.  Hozzon létre a fájl ebben a példában a felhő rendszerhéj nem a helyi számítógépen. A szerkesztő kívánja használata. Adja meg `sensible-editor simple_bash.sh` hozza létre a fájlt, és elérhető szerkesztők listájának megtekintéséhez. Válassza ki a használandó #1 a **nano** szerkesztő. Győződjön meg arról, hogy az egész felhő inicializálás fájl megfelelően lett lemásolva különösen az első sor.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

Ez a rendszerkép telepítés megkezdése előtt hozzon létre egy erőforráscsoportot a kell a [az csoport létrehozása](/cli/azure/group#create) parancsot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Ezután hozzon létre a virtuális gép és [az virtuális gép létrehozása](/cli/azure/vm#create) és meg kell adni a bash parancsfájlt a `--custom-data simple_bash.sh` az alábbiak szerint:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>Ellenőrizze a bash parancsfájlok futtatása
SSH-kapcsolatot a virtuális Gépet, a fenti parancs kimenetében megjelennek a nyilvános IP-címét. Adja meg a saját **publicIpAddress** az alábbiak szerint:

```bash
ssh <publicIpAddress>
```

Módosítsa a **/tmp** könyvtár, és ellenőrizze, hogy myScript.txt fájl létezik, és a megfelelő szöveget saját tartozik.  Ha nem, ellenőrizze a **/var/log/cloud-init.log** további részleteket.  Keresse meg a következő bejegyzést:

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>Következő lépések
Konfigurációs módosítások további felhőalapú inicializálás példákért lásd a következő:
 
- [Egy további Linux-felhasználó hozzáadása a virtuális gépek](cloudinit-add-user.md)
- [Futtassa a Csomagkezelő első indításakor meglévő csomagok frissítése](cloudinit-update-vm.md)
- [Módosítsa a virtuális gép helyi állomásnév](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítését, a konfigurációs fájlokat és a kulcsok beszúrása](tutorial-automate-vm-deployment.md)