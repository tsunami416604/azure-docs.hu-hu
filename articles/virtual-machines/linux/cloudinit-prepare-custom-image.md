---
title: Azure-beli virtuális gép rendszerképének előkészítése a Cloud-init használatával
description: Meglévő Azure-beli virtuálisgép-rendszerkép előkészítése a Cloud-init használatával történő üzembe helyezéshez
author: danis
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/24/2019
ms.author: danis
ms.openlocfilehash: 73df3a12ebea3b94563d02eda8f1211401d1ae3f
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969179"
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Meglévő linuxos Azure VM-rendszerkép előkészítése a Cloud-init használatával
Ebből a cikkből megtudhatja, hogyan készíthet meglévő Azure-beli virtuális gépet, és hogyan készítheti elő újra az üzembe helyezést, és készen áll a Cloud-init használatára. Az eredményül kapott lemezkép új virtuális gép vagy virtuálisgép-méretezési csoportok üzembe helyezésére használható, amelyek ezt követően a Cloud-init használatával további testre szabhatók a telepítési időpontnál.  Ezek a felhő-init parancsfájlok az első rendszerindítás során futnak az Azure-beli erőforrások kiépítés után. További információ arról, hogyan működik a Cloud-init natív módon az Azure-ban és a támogatott Linux-disztribúciókban: a [Cloud-init áttekintése](using-cloud-init.md)

## <a name="prerequisites"></a>Előfeltételek
Ez a dokumentum azt feltételezi, hogy már rendelkezik egy futó Azure-beli virtuális géppel, amely a Linux operációs rendszer támogatott verzióját futtatja. Már beállította a gépet az igényeinek megfelelően, telepítette az összes szükséges modult, feldolgozta az összes szükséges frissítést, és tesztelte, hogy megfeleljen az igényeinek. 

## <a name="preparing-rhel-76--centos-76"></a>RHEL 7,6/CentOS 7,6 előkészítése
A Cloud-init telepítéséhez az SSH-t kell használnia a linuxos virtuális géphez, és futtatnia kell a következő parancsokat.

```bash
sudo yum makecache fast
sudo yum install -y gdisk cloud-utils-growpart
sudo yum install - y cloud-init 
```

A `/etc/cloud/cloud.cfg` `cloud_init_modules` szakaszának frissítésével vegye fel a következő modulokat:
```bash
- disk_setup
- mounts
```

Íme egy példa az általános célú `cloud_init_modules` szakaszra.
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
`/etc/waagent.conf`-ben frissíteni kell az ideiglenes lemezek kiépítési és kezelési feladatait. A megfelelő beállítások frissítéséhez futtassa a következő parancsokat. 
```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
cloud-init clean
```

Csak az Azure-t engedélyezi az Azure Linux-ügynök adatforrásaként egy új fájl létrehozásával `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` a választott szerkesztővel a következő sorral:

```bash
# Azure Data Source config
datasource_list: [ Azure ]
```

Ha a meglévő Azure-lemezképhez konfigurálva van egy lapozófájl, és módosítani szeretné az új lemezképek lapozófájl-konfigurációját a Cloud-init használatával, el kell távolítania a meglévő lapozófájlt.

A Red Hat-alapú rendszerképeknél kövesse a következő Red Hat-dokumentum utasításait, amely leírja, hogyan [távolíthatja el a swap-fájlt](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/swap-removing-file).

Ha a CentOS-lemezképek esetében engedélyezve van a swapfile, a következő parancs futtatásával kikapcsolhatja a swapfile:
```bash
sudo swapoff /mnt/resource/swapfile
```

Győződjön meg arról, hogy a swapfile-hivatkozás el van távolítva a `/etc/fstab`ból – a következő kimenethez hasonlóan kell kinéznie:
```text
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

A tárterület mentéséhez és a lapozófájl eltávolításához futtassa a következő parancsot:
```bash
rm /mnt/resource/swapfile
```
## <a name="extra-step-for-cloud-init-prepared-image"></a>További lépés a Cloud-init előkészített rendszerképhez
> [!NOTE]
> Ha a rendszerkép korábban a **Cloud-init** előkészített és konfigurált rendszerkép volt, a következő lépéseket kell elvégeznie.

A következő három parancs csak akkor használatos, ha a testre szabható virtuális gépet korábban a Cloud-init üzembe helyezte.  Ezeket nem kell futtatnia, ha a rendszerkép az Azure Linux-ügynök használatával lett konfigurálva.

```bash
sudo cloud-init clean --logs
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Linux-ügynök beállításának véglegesítése 
Az Azure-platform összes rendszerképének telepítve van az Azure Linux-ügynöke, függetlenül attól, hogy a felhő-init vagy sem lett konfigurálva.  A következő parancs futtatásával fejezze be a felhasználó kiépítését a Linux rendszerű gépről. 

```bash
sudo waagent -deprovision+user -force
```

Az Azure Linux-ügynök megszüntetési parancsaival kapcsolatos további információkért tekintse meg az [Azure Linux-ügynököt](../extensions/agent-linux.md) . további részleteket a következő témakörben talál:.

Lépjen ki az SSH-munkamenetből, majd a bash rendszerhéjból futtassa az alábbi AzureCLI-parancsokat az új Azure-beli virtuálisgép-rendszerkép felszabadításához, általánosításához és létrehozásához.  Cserélje le a `myResourceGroup` és a `sourceVmName`t a sourceVM tükröző megfelelő információkkal.

```bash
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>Következő lépések
További felhő-inicializálási példákat a konfiguráció változásairól a következő témakörben talál:
 
- [További linuxos felhasználó hozzáadása egy virtuális géphez](cloudinit-add-user.md)
- [Csomagkezelő futtatása a meglévő csomagok frissítéséhez az első rendszerindításkor](cloudinit-update-vm.md)
- [Virtuális gép helyi gazdagépének módosítása](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítése, konfigurációs fájlok frissítése és kulcsok behelyezése](tutorial-automate-vm-deployment.md)
