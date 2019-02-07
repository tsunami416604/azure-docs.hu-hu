---
title: Felhasználó hozzáadása az Azure-on Linuxos VM cloud-init használata |} A Microsoft Docs
description: A cloud-init használata Linux rendszerű virtuális gép létrehozásakor az Azure CLI-vel a felhasználó hozzáadása
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
ms.openlocfilehash: 51de92eb64e9879b769baf7e574ee1dca9355040
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55767009"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>Felhasználók hozzáadása az Azure-beli Linuxos virtuális gép a cloud-init használatával
Ez a cikk bemutatja, hogyan használható [a cloud-init](https://cloudinit.readthedocs.io) felhasználó hozzáadása egy virtuális gép (VM) vagy virtuálisgép-méretezési csoportok (VMSS): kiépítés az Azure-ban ideje. Ez a szkript a cloud-init egyszer az erőforrásokat az Azure-ban kiépített fut az első rendszerindításkor. A cloud-init működése natív módon az Azure és a támogatott Linux-disztribúciók kapcsolatos további információkért lásd: [cloud-init áttekintése](using-cloud-init.md).

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Felhasználó hozzáadása egy virtuális Gépet, a cloud-Init használatával
Minden olyan új Linux rendszerű virtuális gépen az első feladatok egyik maga használatának elkerülése érdekében további felhasználó felvétele *legfelső szintű*. SSH-kulcsokat is ajánlott eljárás, biztonsági és a használhatóságot. Kulcsok kerülnek a *~/.ssh/authorized_keys* ezt a szkriptet a cloud-init-fájlt.

Felhasználó hozzáadása egy Linux rendszerű virtuális géphez, hozzon létre egy fájlt az aktuális felületen *cloud_init_add_user.txt* , és illessze be a következő konfigurációt. Ebben a példában a Cloud shellben, nem a helyi gépén hozzon létre a fájlt. Bármelyik szerkesztőt használhatja. Írja be a `sensible-editor cloud_init_add_user.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. Válassza ki a használandó #1 a **nano** szerkesztő. Győződjön meg arról, hogy a teljes cloud-init-fájlt, másolja, különösen az első sort.  Meg kell adnia a saját nyilvános kulcs (például a tartalmát *~/.ssh/id_rsa.pub*) értékeként `ssh-authorized-keys:` – példa egyszerűsítése érdekében itt csonkult.

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
> A #cloud-konfigurációs fájl tartalmazza a `- default` paramétert tartalmaz. Ez a felhasználó hozzáfűzése a meglévő rendszergazdai felhasználó a kiépítés során létrehozott. Ha egy felhasználói nélkül hoz létre a `- default` paraméter – az automatikusan létrehozott rendszergazdai felhasználó hozta létre az Azure platform felülírt lenne. 

Ez a rendszerkép üzembe helyezése előtt hozzon létre egy erőforráscsoportot kell a [az csoport létrehozása](/cli/azure/group) parancsot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuális Gépet a [az virtuális gép létrehozása](/cli/azure/vm) , és adja meg a cloud-init fájl `--custom-data cloud_init_add_user.txt` módon:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

SSH nyilvános IP-címet a virtuális gép az előző parancs kimenetében látható. Adja meg a saját **publicIpAddress** módon:

```bash
ssh <publicIpAddress>
```

Annak ellenőrzéséhez, hogy a felhasználó lett felvéve a virtuális gép és a megadott csoportokat, megtekintheti a tartalmát a */etc/csoport* fájlt az alábbiak szerint:

```bash
cat /etc/group
```

Az alábbi Példakimenet azt mutatja, hogy a felhasználó a *cloud_init_add_user.txt* fájl van adva a virtuális gép és a megfelelő csoporthoz:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>További lépések
Konfigurációs módosítások további a cloud-init példákért tekintse meg a következőket:
 
- [További Linux-felhasználó hozzáadása virtuális Géphez](cloudinit-add-user.md)
- [Az első rendszerindításkor a meglévő csomagokat frissíteni Csomagkezelő futtatása](cloudinit-update-vm.md)
- [Módosítsa a virtuális gép helyi gazdagépnév](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítése, a konfigurációs fájlokat és a kulcsok beszúrása](tutorial-automate-vm-deployment.md)
