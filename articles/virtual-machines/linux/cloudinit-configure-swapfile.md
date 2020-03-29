---
title: A felhőalapú init használata egy linuxos virtuális gép en lévő cserepartíció konfigurálásához
description: A felhőalapú init használata egy linuxos virtuális gép ben lévő cserepartíció konfigurálásához az Azure CLI-vel való létrehozás során
author: rickstercdn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 1247652e536042ee249054d86aed3c3f8e7aa7bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969201"
---
# <a name="use-cloud-init-to-configure-a-swap-partition-on-a-linux-vm"></a>A felhőalapú init használata egy linuxos virtuális gép en lévő cserepartíció konfigurálásához
Ez a cikk bemutatja, hogyan használhatja a [cloud-init-et](https://cloudinit.readthedocs.io) a swap partíció konfigurálásához különböző Linux disztribúciókon. A swap partíciót hagyományosan a Linux-ügynök (WALA) konfigurálta, amely alapján a disztribúciók igényelték.  Ez a dokumentum ismerteti a folyamatot a swap partíció igény szerinti kiépítése során a felhő-init.  Ha többet szeretne tudni arról, hogy a cloud-init hogyan működik natívan az Azure-ban és a támogatott Linux-disztribúciókban, tekintse meg a [cloud-init áttekintését.](using-cloud-init.md)

## <a name="create-swap-partition-for-ubuntu-based-images"></a>Cserepartíció létrehozása Ubuntu alapú képekhez
Alapértelmezés szerint az Azure-ban az Ubuntu galériaképek nem hoznak létre lapozópartíciókat. A swap partíció konfigurációjának engedélyezéséhez a virtuális gép kiépítése során a cloud-init használatával – tekintse meg az [AzureSwapPartitions dokumentumot](https://wiki.ubuntu.com/AzureSwapPartitions) az Ubuntu wiki.

## <a name="create-swap-partition-for-red-hat-and-centos-based-images"></a>Cserepartíció létrehozása Red Hat és CentOS alapú képekhez

Hozzon létre egy fájlt a *cloud_init_swappart.txt* nevű jelenlegi rendszerhéjban, és illessze be a következő konfigurációt. Ebben a példában hozza létre a fájlt a Cloud Shell nem a helyi gépen. Bármelyik szerkesztőt használhatja. Írja be a `sensible-editor cloud_init_swappart.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. Válassza ki #1 a **nanoszerkesztő** használatához. Győződjön meg arról, hogy a teljes felhő-init fájl másolása helyesen, különösen az első sorban.  

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

A lemezkép telepítése előtt létre kell hoznia egy erőforráscsoportot az [az csoport létrehozása](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuális gép [az vm létrehozása,](/cli/azure/vm) és adja meg a cloud-init fájlt `--custom-data cloud_init_swappart.txt` az alábbiak szerint:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swappart.txt \
  --generate-ssh-keys 
```

## <a name="verify-swap-partition-was-created"></a>A swap partíció létrehozásának ellenőrzése
SSH a virtuális gép nyilvános IP-címét az előző parancs kimenetén látható. Adja meg saját **nyilvánosIpAddress** az alábbiak szerint:

```bash
ssh <publicIpAddress>
```

Miután SSH'ed a vm, ellenőrizze, hogy a swap partíció jött létre

```bash
swapon -s
```

A parancs kimenetének így kell kinéznie:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Ha rendelkezik egy meglévő Azure-lemezkép, amely a swap partíció konfigurálva van, és módosítani szeretné a swap partíció konfigurációját az új rendszerképek, távolítsa el a meglévő swap partíciót. További részletekért olvassa el a "Képek testreszabása felhőalapú init) dokumentumban.

## <a name="next-steps"></a>További lépések
A konfigurációs módosításokra vonatkozó további felhőalapú init példákat az alábbi témakörben talál:
 
- [További Linux-felhasználó hozzáadása a virtuális géphez](cloudinit-add-user.md)
- [Csomagkezelő futtatása meglévő csomagok frissítéséhez az első rendszerindításkor](cloudinit-update-vm.md)
- [A virtuális gép helyi állomásnevének módosítása](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítése, konfigurációs fájlok frissítése és kulcsok befecskendezése](tutorial-automate-vm-deployment.md)
