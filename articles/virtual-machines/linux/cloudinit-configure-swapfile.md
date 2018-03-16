---
title: "A lapozófájl konfigurálása a Linux virtuális gép felhőalapú inicializálás segítségével |} Microsoft Docs"
description: "A lapozófájl konfigurálása a Linux virtuális gép létrehozása az Azure CLI 2.0 felhő inicializálás használata"
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
ms.openlocfilehash: 88a141922f113caf7ad67c89de48f84a821f7ba3
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="use-cloud-init-to-configure-a-swapfile-on-a-linux-vm"></a>A lapozófájl konfigurálása a Linux virtuális gép felhőalapú inicializálás segítségével
Ez a cikk bemutatja, hogyan használható [felhő inicializálás](https://cloudinit.readthedocs.io) konfigurálása a lapozófájl különböző Linux terjesztéseket. A lapozófájl hagyományosan konfigurálása által a Linux ügynök (WALA) alapján mely azokat a terjesztéseket egy szükséges.  Ez a dokumentum fog szerkezeti üzembe helyezési időt felhő inicializálás közben a lapozófájl kiépítése során.  Felhő inicializálás működése natív Azure-ban és a Linux támogatott disztribúciókkal kapcsolatos további információkért lásd: [felhő inicializálás áttekintése](using-cloud-init.md)

## <a name="create-swapfile-for-ubuntu-based-images"></a>Lapozófájl alapú Ubuntu lemezképek létrehozása
Ubuntu gyűjtemény lemezképei Azure alapértelmezés szerint ne hozzon létre lapozó. Engedélyezi a lapozófájl-kapacitás fájl konfigurálást Virtuálisgép-létrehozásnál alkalommal használatával a felhő inicializálás során – tekintse meg a [AzureSwapPartitions dokumentum](https://wiki.ubuntu.com/AzureSwapPartitions) Ubuntu wiki.

## <a name="create-swapfile-for-redhat-and-centos-based-images"></a>Lapozófájl RedHat és CentOS alapján lemezképek létrehozása

Hozzon létre egy fájlt az aktuális rendszerhéjban nevű *cloud_init_swapfile.txt* , majd illessze be a következő konfigurációt. Hozzon létre a fájl ebben a példában a felhő rendszerhéj nem a helyi számítógépen. Bármelyik szerkesztőt használhatja. Írja be a `sensible-editor cloud_init_swapfile.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. Válassza ki a használandó #1 a **nano** szerkesztő. Győződjön meg arról, hogy az egész felhő inicializálás fájl megfelelően lett lemásolva különösen az első sor.  

```yaml
#cloud-config
disk_setup:
  ephemeral0:
    table_type: gpt
    layout: [66, [33,82]]
    overwrite: true
fs_setup:
  - device: ephemeral0.1
    filesystem: ext4
  - device: ephemeral0.2
    filesystem: swap
mounts:
  - ["ephemeral0.1", "/mnt"]
  - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
```

Ez a rendszerkép telepítés megkezdése előtt hozzon létre egy erőforráscsoportot a kell a [az csoport létrehozása](/cli/azure/group#az_group_create) parancsot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Ezután hozzon létre a virtuális gép és [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) , és adja meg a felhő inicializálás fájl `--custom-data cloud_init_swapfile.txt` az alábbiak szerint:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swapfile.txt \
  --generate-ssh-keys 
```

## <a name="verify-swapfile-was-created"></a>Lapozófájl létrejött-e
SSH-kapcsolatot a virtuális Gépet, a fenti parancs kimenetében megjelennek a nyilvános IP-címét. Adja meg a saját **publicIpAddress** az alábbiak szerint:

```bash
ssh <publicIpAddress>
```

Miután SSH'ed be a virtuális géphez, ellenőrizze a létrejött-e a lapozófájl

```bash
swapon -s
```

Ez a parancs kimenetében kell kinéznie:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Ha egy meglévő Azure-rendszerképet, amely rendelkezik beállított lapozófájl rendelkezik, és új képek swap fájl konfigurációjának módosításához, el kell távolítania a meglévő lapozófájl. "Testreszabás képek kiépítését felhő inicializálás" dokumentum további részletekért tekintse meg.

## <a name="next-steps"></a>További lépések
Konfigurációs módosítások további felhőalapú inicializálás példákért lásd a következő:
 
- [Egy további Linux-felhasználó hozzáadása a virtuális gépek](cloudinit-add-user.md)
- [Futtassa a Csomagkezelő első indításakor meglévő csomagok frissítése](cloudinit-update-vm.md)
- [Módosítsa a virtuális gép helyi állomásnév](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítését, a konfigurációs fájlokat és a kulcsok beszúrása](tutorial-automate-vm-deployment.md)
