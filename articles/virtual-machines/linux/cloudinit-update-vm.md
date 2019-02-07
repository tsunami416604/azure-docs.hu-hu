---
title: A cloud-init használatával frissítse és a csomagok telepítése az Azure-beli Linuxos virtuális gép |} A Microsoft Docs
description: A cloud-init használatával frissítheti és a egy Linux rendszerű virtuális gép létrehozása során az Azure CLI-vel a csomagok telepítése
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
ms.date: 04/20/2018
ms.author: rclaus
ms.openlocfilehash: d5f4dc7f4abc13f253a206a63e65faf1106f9c7c
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55766180"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>A cloud-init használatával frissítse és a csomagok telepítése az Azure-beli Linuxos virtuális gép
Ez a cikk bemutatja, hogyan használható [a cloud-init](https://cloudinit.readthedocs.io) a Linux-csomagok frissítése, a virtuális gép (VM) vagy virtuálisgép-méretezési csoportok (VMSS), kiépítés ideje az Azure-ban. Ezen a cloud-init parancsfájlok futtatása az első rendszerindításkor az Azure-ban kiépített erőforrások után. A cloud-init működése natív módon az Azure és a támogatott Linux-disztribúciók kapcsolatos további információkért lásd: [cloud-init áttekintése](using-cloud-init.md)

## <a name="update-a-vm-with-cloud-init"></a>A virtuális gép frissítése a cloud-Init használatával
Biztonsági okokból érdemes lehet, hogy konfiguráljon egy virtuális Gépet a alkalmazni a legújabb frissítéseket az első rendszerindításkor. A cloud-init különböző Linux-disztribúciók működik, mert nincs szükség a megadásához `apt` vagy `yum` a package manager számára. Helyette megadhat `package_upgrade` és hagyhatja, hogy a cloud-init folyamat határozza meg a megfelelő mechanizmusa a használt disztribúció. Ez a munkafolyamat lehetővé teszi, hogy az ugyanazon a cloud-init-parancsfájlok disztribúciókhoz között.

A frissítési folyamat működés közben látni, hozzon létre egy fájlt az aktuális felületen *cloud_init_upgrade.txt* , és illessze be a következő konfigurációt. Ebben a példában a Cloud shellben, nem a helyi gépén hozzon létre a fájlt. Bármelyik szerkesztőt használhatja. Írja be a `sensible-editor cloud_init_upgrade.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. Válassza ki a használandó #1 a **nano** szerkesztő. Győződjön meg arról, hogy a teljes cloud-init-fájlt, másolja, különösen az első sort.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Ez a rendszerkép üzembe helyezése előtt hozzon létre egy erőforráscsoportot kell a [az csoport létrehozása](/cli/azure/group) parancsot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuális Gépet a [az virtuális gép létrehozása](/cli/azure/vm) , és adja meg a cloud-init fájl `--custom-data cloud_init_upgrade.txt` módon:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

SSH nyilvános IP-címet a virtuális gép az előző parancs kimenetében látható. Adja meg a saját **publicIpAddress** módon:

```bash
ssh <publicIpAddress>
```

Futtassa a Csomagkezelő eszközt és a frissítések keresése.

```bash
sudo yum update
```

Cloud-Init használatával ellenőrzi, és telepített frissítések a rendszerindító, nincs további frissítések a alkalmazni kell.  Megjelenik a frissítési folyamat, a módosított csomagok száma, valamint telepítésének `httpd` futtatásával `yum history` , és tekintse át a kimenet az alábbihoz hasonló.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>További lépések
Konfigurációs módosítások további a cloud-init példákért tekintse meg a következőket:
 
- [További Linux-felhasználó hozzáadása virtuális Géphez](cloudinit-add-user.md)
- [Az első rendszerindításkor a meglévő csomagokat frissíteni Csomagkezelő futtatása](cloudinit-update-vm.md)
- [Módosítsa a virtuális gép helyi gazdagépnév](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítése, a konfigurációs fájlokat és a kulcsok beszúrása](tutorial-automate-vm-deployment.md)
