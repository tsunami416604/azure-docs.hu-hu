---
title: Felhasználó hozzáadása a Linux virtuális gépek Azure-felhő inicializálás használatával |} Microsoft Docs
description: Felhő inicializálás használata a Linux virtuális gép létrehozása az Azure CLI 2.0 során a felhasználó hozzáadása
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
ms.openlocfilehash: ce4421fc8276f215564cb7a171a215cc166c8517
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
ms.locfileid: "29123463"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>Használja a felhő inicializálás hozzáadni egy felhasználót egy Linux virtuális Gépet az Azure-ban
Ez a cikk bemutatja, hogyan használható [felhő inicializálás](https://cloudinit.readthedocs.io) felhasználó hozzáadása a virtuális gép (VM) vagy virtuális gép méretezési beállítja (VMSS) kiépítési idő az Azure-ban. A felhő inicializálás parancsprogram lefut a első indítása, ha az erőforrások az Azure-ban kiépített. Felhő inicializálás működése natív Azure-ban és a Linux támogatott disztribúciókkal kapcsolatos további információkért lásd: [felhő inicializálás áttekintése](using-cloud-init.md).

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Felhasználó hozzáadása a virtuális gép és felhő inicializálás
Az első feladatok számára bármely új Linux virtuális gép egyik hozzáadni a szolgáltatást, hogy kerülje el a további felhasználó *legfelső szintű*. SSH-kulcsok a biztonsági és a használhatóság ajánlott eljárás. Kulcsok bekerülnek a *~/.ssh/authorized_keys* a felhő inicializálás parancsfájl-fájlt.

Felhasználó hozzáadása a Linux virtuális gép, hozzon létre egy fájlt az aktuális rendszerhéjban nevű *cloud_init_add_user.txt* , majd illessze be a következő konfigurációt. Hozzon létre a fájl ebben a példában a felhő rendszerhéj nem a helyi számítógépen. A szerkesztő kívánja használata. Írja be a `sensible-editor cloud_init_add_user.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. Válassza ki a használandó #1 a **nano** szerkesztő. Győződjön meg arról, hogy az egész felhő inicializálás fájl megfelelően lett lemásolva különösen az első sor.  Meg kell adnia a saját nyilvános kulcs (például a tartalmát *~/.ssh/id_rsa.pub*) értékének `ssh-authorized-keys:` -egyszerűbbé teheti a példa itt lerövidítette.

```yaml
#cloud-config
users:
  - default
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```
> [!NOTE] 
> A #cloud-konfigurációs fájl tartalmazza a `- default` paraméter. Ez a felhasználó hozzáfűzése a meglévő rendszergazdai jogú felhasználó kiépítés során létrehozott. Ha létrehoz egy felhasználó nélkül a `- default` paraméter – az automatikusan létrehozott rendszergazdai jogú felhasználó hozta létre az Azure platformon felülírt lenne. 

Ez a rendszerkép telepítés megkezdése előtt hozzon létre egy erőforráscsoportot a kell a [az csoport létrehozása](/cli/azure/group#az_group_create) parancsot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Ezután hozzon létre a virtuális gép és [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) , és adja meg a felhő inicializálás fájl `--custom-data cloud_init_add_user.txt` az alábbiak szerint:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

SSH-kapcsolatot a virtuális Gépet, a fenti parancs kimenetében megjelennek a nyilvános IP-címét. Adja meg a saját **publicIpAddress** az alábbiak szerint:

```bash
ssh <publicIpAddress>
```

Ellenőrizze, hogy a felhasználó lett felvéve a virtuális gép és a megadott csoportokra, tekintse meg a tartalmát a */etc/csoport* fájlt az alábbiak szerint:

```bash
cat /etc/group
```

Az alábbi példa kimenetben látható a felhasználó nem a *cloud_init_add_user.txt* fájl hozzá lett adva a virtuális gép és a megfelelő csoporthoz:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>További lépések
Konfigurációs módosítások további felhőalapú inicializálás példákért lásd a következő:
 
- [Egy további Linux-felhasználó hozzáadása a virtuális gépek](cloudinit-add-user.md)
- [Futtassa a Csomagkezelő első indításakor meglévő csomagok frissítése](cloudinit-update-vm.md)
- [Módosítsa a virtuális gép helyi állomásnév](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítését, a konfigurációs fájlokat és a kulcsok beszúrása](tutorial-automate-vm-deployment.md)