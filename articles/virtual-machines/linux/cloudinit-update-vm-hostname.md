---
title: Linuxos virtuális gép állomásnevének beállítása a cloud-init használatával
description: A felhőalapú init használata linuxos virtuális gépek testreszabásához az Azure CLI-vel való létrehozás során
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 631b8ef83d5fbf10ec401df7432b23238f2ae2e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969172"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>A felhőalapú init használatával állítsa be a Linux virtuális gép állomásnevét az Azure-ban
Ez a cikk bemutatja, hogyan használhatja a [cloud-init](https://cloudinit.readthedocs.io) egy adott állomásnév konfigurálásához egy virtuális gépen (VM) vagy virtuálisgép-méretezési készletek (VMSS) kiépítése kor az Azure-ban. Ezek a felhőalapú init-parancsfájlok az azure-beli erőforrások kiépítése után futnak az első rendszerindításkor. Ha többet szeretne tudni arról, hogy a cloud-init hogyan működik natívan az Azure-ban és a támogatott Linux-disztribúciókban, tekintse meg a [cloud-init áttekintését.](using-cloud-init.md)

## <a name="set-the-hostname-with-cloud-init"></a>A gazdagépnév beállítása felhőalapú init tel
Alapértelmezés szerint a állomásnév megegyezik a virtuális gép nevével, amikor új virtuális gépet hoz létre az Azure-ban.  Ha egy felhő-init parancsfájl futtatásához módosítani ezt az alapértelmezett állomásnevet, amikor létrehoz egy virtuális gép az `--custom-data` Azure-ban az az [vm létrehozása,](/cli/azure/vm)adja meg a felhő-init fájl a kapcsolóval.  

A frissítési folyamat működés közbeni megtekintéséhez hozzon létre egy fájlt az *aktuális cloud_init_hostname.txt* nevű rendszerhéjban, és illessze be a következő konfigurációt. Ebben a példában hozza létre a fájlt a Cloud Shell nem a helyi gépen. Bármelyik szerkesztőt használhatja. Írja be a `sensible-editor cloud_init_hostname.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. Válassza ki #1 a **nanoszerkesztő** használatához. Győződjön meg arról, hogy a teljes felhő-init fájl másolása helyesen, különösen az első sorban.  

```yaml
#cloud-config
hostname: myhostname
```

A lemezkép telepítése előtt létre kell hoznia egy erőforráscsoportot az [az csoport létrehozása](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuális gép [az vm létrehozása,](/cli/azure/vm) és adja meg a cloud-init fájlt `--custom-data cloud_init_hostname.txt` az alábbiak szerint:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

Miután létrehozta, az Azure CLI a virtuális gép adatait jeleníti meg. Használja `publicIpAddress` a SSH a virtuális gép. Adja meg saját címét az alábbiak szerint:

```bash
ssh <publicIpAddress>
```

A virtuális gép nevének `hostname` megtekintéséhez használja a következő parancsot:

```bash
hostname
```

A virtuális gépnek a felhő-init fájlban beállított értékként kell jelentenie az állomásnevet, ahogy az a következő példakimeneten látható:

```bash
myhostname
```

## <a name="next-steps"></a>További lépések
A konfigurációs módosításokra vonatkozó további felhőalapú init példákat az alábbi témakörben talál:
 
- [További Linux-felhasználó hozzáadása a virtuális géphez](cloudinit-add-user.md)
- [Csomagkezelő futtatása meglévő csomagok frissítéséhez az első rendszerindításkor](cloudinit-update-vm.md)
- [A virtuális gép helyi állomásnevének módosítása](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítése, konfigurációs fájlok frissítése és kulcsok befecskendezése](tutorial-automate-vm-deployment.md)
