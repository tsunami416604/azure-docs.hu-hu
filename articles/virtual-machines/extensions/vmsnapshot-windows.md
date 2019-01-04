---
title: VM-pillanatkép Windows kiterjesztése az Azure Backuphoz |} A Microsoft Docs
description: Alkalmazáskonzisztens biztonsági mentése a virtuális géphez igénybe az Azure Backup használatával VM snapshot bővítményt
services: backup, virtual-machines-windows
documentationcenter: ''
author: trinadhk
manager: jeconnoc
ms.service: backup, virtual-machines-windows
ms.topic: article
ms.date: 12/17/2018
ms.author: trinadhk
ms.openlocfilehash: 4d942e21afbf1d87ab67d79e90119a4122d889da
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632195"
---
# <a name="vm-snapshot-windows-extension-for-azure-backup"></a>VM-pillanatkép Windows Azure Backup-bővítmény

Az Azure Backup támogatja munkaterhelések biztonsági mentéséhez a helyszíni, felhőbeli és a Recovery Services-tároló biztonsági másolatot a felhőbeli erőforrások. Az Azure Backup olyan alkalmazáskonzisztens biztonsági mentése az Azure virtuális gépen nincs szükség érvénybe leállítja a virtuális gép VM snapshot bővítményt használ. VM Snapshot bővítményt közzétett és a Microsoft Azure Backup szolgáltatás részeként támogatja. Az Azure Backup az első ütemezett biztonsági mentési aktivált bejegyzés a biztonsági mentés engedélyezése részeként fogja telepíteni a bővítményt. Ez a dokumentum részletesen, a támogatott platformok, konfigurációk és a VM-pillanatkép bővítményt üzembe helyezési lehetőségeit.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer
A támogatott operációs rendszerek listájáért tekintse meg [az Azure Backup által támogatott operációs rendszerek](../../backup/backup-azure-arm-vms-prepare.md#before-you-start)

### <a name="internet-connectivity"></a>Internetkapcsolat

A virtuális gép pillanatképét bővítmény szükséges, hogy a céloldali virtuális gép csatlakoztatva az internethez, amikor egy virtuális gép biztonsági mentése is.

## <a name="extension-schema"></a>Bővítményséma

A következő JSON VM snapshot bővítményt sémáját jeleníti meg. A bővítmény telepítéséhez a feladat Azonosítóját – ez azonosítja a biztonsági mentési feladat aktiválódik, amely a virtuális gépen, a pillanatkép állapotát blob uri -, a pillanatkép-készítési művelet állapotának írása, ütemezett kezdési idő a pillanatkép, a naplók blob uri - ahol megfelelő naplók pillanatkép-feladat írt, Virtuálisgép-lemezek és a metaadatok objstr-ábrázolását.  Ezek a beállítások kényes adatként kell kezelni, mert azt egy védett beállítás konfigurációjának kell tárolni. Az Azure VM-bővítmény védett beállítás adatok titkosítva, és csak az átjárót tartalmazó a cél virtuális gépen. Vegye figyelembe, hogy ezek a beállítások az Azure Backup szolgáltatás biztonsági mentési feladat részeként átadandó használata ajánlott.

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

### <a name="property-values"></a>Tulajdonságok értékei

| Name (Név) | Érték és példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | dátum |
| Feladatazonosító: | e07354cf-041e-4370-929f-25a319ce8933_1 | sztring |
| commandStartTimeUTCTicks | 6.36458E + 17 | sztring |
| területi beállítás | en-us | sztring |
| objectStr | Az sas URI-t tömb – "blobSASUri" kódolásának: ["https:\/\/sopattna5365.blob.core.windows.net\/VHD-k\/vmwin1404ltsc201652903941.vhd? sv 2014. 02. 14 = & sr = b & sig = TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA % 3D & st = 2017-11-09T14 % 3A23 % 3A28Z & se = 2017-11-09T17 % 3A38 % 3A28Z & sp = rw "," https:\/\/sopattna8461.blob.core.windows.net\/VHD-k\/vmwin1404ltsc-20160629-122418.vhd? sv 2014. 02. 14 = & sr = b & sig 5S0A6YDWvVwqPAkzWXVy % 2BS % 2FqMwzFMbamT5upwx05v8Q % = 3D & st = 2017-11-09T14 % 3A23 % 3A28Z & se = 2017-11-09T17 % 3A38 % 3A28Z & sp = rw "," https:\/ \/ sopattna8461.BLOB.Core.Windows.NET\/bootdiagnostics-vmwintu1-deb58392-ed5e-48be-9228-ff681b0cd3ee\/vmubuntu1404ltsc-20160629-122541.vhd? sv 2014. 02. 14 = & sr = b & sig = X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4 % 3D & st = 2017-11-09T14 % 3A23 % 3A28Z & se = 2017-11-09T17 % 3A38 % 3A28Z & sp = rw "," https:\/\/sopattna5365.blob.core.windows.net\/VHD-k\/vmwin1404ltsc-20160701-163922.vhd? sv 2014. 02. 14 = & sr = b & sig oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r % 2BC % 2BNIAork % = 3D & st = 2017-11-09T14 % 3A23 % 3A28Z & se = 2017-11-09T17 % 3A38 % 3A28Z & sp = rw "," https:\/ \/ sopattna5365.BLOB.Core.Windows.NET\/VHD-k\/vmwin1404ltsc-20170705-124311.vhd? sv 2014. 02. 14 = & sr = b & sig ZUM9d28Mvvm % 2FfrhJ71TFZh0Ni90m38bBs3zMl % 2FQ9rs0 % = 3D & st = 2017-11-09T14 % 3A23 % 3A28Z & se = 2017-11-09T17 % 3A38 % 3A28Z & sp = rw "] | sztring |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | sztring |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | sztring |



## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. Az ajánlott módszer a VM-pillanatkép bővítmény hozzáadása egy virtuális géphez, azonban nem engedélyezi a biztonsági mentés a virtuális gépen. Ez megvalósítható a Resource Manager-sablon használatával.  Egy mintául szolgáló Resource Manager-sablon, amely a virtuális gép biztonsági mentését találhatók a [Azure gyors üzembe helyezési katalógus](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/).


## <a name="azure-cli-deployment"></a>Az Azure CLI-telepítés

Az Azure CLI segítségével a virtuális gépek biztonsági mentésének engedélyezése. Biztonsági mentés a POST engedélyezése, első ütemezett biztonsági mentési feladat telepíti a Vm snapshot bővítményt a virtuális Gépet.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Bővítmény központi telepítések állapotát lehet adatokat beolvasni az Azure Portalról, és az Azure parancssori felület használatával. Adott Virtuálisgép-bővítmények központi telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure CLI használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Bővítmény végrehajtás kimenetének a rendszer naplózza a következő fájlt:

```
C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot
```

### <a name="error-codes-and-their-meanings"></a>Hibakódok és azok jelentését

Hibaelhárítási információk találhatók a [hibaelhárítási útmutató Azure VM backup](../../backup/backup-azure-vms-troubleshoot.md).

### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).
