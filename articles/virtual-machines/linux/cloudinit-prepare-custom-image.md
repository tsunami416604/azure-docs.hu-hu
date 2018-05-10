---
title: Azure virtuális gép lemezképének elkészítése inicializálás felhőben való használatra |} Microsoft Docs
description: Egy már meglévő Azure Virtuálisgép-lemezkép előkészítése felhő inicializálás központi telepítése
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
ms.openlocfilehash: faa28a6b28c721e4088ccfbb00514be7f605f3e2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Egy meglévő Linux Azure Virtuálisgép-lemezkép előkészítése inicializálás felhőben való használatra
Ez a cikk bemutatja, hogyan egy meglévő Azure virtuális gépet, és készítse elő a újratelepített és felhő inicializálás használatra kész. A létrejövő kép segítségével telepítsen egy új virtuális gép vagy virtuálisgép-méretezési csoportok - vagy amelyek sikerült majd további szabhatja testre felhő inicializálás központi telepítéskor.  Ezen felhő inicializálás parancsfájlok futtatása az első betöltés után az erőforrásokat az Azure-ban kiépített. Felhő inicializálás működése natív Azure-ban és a Linux támogatott disztribúciókkal kapcsolatos további információkért lásd: [felhő inicializálás áttekintése](using-cloud-init.md)

## <a name="prerequisites"></a>Előfeltételek
Ez a dokumentum azt feltételezi, hogy már rendelkezik a Linux operációs rendszer támogatott verziója fut az Azure virtuális gép. Az igényeinek, a gép már beállította a szükséges modulokat telepíteni, minden szükséges frissítés feldolgozása és tesztelt úgy, hogy az megfelel-e a követelményeknek. 

## <a name="preparing-rhel-74--centos-74"></a>Felkészülés az RHEL 7.4 / CentOS 7.4
SSH a Linux virtuális gép be kell, és a felhő inicializálás telepítéséhez a következő parancsokat.

```bash
sudo yum install -y cloud-init gdisk
sudo yum check-update cloud-init -y
sudo yum install cloud-init -y
```

Frissítés a `cloud_init_modules` szakasz `/etc/cloud/cloud.cfg` tartalmazza a következő modult:
```bash
- disk_setup
- mounts
```

Itt látható egy minta milyen egy általános célú `cloud_init_modules` szakasz tűnik.
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
Üzembe helyezési és kezelési elmúló lemezek kapcsolatos feladatot frissítenie kell a `/etc/waagent.conf`. A következő parancsokat a megfelelő beállítások frissítése. 
```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
```
Engedélyezése csak Azure adatforrásként az Azure Linux ügynök hozzon létre egy új fájlt `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` az Ön által választott szerkesztő használatával a következő sort:

```bash
# This configuration file is provided by the WALinuxAgent package.
datasource_list: [ Azure ]
```

Adja hozzá a konfigurációs egy függőben lévő állomásnév regisztrációs hiba elhárítása.
```bash
cat > /etc/cloud/hostnamectl-wrapper.sh <<\EOF
#!/bin/bash -e
if [[ -n $1 ]]; then
  hostnamectl set-hostname $1
else
  hostname
fi
EOF

chmod 0755 /etc/cloud/hostnamectl-wrapper.sh

cat > /etc/cloud/cloud.cfg.d/90-hostnamectl-workaround-azure.cfg <<EOF
# local fix to ensure hostname is registered
datasource:
  Azure:
    hostname_bounce:
      hostname_command: /etc/cloud/hostnamectl-wrapper.sh
EOF
```

Ha a meglévő Azure-rendszerképet lapozófájl konfigurálva van, és új lemezképet felhő inicializálás swap fájl konfigurációjának módosításához, el kell távolítania a meglévő lapozófájl.

RedHat képek - alapú kövesse a következő RedHat dokumentum ismertető útmutató [távolítsa el a lapozófájl](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/5/html/Deployment_Guide/s2-swap-removing-file.html).

CentOS a képekkel swapfile engedélyezve van a lapozófájl kikapcsolása a következő parancsot futtathatja:
```bash
sudo swapoff /mnt/resource/swapfile
```

Győződjön meg arról, a lapozófájl hivatkozás eltávolítják az `/etc/fstab` -az alábbiakhoz hasonlóan a következő az alábbiakat:
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
## <a name="extra-step-for-cloud-init-prepared-image"></a>További lépés a felhő inicializálás lemezkép előkészítése
> [!NOTE]
> Ha a kép korábban egy **felhő inicializálás** előkészített és konfigurált kép, végre kell hajtani a következő lépéseket.

A következő három parancsokat csak használhatók, ha a virtuális gép testreszabása speciális forráslemezkép kell korábban felhő inicializálás lett kiépítve.  NEM kell futtatnia ezeket, ha a lemezkép az Azure Linux ügynök használatával lett konfigurálva.

```bash
sudo rm -rf /var/lib/cloud/instances/* 
sudo rm -rf /var/log/cloud-init*
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Linux-ügynök véglegesítése beállítás 
Minden Azure platformon képek a Azure Linux ügynök van telepítve, attól függetlenül történik, ha konfigurálása által a felhő-init vagy nem rendelkezik.  A következő parancsot a felhasználó a Linux rendszerű számítógépről megszüntetés befejezéséhez. 

```bash
sudo waagent -deprovision+user -force
```

Az Azure Linux ügynök deprovision parancsokkal kapcsolatos további információkért lásd: a [Azure Linux ügynök](../extensions/agent-linux.md) további részleteket.

Lépjen ki az SSH-munkamenetet, majd a rendszerhéjakba futtassa a következő AzureCLI parancsokat felszabadítani, generalize, és hozzon létre egy új Azure Virtuálisgép-lemezkép.  Cserélje le `myResourceGroup` és `sourceVmName` a megfelelő információkkal a sourceVM tükrözve.

```bash
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>További lépések
Konfigurációs módosítások további felhőalapú inicializálás példákért lásd a következő:
 
- [Egy további Linux-felhasználó hozzáadása a virtuális gépek](cloudinit-add-user.md)
- [Futtassa a Csomagkezelő első indításakor meglévő csomagok frissítése](cloudinit-update-vm.md)
- [Módosítsa a virtuális gép helyi állomásnév](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítését, a konfigurációs fájlokat és a kulcsok beszúrása](tutorial-automate-vm-deployment.md)
