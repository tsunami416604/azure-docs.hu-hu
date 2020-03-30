---
title: Az Azure VM-lemezkép előkészítése a felhőalapú környezettel való használatra
description: Egy már meglévő Azure Virtuálisgép-lemezkép előkészítése a felhőalapú telepítéshez
author: danis
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/24/2019
ms.author: danis
ms.openlocfilehash: fef41f4dc90c03e3efbe4c8a75e495c26eec64b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066819"
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Meglévő Linux Azure virtuális géplemezkép előkészítése felhőalapú informatikai rendszerrel való használatra
Ez a cikk bemutatja, hogyan vehet egy meglévő Azure virtuális gépet, és előkészítheti újraüzembe helyezésére, és készen áll a felhőalapú init használatára. Az eredményül kapott lemezkép egy új virtuális gép vagy virtuálisgép-méretezési készletek üzembe helyezésére használható , amelyek bármelyikét a felhő-init üzembe helyezéskor tovább testreszabhatja.  Ezek a felhőalapú init-parancsfájlok az azure-beli erőforrások kiépítése után futnak az első rendszerindításkor. Ha többet szeretne tudni arról, hogy a cloud-init hogyan működik natívan az Azure-ban és a támogatott Linux-disztribúciókban, tekintse meg a [cloud-init áttekintését.](using-cloud-init.md)

## <a name="prerequisites"></a>Előfeltételek
Ez a dokumentum feltételezi, hogy már rendelkezik egy futó Azure virtuális géppel, amely a Linux operációs rendszer támogatott verzióját futtatja. Már beállította a gépet az Ön igényeinek megfelelően, telepítette az összes szükséges modult, feldolgozta az összes szükséges frissítést, és tesztelte, hogy megbizonyosodjon arról, hogy megfelel az Ön igényeinek. 

## <a name="preparing-rhel-76--centos-76"></a>Felkészülés RHEL 7,6 / CentOS 7,6
A felhőalapú init telepítéséhez ssh-t kell futtatnia a Linux virtuális gépbe, és futtatnia kell a következő parancsokat.

```bash
sudo yum makecache fast
sudo yum install -y gdisk cloud-utils-growpart
sudo yum install - y cloud-init 
```

Frissítse `cloud_init_modules` a `/etc/cloud/cloud.cfg` szakaszt úgy, hogy az a következő modulokat tartalmazza:

```bash
- disk_setup
- mounts
```

Itt van egy példa, hogy `cloud_init_modules` egy általános célú szakasz néz ki.

```bash
cloud_init_modules:
 - migrator
 - bootcmd
 - write-files
 - growpart
 - resizefs
 - disk_setup
 - mounts
 - set_hostname
 - update_hostname
 - update_etc_hosts
 - rsyslog
 - users-groups
 - ssh
```

A rövid élettartamú lemezek kiépítésével és kezelésével kapcsolatos számos `/etc/waagent.conf`feladatot frissíteni kell a rendszerben. A megfelelő beállítások frissítéséhez futtassa a következő parancsokat.

```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
cloud-init clean
```

Csak az Azure Linux-ügynök adatforrásaként engedélyezze `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` az Azure Linux-ügynök adatforrásaként egy új fájlt egy ön által kiválasztott szerkesztő használatával a következő súrolóval:

```bash
# Azure Data Source config
datasource_list: [ Azure ]
```

Ha a meglévő Azure-lemezkép egy swap fájl konfigurálva van, és módosítani szeretné a swap fájl konfigurációját az új lemezképek felhő-init használatával, el kell távolítania a meglévő cserefájlt.

Red Hat alapú képek esetén - kövesse a következő Red Hat dokumentum utasításait, amely elmagyarázza, hogyan [távolíthatja el a cserefájlt](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/swap-removing-file).

A swapfile-t engedélyező CentOS-lemezképek esetén a következő paranccsal kikapcsolhatja a swapfájlt:

```bash
sudo swapoff /mnt/resource/swapfile
```

Győződjön meg arról, `/etc/fstab` hogy a swapfile hivatkozás törlődik - a következő kimenethez hasonlóan kell kinéznie:

```output
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

Helytakarékosság és a lapozófájl eltávolítása érdekében futtassa a következő parancsot:

```bash
rm /mnt/resource/swapfile
```

## <a name="extra-step-for-cloud-init-prepared-image"></a>Extra lépés a felhő-init elkészített kép
> [!NOTE]
> Ha a rendszerkép korábban egy **felhő-init** előkészített és konfigurált lemezkép volt, a következő lépéseket kell tennie.

A következő három parancs csak akkor használatos, ha a virtuális gép, amelyet új, speciális forrásrendszernek szab, korábban a cloud-init által kiépített.  Nem kell futtatni ezeket, ha a rendszerkép az Azure Linux-ügynök használatával lett konfigurálva.

```bash
sudo cloud-init clean --logs
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>A Linux-ügynök beállításának véglegesítése 
Az összes Azure-platformrendszerkép az Azure Linux-ügynök telepítve van, függetlenül attól, hogy a felhő-init konfigurálta-e vagy sem.  Futtassa a következő parancsot a felhasználó Linux-gépről való deprovisioning befejezéséhez. 

```bash
sudo waagent -deprovision+user -force
```

Az Azure Linux Ügynök deprovision parancsokkal kapcsolatos további információkért tekintse meg az [Azure Linux-ügynök](../extensions/agent-linux.md) további részleteket.

Lépjen ki az SSH-munkamenetből, majd a bash rendszerhéjból futtassa a következő AzureCLI-parancsokat a felszabadításhoz, az általánosításhoz és egy új Azure virtuális géplemezkép létrehozásához.  Cserélje `myResourceGroup` `sourceVmName` ki, és a megfelelő információkat tükröző sourceVM.

```azurecli
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>További lépések
A konfigurációs módosításokra vonatkozó további felhőalapú init példákat az alábbi témakörben talál:
 
- [További Linux-felhasználó hozzáadása a virtuális géphez](cloudinit-add-user.md)
- [Csomagkezelő futtatása meglévő csomagok frissítéséhez az első rendszerindításkor](cloudinit-update-vm.md)
- [A virtuális gép helyi állomásnevének módosítása](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítése, konfigurációs fájlok frissítése és kulcsok befecskendezése](tutorial-automate-vm-deployment.md)
