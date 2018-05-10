---
title: Az Azure biztonsági mentési pillanatkép Linux virtuális gép bővítmény |} Microsoft Docs
description: Azure biztonsági mentési pillanatkép Virtuálisgép-bővítmény használatával igénybe alkalmazás konzisztens biztonsági mentését a virtuális gép
services: backup, virtual-machines-linux
documentationcenter: ''
author: trinadhk
manager: jeconnoc
editor: ''
ms.assetid: 57759670-0baa-44db-ae14-8cdc00d3a906
ms.service: backup, virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 3/26/2018
ms.author: trinadhk
ms.openlocfilehash: bed5716b6d4ea6d81214a95d0f2360f359048893
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="vm-snapshot-linux-extension-for-azure-backup"></a>Azure biztonsági mentési pillanatkép Linux virtuális gép kiterjesztése

## <a name="overview"></a>Áttekintés

Azure biztonsági mentés támogatást nyújt a felhő és a felhőalapú erőforrások biztonsági mentéséről Recovery Services-tárolónak a helyszíni biztonsági mentési munkaterhelések. Azure biztonsági mentés Virtuálisgép-pillanatképét bővítmény leállítási az alkalmazás konzisztens biztonsági mentését az Azure virtuális gép nem kell tennie a virtuális gép használja. Virtuális gép pillanatkép Linux bővítményt közzé, és támogatja a Microsoft Azure Backup szolgáltatás részeként. Azure biztonsági mentés első ütemezett biztonsági mentési kiváltott post engedélyezése a biztonsági mentés részeként telepíti a bővítményt. Ez a dokumentum a támogatott platformok, konfigurációk és a virtuális gép pillanatkép bővítmény telepítési beállításait részletezi.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer
A támogatott operációs rendszerek listája, tekintse meg [Azure Backup által támogatott operációs rendszerek](../../backup/backup-azure-arm-vms-prepare.md#supported-operating-systems-for-backup)

### <a name="internet-connectivity"></a>Internetkapcsolat

Virtuális gép pillanatkép-bővítményhez olyan, a cél virtuális gép csatlakozik az internethez, ha azt a virtuális gép biztonsági mentés készítése.

## <a name="extension-schema"></a>Bővítményséma

A következő JSON jeleníti meg a Virtuálisgép-pillanatképét bővítmény sémáját. A bővítmény igényel a Feladatazonosítót – ez azonosítja, hogy a biztonsági mentési feladat kiváltó pillanatkép készítése a virtuális Gépre, állapot blob uri - Ha a pillanatkép művelet állapotának írása, ütemezett kezdési időpontja a pillanatkép, a naplók blob uri - ahol megfelelő naplók pillanatkép-feladat a rendszer ír, objstr-ábrázolását virtuális gépek lemezei és a metaadatokban.  Ezek a beállítások bizalmas adatokat kell kezelni, mert azt egy védett beállítás konfigurációban kell tárolni. Az Azure Virtuálisgép-bővítmény védett beállítás adatokat titkosít, és csak visszafejti a cél virtuális gépen. Vegye figyelembe, hogy ezek a beállítások csak a biztonsági mentési feladat részeként átadását az Azure Backup szolgáltatás használata ajánlott.

```json
{
  "type": "extensions",
  "name": "VMSnapshot",
  "location":"<myLocation>",
  "properties": {
    "publisher": "Microsoft.RecoveryServices",
    "type": "VMSnapshot",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "locale":"<location>",
      "taskId":"<taskId used by Azure Backup service to communicate with extension>",
      "commandToExecute": "snapshot",
      "commandStartTimeUTCTicks": "<scheduled start time of the snapshot task>",
      "vmType": "microsoft.compute/virtualmachines"
    },
    "protectedSettings": {
      "objectStr": "<blob SAS uri represenattion of VM sent by Azure Backup service to extension>",
      "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
      "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
    }
  }
}
```

### <a name="property-values"></a>A tulajdonság értékek

| Name (Név) | Érték / – példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | dátum |
| taskId | e07354cf-041e-4370-929f-25a319ce8933_1 | karakterlánc |
| commandStartTimeUTCTicks | 6.36458E + 17 | karakterlánc |
| Területi beállítás | en-us | karakterlánc |
| objectStr | A sas uri tömb-"blobSASUri" kódolását: ["https:\/\/sopattna5365.blob.core.windows.net\/VHD-k\/vmubuntu1404ltsc201652903941.vhd? sv 2014-02-14 = & sr = b & sig = TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA % 3D & st = 2017-11-09T14 % 3A23 % 3A28Z & se 2017-11-09T17 % 3A38 % 3A28Z = & sp = rw "," https:\/\/sopattna8461.blob.core.windows.net\/VHD-k\/vmubuntu1404ltsc-20160629-122418.vhd? sv 2014-02-14 = & sr = b & sig 5S0A6YDWvVwqPAkzWXVy % 2BS % 2FqMwzFMbamT5upwx05v8Q % = 3D & st = 2017-11-09T14 % 3A23 % 3A28Z & se 2017-11-09T17 % 3A38 % 3A28Z = & sp = rw "," https:\/ \/ sopattna8461.BLOB.Core.Windows.NET\/bootdiagnostics-vmubuntu1-deb58392-ed5e-48be-9228-ff681b0cd3ee\/vmubuntu1404ltsc-20160629-122541.vhd? sv 2014-02-14 = & sr = b & sig = X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4 % 3D & st = 2017-11-09T14 % 3A23 % 3A28Z & se 2017-11-09T17 % 3A38 % 3A28Z = & sp = rw "," https:\/\/sopattna5365.blob.core.windows.net\/VHD-k\/vmubuntu1404ltsc-20160701-163922.vhd? sv 2014-02-14 = & sr = b & sig oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r % 2BC % 2BNIAork % = 3D & st = 2017-11-09T14 % 3A23 % 3A28Z & se 2017-11-09T17 % 3A38 % 3A28Z = & sp = rw "," https:\/ \/ sopattna5365.BLOB.Core.Windows.NET\/VHD-k\/vmubuntu1404ltsc-20170705-124311.vhd? sv 2014-02-14 = & sr = b & sig ZUM9d28Mvvm % 2FfrhJ71TFZh0Ni90m38bBs3zMl % 2FQ9rs0 % = 3D & st = 2017-11-09T14 % 3A23 % 3A28Z & se 2017-11-09T17 % 3A38 % 3A28Z = & sp = rw "] | karakterlánc |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | karakterlánc |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | karakterlánc |



## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonok is telepíthető. Ajánlott módja a virtuális gép pillanatkép bővítmény felvétele a virtuális gép azonban a virtuális gépen található biztonsági mentés engedélyezése. Ez a Resource Manager-sablon keresztül elérhető.  Egy minta Resource Manager-sablon, amely lehetővé teszi a biztonsági másolat a virtuális gépen található meg a [Azure Quick Start gyűjtemény](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/).


## <a name="azure-cli-deployment"></a>Az Azure CLI-telepítés

Az Azure CLI segítségével a virtuális gépek biztonsági mentésének engedélyezése. POST biztonságimásolat-készítés engedélyezése, első ütemezett biztonsági mentési feladat telepíti a virtuális gép pillanatkép-bővítményt a virtuális Gépet.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Bővítmény központi telepítések állapotára vonatkozó lehet adatokat beolvasni az Azure-portálon, és az Azure parancssori felület használatával. A megadott virtuális gépek bővítmények központi telepítési állapotának megtekintéséhez a következő parancsot az Azure parancssori felület használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A következő fájl kerül a bővítmény végrehajtás kimenetének:

```
/var/log/waagent.log
```

### <a name="error-codes-and-their-meanings"></a>Hibakódok és azok jelentését

Hibaelhárítási információkat itt talál a [Azure virtuális gép biztonsági mentése hibaelhárítási útmutató](../../backup/backup-azure-vms-troubleshoot.md).

### <a name="support"></a>Támogatás

Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a a [MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként is fájl az Azure támogatási incidens. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza ki a Get-támogatási szolgálathoz. Támogatja az Azure használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).
