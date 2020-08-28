---
title: Lemezes titkosítást használó készlet létrehozása
description: Megtudhatja, hogyan titkosíthatja a csomópontokat egy platform által felügyelt kulccsal a lemez titkosítási konfigurációjának használatával.
author: pkshultz
ms.topic: how-to
ms.date: 08/25/2020
ms.author: peshultz
ms.custom: references_regions
ms.openlocfilehash: 5210ead0a85cff27c38d9ff9fb0d387e3799428c
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89038537"
---
# <a name="create-a-pool-with-disk-encryption-enabled"></a>Lemezes titkosítást használó készlet létrehozása

Ha Azure Batch-készletet hoz létre a virtuális gép konfigurációja segítségével, a készletben lévő számítási csomópontokat a platform által felügyelt kulccsal titkosíthatja a lemez titkosítási konfigurációjának megadásával. 

Ez a cikk azt ismerteti, hogyan lehet létrehozni egy batch-készletet, amelyen engedélyezve van a lemezes titkosítás. 

## <a name="why-use-a-pool-with-disk-encryption-configuration"></a>Miért érdemes egy készletet használni a lemezes titkosítási konfigurációval?

A Batch-készlettel a számítási csomópont operációs rendszerén és ideiglenes lemezén tárolt adatok érhetők el és tárolhatók. A kiszolgálóoldali lemeznek a platform által felügyelt kulccsal történő titkosításával az adatokat alacsony terheléssel és kényelemmel fogja védeni.  

A Batch ezeket a lemezes titkosítási technológiákat alkalmazza a számítási csomópontokon a készlet konfigurációja és a regionális támogatás alapján. 

* [Felügyelt lemezes titkosítás a platform által felügyelt kulcsokkal](../virtual-machines/windows/disk-encryption.md#platform-managed-keys) 

* [Titkosítás a gazdagépen a platform által felügyelt kulcs használatával](../virtual-machines/windows/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data) 

* [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) 

> [!IMPORTANT]
> A gazdagépeken a platform által felügyelt kulcs használatával történő titkosítás támogatása Azure Batch jelenleg nyilvános előzetes verzióban érhető el az USA keleti régiója, az USA 2. nyugati régiója, az USA déli középső régiója, az US Gov Virginia és a US Gov Arizona régiók számára.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Nem fogja tudni megadni, hogy melyik titkosítási módszert alkalmazza a rendszer a készlet csomópontjaira. Ehelyett adja meg a csomópontokon titkosítani kívánt cél lemezeket, a Batch pedig kiválaszthatja a megfelelő titkosítási módszert, így biztosítva, hogy a megadott lemezek titkosítva legyenek a számítási csomóponton.
 
## <a name="azure-portal"></a>Azure Portal 

Ha batch-készletet hoz létre a Azure Portalban, válassza a **TemporaryDisk** vagy a **OsAndTemporaryDisk** lehetőséget a **lemez titkosítása beállítás**alatt.

![Képernyőfelvétel a lemez titkosításának konfigurációs beállításáról a Azure Portal.](./media/disk-encryption/portal-view.png)

A készlet létrehozása után a lemez titkosítási konfigurációs céljait a készlet **Tulajdonságok** szakaszában tekintheti meg.

![Képernyőfelvétel: a Azure Portal a lemez titkosítási konfigurációs céljait jeleníti meg.](./media/disk-encryption/disk-encryption-configuration-target.png)

## <a name="examples"></a>Példák

Az alábbi példák bemutatják, hogyan titkosíthatja a Batch-készlet operációs rendszerét és ideiglenes lemezeit a Batch .NET SDK, a Batch REST API és az Azure CLI használatával.

### <a name="batch-net-sdk"></a>Batch .NET SDK 

```csharp
pool.VirtualMachineConfiguration.DiskEncryptionConfiguration = new DiskEncryptionConfiguration(
    targets: new List<DiskEncryptionTarget> { DiskEncryptionTarget.OsDisk, DiskEncryptionTarget.TemporaryDisk }
    );
```

### <a name="batch-rest-api"></a>Batch REST API


REST API URL-CÍM:
```
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```
Kérés törzse:
```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "18.04-LTS"
        },
        "diskEncryptionConfiguration": {
            "targets": [
                "OsDisk",
                "TemporaryDisk"
            ]
        }
        "nodeAgentSKUId": "batch.node.ubuntu 18.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "maxTasksPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az batch pool create \
    --id diskencryptionPool \
    --vm-size Standard_DS1_V2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:18.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 18.04" \
    --disk-encryption-targets OsDisk TemporaryDisk
```
