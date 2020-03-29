---
title: Felhasználó hozzáadása linuxos virtuális géphez az Azure-ban
description: A felhőalapú init használata egy felhasználó hozzáadása linuxos virtuális géphez az Azure CLI-vel való létrehozás során
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: f1782bfe0c14e3b44703f89ec7f78590c1bb74c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969240"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>Felhasználó hozzáadása linuxos virtuális géphez az Azure-ban
Ez a cikk bemutatja, hogyan használhatja a [cloud-init](https://cloudinit.readthedocs.io) egy felhasználó hozzáadása egy virtuális gépen (VM) vagy virtuálisgép-méretezési csoportok (VMSS) kiépítése kor az Azure-ban. Ez a cloud-init parancsfájl fut az első rendszerindítás, miután az erőforrások azure-beli kiépítése. Ha többet szeretne tudni arról, hogy a cloud-init hogyan működik natívan az Azure-ban és a támogatott Linux-disztribúciókban, tekintse meg a [cloud-init áttekintését.](using-cloud-init.md)

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Felhasználó hozzáadása felhőalapú virtuális géphez
Az egyik első feladat minden új Linux VM- ben, hogy további felhasználót adjon hozzá magának, hogy elkerülje a *gyökér*használatát. Az SSH-kulcsok a biztonság és a használhatóság ajánlott gyakorlatai. A kulcsok hozzáadódnak a *~/.ssh/authorized_keys* fájlhoz ezzel a cloud-init parancsfájllal.

Ha hozzá szeretne adni egy felhasználót egy Linux virtuális géphez, hozzon létre egy fájlt az *aktuális cloud_init_add_user.txt* nevű rendszerhéjban, és illessze be a következő konfigurációt. Ebben a példában hozza létre a fájlt a Cloud Shell nem a helyi gépen. Bármelyik szerkesztőt használhatja. Írja be a `sensible-editor cloud_init_add_user.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. Válassza ki #1 a **nanoszerkesztő** használatához. Győződjön meg arról, hogy a teljes felhő-init fájl másolása helyesen, különösen az első sorban.  Meg kell adnia a saját nyilvános kulcsát (például a *~/.ssh/id_rsa.pub*tartalmát ) a `ssh-authorized-keys:` - itt lerövidült, hogy egyszerűsítse a példát.

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
> A #cloud-config fájl `- default` tartalmazza a paramétert tartalmazza. Ez hozzáfűzi a felhasználót a meglévő rendszergazdai felhasználó a kiépítés során létrehozott. Ha létrehoz egy felhasználót a `- default` paraméter nélkül – az Automatikusan létrehozott rendszergazdai felhasználó az Azure platform által létrehozott felülírja. 

A lemezkép telepítése előtt létre kell hoznia egy erőforráscsoportot az [az csoport létrehozása](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuális gép [az vm létrehozása,](/cli/azure/vm) és adja meg a cloud-init fájlt `--custom-data cloud_init_add_user.txt` az alábbiak szerint:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

SSH a virtuális gép nyilvános IP-címét az előző parancs kimenetén látható. Adja meg saját **nyilvánosIpAddress** az alábbiak szerint:

```bash
ssh <publicIpAddress>
```

A felhasználó virtuális géphez és a megadott csoportokhoz való hozzáadásának ellenőrzéséhez tekintse meg az */etc/group* fájl tartalmát az alábbiak szerint:

```bash
cat /etc/group
```

A következő példakimenet azt mutatja, hogy a felhasználó a *cloud_init_add_user.txt* fájlból hozzá lett adva a virtuális géphez és a megfelelő csoporthoz:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>További lépések
A konfigurációs módosításokra vonatkozó további felhőalapú init példákat az alábbi témakörben talál:
 
- [További Linux-felhasználó hozzáadása a virtuális géphez](cloudinit-add-user.md)
- [Csomagkezelő futtatása meglévő csomagok frissítéséhez az első rendszerindításkor](cloudinit-update-vm.md)
- [A virtuális gép helyi állomásnevének módosítása](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítése, konfigurációs fájlok frissítése és kulcsok befecskendezése](tutorial-automate-vm-deployment.md)
