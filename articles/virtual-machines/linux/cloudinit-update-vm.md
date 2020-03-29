---
title: Felhőalapú init használata Linuxos virtuális gépben az Azure-ban
description: A felhőalapú init használata csomagok frissítéséhez és telepítéséhez Linux os virtuális gépben az Azure CLI-vel való létrehozás során
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/20/2018
ms.author: cynthn
ms.openlocfilehash: 7b7a03572a001fc6d5114635b33510f1a4b1bc70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969154"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>A cloud-init használatával frissítheti és telepítheti a csomagokat linuxos virtuális gépben az Azure-ban
Ez a cikk bemutatja, hogyan használhatja [a cloud-init](https://cloudinit.readthedocs.io) csomagok frissítésére egy Linux virtuális gépen (VM) vagy virtuálisgép-méretezési csoportok kiépítése során az Azure-ban. Ezek a felhőalapú init-parancsfájlok az azure-beli erőforrások kiépítése után futnak az első rendszerindításkor. Ha többet szeretne tudni arról, hogy a cloud-init hogyan működik natívan az Azure-ban és a támogatott Linux-disztribúciókban, tekintse meg a [cloud-init áttekintését.](using-cloud-init.md)

## <a name="update-a-vm-with-cloud-init"></a>Virtuális gép frissítése felhőalapú inittel
Biztonsági okokból érdemes konfigurálni a virtuális gép, hogy alkalmazza a legújabb frissítéseket az első rendszerindításkor. Mivel a cloud-init különböző Linux disztribúciók `apt` között `yum` működik, nincs szükség a csomagkezelő megadására vagy megadására. Ehelyett definiálja, `package_upgrade` és hagyja, hogy a felhő-init folyamat határozza meg a megfelelő mechanizmust a distro használatban. Ez a munkafolyamat lehetővé teszi, hogy ugyanazokat a felhő-init parancsfájlokat használja a disti-k között.

A frissítési folyamat működés közbeni megtekintéséhez hozzon létre egy fájlt az *aktuális rendszerhéjban, amelynek neve cloud_init_upgrade.txt,* és illessze be a következő konfigurációt. Ebben a példában hozza létre a fájlt a Cloud Shell nem a helyi gépen. Bármelyik szerkesztőt használhatja. Írja be a `sensible-editor cloud_init_upgrade.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. Válassza ki #1 a **nanoszerkesztő** használatához. Győződjön meg arról, hogy a teljes felhő-init fájl másolása helyesen, különösen az első sorban.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

A lemezkép telepítése előtt létre kell hoznia egy erőforráscsoportot az [az csoport létrehozása](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuális gép [az vm létrehozása,](/cli/azure/vm) és adja meg a cloud-init fájlt `--custom-data cloud_init_upgrade.txt` az alábbiak szerint:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

SSH a virtuális gép nyilvános IP-címét az előző parancs kimenetén látható. Adja meg saját **nyilvánosIpAddress** az alábbiak szerint:

```bash
ssh <publicIpAddress>
```

Futtassa a csomagkezelő eszközt, és ellenőrizze a frissítéseket.

```bash
sudo yum update
```

Mivel a cloud-init ellenőrizte és telepítette a frissítéseket a rendszerindításkor, nem kell további frissítéseket alkalmaznia.  Láthatja a frissítési folyamatot, a módosított csomagok `httpd` számát, `yum history` valamint a telepítést az alábbihoz hasonló kimenet futtatásával és felülvizsgálatával.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>További lépések
A konfigurációs módosításokra vonatkozó további felhőalapú init példákat az alábbi témakörben talál:
 
- [További Linux-felhasználó hozzáadása a virtuális géphez](cloudinit-add-user.md)
- [Csomagkezelő futtatása meglévő csomagok frissítéséhez az első rendszerindításkor](cloudinit-update-vm.md)
- [A virtuális gép helyi állomásnevének módosítása](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítése, konfigurációs fájlok frissítése és kulcsok befecskendezése](tutorial-automate-vm-deployment.md)
