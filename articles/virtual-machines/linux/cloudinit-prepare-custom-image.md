---
title: Azure Virtuálisgép-lemezkép előkészítése a cloud-init való használatra |} A Microsoft Docs
description: Egy már meglévő Azure-beli Virtuálisgép-lemezkép előkészítése az üzembe helyezés, a cloud-Init használatával
services: virtual-machines-linux
documentationcenter: ''
author: danis
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 02/27/2019
ms.author: danis
ms.openlocfilehash: da539a5bebc1613115f89a7b47c513ce486b5e3a
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337311"
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Meglévő Azure-beli Linux rendszerű Virtuálisgép-rendszerképet használja a cloud-Init használatával előkészítése
Ez a cikk bemutatja, hogyan egy meglévő Azure virtuális gépen és készítse elő a újratelepített, a cloud-init használatra kész lesz. Az eredményül kapott kép egy új virtuális gépet vagy virtuális gép méretezési csoportok – amely bármelyike sikerült majd további testre a cloud-init üzembe helyezéskor üzembe helyezéséhez használható.  Ezen a cloud-init parancsfájlok futtatása az első rendszerindításkor az Azure-ban kiépített erőforrások után. A cloud-init működése natív módon az Azure és a támogatott Linux-disztribúciók kapcsolatos további információkért lásd: [cloud-init áttekintése](using-cloud-init.md)

## <a name="prerequisites"></a>Előfeltételek
Jelen dokumentum céljából feltételezzük, hogy már rendelkezik a Linux operációs rendszer támogatott verziója fut az Azure virtuális gép. Igényei, a gép már konfigurálta a szükséges modulok telepítése, minden szükséges frissítés feldolgozása és tesztelt, annak érdekében, hogy megfelelnek-e a követelményeknek. 

## <a name="preparing-rhel-76--centos-76"></a>Felkészülés az RHEL 7.6 / CentOS 7.6
Az SSH a Linux rendszerű virtuális Gépre való kell, és futtassa az alábbi parancsokat a cloud-init telepítéséhez.

```bash
sudo yum makecache fast
sudo yum install -y gdisk cloud-utils-growpart
sudo yum install - y cloud-init 
```

Frissítés a `cloud_init_modules` szakasz `/etc/cloud/cloud.cfg` a következő modulok tartalmazza:
```bash
- disk_setup
- mounts
```

Íme egy példa, mely egy általános célú `cloud_init_modules` szakasz néz ki.
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
Kiépítés és ideiglenes lemezek kezelése kapcsolatos feladatok számos frissítenie kell a `/etc/waagent.conf`. Futtassa az alábbi parancsokat a megfelelő beállítások frissítéséhez. 
```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
cp /lib/systemd/system/waagent.service /etc/systemd/system/waagent.service
sed -i 's/After=network-online.target/WantedBy=cloud-init.service\\nAfter=network.service systemd-networkd-wait-online.service/g' /etc/systemd/system/waagent.service
systemctl daemon-reload
cloud-init clean
```
Engedélyezése csak az Azure adatforrásként az Azure Linux-ügynök hozzon létre egy új fájlt `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` segítségével egy tetszőleges szövegszerkesztőben a következő sorokat:

```bash
# Azure Data Source config
datasource_list: [ Azure ]
datasource:
   Azure:
     agent_command: [systemctl, start, waagent, --no-block]
```

Ha a meglévő Azure-rendszerképet egy lapozófájl konfigurálva van, és meg szeretné változtatni a lapozófájl-kapacitás fájl konfigurációját a cloud-init használatával új képek, távolítsa el a meglévő lapozófájl szeretné.

Red Hat-alapú lemezképek – kövesse a következő Red Hat dokumentum elmagyarázza a hogyan [távolítsa el a lapozófájl](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/swap-removing-file).

A lapozófájl engedélyezve van a CentOS-lemezképek esetében kapcsolja ki a lapozófájl a következő parancsot futtathatja:
```bash
sudo swapoff /mnt/resource/swapfile
```

Győződjön meg arról, a lapozófájl hivatkozás eltávolítják az `/etc/fstab` -valamit a következő példához hasonlóan kell kinéznie:
```text
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

Mentse a helyet, és távolítsa el a lapozófájl futtathatja a következő parancsot:
```bash
rm /mnt/resource/swapfile
```
## <a name="extra-step-for-cloud-init-prepared-image"></a>A cloud-init előkészített lemezképek extra lépés
> [!NOTE]
> Ha a rendszerkép korábban egy **a cloud-init** előkészített és konfigurált lemezképet kell tennie a következő lépéseket.

A következő három parancsokat csak akkor használatosak, ha a virtuális gép testreszabása speciális forráslemezkép kell korábban kiépítette a cloud-init.  NEM kell lefuttatni ezeket, ha a rendszerképet az Azure Linux-ügynök használatával lett konfigurálva.

```bash
sudo cloud-init clean --logs
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Linux-ügynök véglegesítése beállítás 
Az összes Azure-platform-lemezképek az Azure Linux-ügynök telepítve van, függetlenül attól, ha konfigurálása a cloud-Init eszközt, vagy nem rendelkezik.  Futtassa a következő parancsot a befejezéséhez a megszüntetés a felhasználó a Linux rendszerű gépen. 

```bash
sudo waagent -deprovision+user -force
```

Az Azure Linux-ügynök megszüntetési parancsokkal kapcsolatos további információkért lásd: a [Azure Linux-ügynök](../extensions/agent-linux.md) további részletekért.

Az SSH-munkamenetet, majd a bash-rendszerhéjból, futtassa a következő AzureCLI-parancsok felszabadítása, generalize, és hozzon létre egy új Azure-beli Virtuálisgép-rendszerképet.  Cserélje le `myResourceGroup` és `sourceVmName` a sourceVM tükröző a megfelelő információkkal.

```bash
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>További lépések
Konfigurációs módosítások további a cloud-init példákért tekintse meg a következőket:
 
- [További Linux-felhasználó hozzáadása virtuális Géphez](cloudinit-add-user.md)
- [Az első rendszerindításkor a meglévő csomagokat frissíteni Csomagkezelő futtatása](cloudinit-update-vm.md)
- [Módosítsa a virtuális gép helyi gazdagépnév](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítése, a konfigurációs fájlokat és a kulcsok beszúrása](tutorial-automate-vm-deployment.md)
