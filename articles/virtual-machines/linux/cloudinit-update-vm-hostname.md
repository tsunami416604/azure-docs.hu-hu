---
title: A cloud-init használatával Linux rendszerű virtuális gép az Azure-ban beállított állomásnév |} A Microsoft Docs
description: A cloud-init használata Linux rendszerű virtuális gép testreszabása az Azure CLI-vel létrehozása során
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
ms.openlocfilehash: e985111a28805f861242240a5c2e3d7b6664be4e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996111"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>A cloud-init használatával Linux rendszerű virtuális gép az Azure-ban beállított állomásnév
Ez a cikk bemutatja, hogyan használható [a cloud-init](https://cloudinit.readthedocs.io) konfigurálása egy adott gazdagépnévnek küldenek egy virtuális gép (VM) vagy virtuálisgép-méretezési csoportok (VMSS) kiépítés ideje az Azure-ban. Ezen a cloud-init parancsfájlok futtatása az első rendszerindításkor az Azure-ban kiépített erőforrások után. A cloud-init működése natív módon az Azure és a támogatott Linux-disztribúciók kapcsolatos további információkért lásd: [cloud-init áttekintése](using-cloud-init.md)

## <a name="set-the-hostname-with-cloud-init"></a>Állítsa be az állomásnevet a cloud-Init használatával
Alapértelmezés szerint az állomásnév megegyezik a virtuális gép nevét egy új virtuális gépet az Azure-ban való létrehozásakor.  Ez az alapértelmezett állomásnév módosítani, ha egy virtuális Gépet hoz létre az Azure-ban a cloud-init parancsfájl futtatása [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create), adja meg a cloud-init-fájlt a `--custom-data` váltani.  

A frissítési folyamat működés közben látni, hozzon létre egy fájlt az aktuális felületen *cloud_init_hostname.txt* , és illessze be a következő konfigurációt. Ebben a példában a Cloud shellben, nem a helyi gépén hozzon létre a fájlt. Bármelyik szerkesztőt használhatja. Írja be a `sensible-editor cloud_init_hostname.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. Válassza ki a használandó #1 a **nano** szerkesztő. Győződjön meg arról, hogy a teljes cloud-init-fájlt, másolja, különösen az első sort.  

```yaml
#cloud-config
hostname: myhostname
```

Ez a rendszerkép üzembe helyezése előtt hozzon létre egy erőforráscsoportot kell a [az csoport létrehozása](/cli/azure/group#az_group_create) parancsot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuális Gépet a [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) , és adja meg a cloud-init fájl `--custom-data cloud_init_hostname.txt` módon:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

Létrehozása után az Azure CLI a virtuális Géppel kapcsolatos információkat jeleníti meg. Használja a `publicIpAddress` , a virtuális gép ssh-KAPCSOLATOT. A saját címét adja meg a következők szerint:

```bash
ssh <publicIpAddress>
```

A virtuális gép nevének megtekintéséhez használja a `hostname` paranccsal a következőképpen:

```bash
hostname
```

A virtuális gép jelentse az állomásnevet, ezt az értéket állítsa be a cloud-init-fájl az alábbi példa kimenetében látható módon:

```bash
myhostname
```

## <a name="next-steps"></a>További lépések
Konfigurációs módosítások további a cloud-init példákért tekintse meg a következőket:
 
- [További Linux-felhasználó hozzáadása virtuális Géphez](cloudinit-add-user.md)
- [Az első rendszerindításkor a meglévő csomagokat frissíteni Csomagkezelő futtatása](cloudinit-update-vm.md)
- [Módosítsa a virtuális gép helyi gazdagépnév](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítése, a konfigurációs fájlokat és a kulcsok beszúrása](tutorial-automate-vm-deployment.md)