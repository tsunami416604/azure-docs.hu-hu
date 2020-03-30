---
title: Vm Snapshot Linux-bővítmény az Azure Backup hoz
description: A virtuális gép alkalmazáskonzisztens biztonsági másolatának készítése az Azure Backup ból a VM pillanatkép-bővítményhasználatával
services: backup, virtual-machines-linux
documentationcenter: ''
author: trinadhkotturu
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 12/17/2018
ms.author: trinadhk
ms.openlocfilehash: d0ad54c19749d670f9ab753e1e6d8eb130475ffc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415107"
---
# <a name="vm-snapshot-linux-extension-for-azure-backup"></a>Vm Snapshot Linux-bővítmény az Azure Backup hoz



Az Azure Backup támogatást nyújt a helyszíni és a felhőbeli számítási feladatok biztonsági mentéséhez, valamint a felhőbeli erőforrások biztonsági mentéséhez a Recovery Services-tárolóba. Az Azure Backup virtuális gép pillanatkép-bővítményt használ az Azure virtuális gép konzisztens biztonsági mentéséhez anélkül, hogy le kellene állítania a virtuális gépet. VM Snapshot Linux-bővítmény tették közzé, és támogatja a Microsoft az Azure Backup szolgáltatás részeként. Az Azure Backup telepíti a bővítményt az első ütemezett biztonsági mentés imitálása a biztonsági mentés engedélyezésével. Ez a dokumentum részletezi a támogatott platformok, konfigurációk és a virtuális gép pillanatkép bővítménytelepítési lehetőségek.

A VMSnapshot bővítmény csak a nem felügyelt virtuális gépek en jelenik meg az Azure Portalon.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer
A támogatott operációs rendszerek listáját az Azure Backup által támogatott operációs rendszerek című részében tájékszámára [tartalmazza.](../../backup/backup-azure-arm-vms-prepare.md#before-you-start)

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a virtuális gép pillanatkép-bővítményének sémáját jeleníti meg. A bővítmény megköveteli a feladat azonosítóját - ez azonosítja a biztonsági mentési feladatot, amely elindította a pillanatképet a virtuális gép, status blob uri - ahol állapotát a pillanatkép művelet van írva, ütemezett kezdési időpontja a pillanatkép, naplók blob uri - ahol naplók megfelelő pillanatfelvétel feladat vannak írva, objstr- ábrázolása VM lemezek és meta adatok.  Mivel ezeket a beállításokat bizalmas adatként kell kezelni, védett beállítási konfigurációban kell tárolni. Az Azure VM-bővítmény védett beállítási adatai titkosítva vannak, és csak a cél virtuális gépen fejtik vissza. Kérjük, vegye figyelembe, hogy ezeket a beállításokat ajánlott átadni az Azure Backup szolgáltatás csak a biztonsági mentési feladat részeként.

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
      "objectStr": "<blob SAS uri representation of VM sent by Azure Backup service to extension>",
      "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
      "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
    }
  }
}
```

### <a name="property-values"></a>Tulajdonság értékek

| Név | Érték / Példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | dátum |
| taskId | e07354cf-041e-4370-929f-25a319ce8933_1 | sztring |
| commandStartTimeUTCTicks | 6.36458E+17 | sztring |
| locale | hu-hu | sztring |
| objectStr | Sas uri tömb kódolása- "blobSASUri":\/\/["https: sopattna5365.blob.core.windows.net\/vhds\/vmubuntu1404ltsc201652903941.vhd?sv=2014-02-14&sr=b&sig=TywkROXL1zvhXcLujtCut 8g3jTpgbE6JpSWRLZxAdtA%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw",\/\/"https: sopattna8461.blob.core.windows.net\/vhds\/vmubuntu1404ltsc-20160629-122418.vhd?sv=2014-02-14&sr=b&sig= 5S0A6YDWvVwqPAkzWXVy%2BS%2FqMwmwzFMbamT5upw05v8Q%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28&Z sp=rw", "https:\/\/sopattna8461.blob.core.windows.net\/bootdiagnostics-vmubuntu1-deb58392-ed5e-48be-9228-ff681b0cd3ee\/vmubuntu1404ltsc-20160629-122541.vhd?sv=2014-02-14&sr=b&sig=X0Me2djByksBBMVXMGIUrcycvhQSfjYv qKLeRA7nBD4%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", " https:\/\/\/\/sopattna5365.blob.core.windows.net vhds vmubuntu1404ltsc-20160701-163922.vhd?sv=2014-02-14&sr=b&sig=oXvtK2IXCNqWv7fpjc7TAzFDpc1Go XtT7r%2BC%2BNIAork%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna5365.blob.core.windows.net\/vhds\/vmubuntu1404ltsc-20170705-124311.vhd?sv=2014-02-14&sr=b&sig=ZUM9d28Mvvm% 2FfrhJ71TFZh0Ni90m38bBs3zMl%2FQ9rs0%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw"] | sztring |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | sztring |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | sztring |



## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonokkal telepíthetők. Azonban a virtuális gép pillanatkép-bővítmény virtuális géphez való hozzáadásának ajánlott módja a biztonsági mentés engedélyezése a virtuális gépen. Ez egy Erőforrás-kezelő sablonnal érhető el.  Az [Azure Gyorskatalógusban](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/)található egy erőforrás-kezelősablon, amely lehetővé teszi a biztonsági mentést a virtuális gépen.


## <a name="azure-cli-deployment"></a>Az Azure CLI üzembe helyezése

Az Azure CLI segítségével engedélyezheti a biztonsági mentést egy virtuális gépen. Tegye lehetővé a biztonsági mentést, az első ütemezett biztonsági mentési feladat telepíti a virtuális gép pillanatkép-bővítményét a virtuális gépre.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A bővítmény-üzembe helyezések állapotára vonatkozó adatok az Azure Portalról és az Azure CLI használatával is lekérdezhetők. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure CLI használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A bővítmény-végrehajtási kimenet a következő fájlba kerül:

```
/var/log/waagent.log
```

### <a name="error-codes-and-their-meanings"></a>Hibakódok és jelentésük

A hibaelhárítási információk az [Azure VM biztonsági mentési hibaelhárítási útmutatójában](../../backup/backup-azure-vms-troubleshoot.md)találhatók.

### <a name="support"></a>Támogatás

Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel az [MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/) Másik lehetőségként benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza a Támogatás beszerezni lehetőséget. Az Azure-támogatás használatáról a [Microsoft Azure támogatási gyIK](https://azure.microsoft.com/support/faq/)című területén olvashat.
