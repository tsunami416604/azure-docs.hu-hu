---
title: Csomagok frissítése és telepítése a Cloud-init használatával Linux rendszerű virtuális gépen az Azure-ban
description: A Cloud-init használata csomagok frissítésére és telepítésére Linux rendszerű virtuális gépen az Azure CLI-vel való létrehozás során
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 04/20/2018
ms.author: rclaus
ms.openlocfilehash: ddea412598e02be7d71d5a3efafa444a5dc19e8c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036725"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Csomagok frissítése és telepítése a Cloud-init használatával Linux rendszerű virtuális gépen az Azure-ban
Ez a cikk bemutatja, hogyan lehet a [Cloud-init](https://cloudinit.readthedocs.io) használatával frissíteni a csomagokat egy linuxos virtuális GÉPEN (VM) vagy virtuálisgép-méretezési csoportokon (VMSS) az Azure-ban kiépített időpontban. Ezek a felhő-init parancsfájlok az első rendszerindítás során futnak az Azure-beli erőforrások kiépítés után. További információ arról, hogyan működik a Cloud-init natív módon az Azure-ban és a támogatott Linux-disztribúciókban: a [Cloud-init áttekintése](using-cloud-init.md)

## <a name="update-a-vm-with-cloud-init"></a>Virtuális gép frissítése a Cloud-init használatával
Biztonsági okokból érdemes lehet úgy konfigurálni a virtuális gépet, hogy az első rendszerindításkor alkalmazza a legújabb frissítéseket. Mivel a Cloud-init a különböző Linux-disztribúciókban működik, nem kell megadnia `apt` vagy `yum` a csomagkezelő számára. Ehelyett definiálja `package_upgrade`, és hagyja, hogy a Cloud-init folyamat meghatározza a megfelelő mechanizmust a használatban lévő disztribúcióhoz. Ez a munkafolyamat lehetővé teszi, hogy ugyanazt a Cloud-init parancsfájlt használja a disztribúciók között.

A frissítési folyamat működés közbeni megtekintéséhez hozzon létre egy fájlt a *cloud_init_upgrade. txt* nevű aktuális rendszerhéjban, és illessze be a következő konfigurációt. Ebben a példában hozza létre a fájlt a Cloud Shell nem a helyi gépen. Bármelyik szerkesztőt használhatja. Írja be a `sensible-editor cloud_init_upgrade.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. A **Nano** Editor használatához válassza a #1 lehetőséget. Győződjön meg arról, hogy a teljes Cloud-init fájl megfelelően van másolva, különösen az első sorban.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

A rendszerkép telepítése előtt létre kell hoznia egy erőforráscsoportot az az [Group Create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuális gépet az [az VM Create](/cli/azure/vm) paranccsal, és határozza meg a Cloud-init fájlt `--custom-data cloud_init_upgrade.txt` a következőképpen:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

SSH-t az előző parancs kimenetében látható virtuális gép nyilvános IP-címére. Adja meg saját **publicIpAddress** a következőképpen:

```bash
ssh <publicIpAddress>
```

Futtassa a csomagkezelő eszközt, és keressen frissítéseket.

```bash
sudo yum update
```

Mivel a Cloud-init bejelölte és telepítette a frissítéseket a rendszerindításhoz, nem kell további frissítéseket alkalmaznia.  Ekkor megjelenik a frissítési folyamat, a módosított csomagok száma, valamint az `httpd` telepítése `yum history` futtatásával, és az alábbihoz hasonló kimenet tekinthető meg.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>Következő lépések
További felhő-inicializálási példákat a konfiguráció változásairól a következő témakörben talál:
 
- [További linuxos felhasználó hozzáadása egy virtuális géphez](cloudinit-add-user.md)
- [Csomagkezelő futtatása a meglévő csomagok frissítéséhez az első rendszerindításkor](cloudinit-update-vm.md)
- [Virtuális gép helyi gazdagépének módosítása](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítése, konfigurációs fájlok frissítése és kulcsok behelyezése](tutorial-automate-vm-deployment.md)
