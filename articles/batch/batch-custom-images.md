---
title: Hozzon létre egy készletet a megosztott rendszerkép-katalógus használatával – Azure Batch | Microsoft Docs
description: Hozzon létre egy batch-készletet a megosztott rendszerkép-katalógusban, hogy egyéni rendszerképeket építsen ki az alkalmazásához szükséges szoftvereket és adatokból álló számítási csomópontok számára. Az egyéni lemezképek hatékony módszer a számítási csomópontok konfigurálására a Batch-munkaterhelések futtatásához.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 08/14/2019
ms.author: lahugh
ms.openlocfilehash: 00da17512cbc2e713955ea83c7d9fa7517958169
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036696"
---
# <a name="use-the-shared-image-gallery-to-create-a-pool"></a>Készlet létrehozása a megosztott képtára használatával

Ha Azure Batch-készletet hoz létre a virtuális gép konfigurációjával, meg kell adnia egy virtuálisgép-rendszerképet, amely a készlet minden számítási csomópontja számára biztosítja az operációs rendszert. Létrehozhatja a virtuális gépek készletét vagy egy támogatott Azure Marketplace-lemezképpel, vagy létrehozhat egy egyéni rendszerképet a [megosztott](../virtual-machines/windows/shared-image-galleries.md)képkatalógus használatával.

## <a name="benefits-of-the-shared-image-gallery"></a>A megosztott képgyűjtemény előnyei

Ha az egyéni rendszerképhez a megosztott képtárat használja, az operációs rendszer típusát és konfigurációját, valamint az adatlemezek típusát is szabályozhatja. A megosztott rendszerkép tartalmazhat olyan alkalmazásokat és referenciákat, amelyek a Batch-készlet összes csomópontján elérhetővé válnak a kiépítés után.

A környezethez szükség szerint a rendszerkép több verzióját is használhatja. Ha rendszerkép-verziót használ a virtuális gép létrehozásához, a rendszer a rendszerkép verzióját használja a virtuális gép új lemezének létrehozásához. 

A megosztott rendszerkép használatával időt takaríthat meg a készlet számítási csomópontjainak előkészítése a Batch-munkaterhelések futtatásához. A kiépítés után egy Azure Marketplace-rendszerkép is használható, és minden számítási csomóponton telepítheti a szoftvereket, de a megosztott rendszerképek használata általában hatékonyabb. Emellett több replikát is megadhat a megosztott rendszerképhez, így ha sok virtuális géppel (több mint 600 virtuális géppel) rendelkező készleteket hoz létre, akkor időt takaríthat meg a készlet létrehozásakor.

A forgatókönyvhöz konfigurált megosztott rendszerkép használata több előnyt is biztosíthat:

* **Ugyanazokat a lemezképeket használja a régiók között.** Létrehozhat megosztott rendszerkép-replikákat különböző régiók között, így az összes készlet ugyanazt a rendszerképet használja.
* **Konfigurálja az operációs rendszert (operációs rendszer).** Testreszabhatja a lemezkép operációsrendszer-lemezének konfigurációját.
* **Alkalmazások telepítése előtt.** Az operációsrendszer-lemezen lévő alkalmazások előzetes telepítése hatékonyabb és kevésbé hasonló, mint az alkalmazások telepítése a számítási csomópontok indítási tevékenységgel való kiépítés után.
* **Nagyméretű adatmennyiségek másolása egyszer.** A felügyelt megosztott rendszerkép statikus adatokból álló részét a felügyelt rendszerkép adatlemezére másolja. Ezt csak egyszer kell elvégezni, és a készlet minden csomópontja számára elérhetővé kell tenni az adatmennyiséget.
* **Növelje a készleteket nagyobb méretekre.** A megosztott képkatalógussal nagyobb készleteket hozhat létre a testreszabott rendszerképekkel együtt, és több megosztott lemezkép-replikát is használhat.
* **Jobb teljesítmény, mint az egyéni rendszerkép.** A megosztott rendszerképek használatával a készlethez szükséges idő akár 25%-kal gyorsabb, a virtuális gép üresjárati késése pedig akár 30%-kal rövidebb is lehet.
* **A képek verziószámozása és csoportosítása az egyszerűbb felügyelet érdekében.** A képcsoportosítási definíció információt tartalmaz arról, hogy miért jött létre a rendszerkép, milyen operációs rendszerre és a rendszerképek használatára vonatkozó információkkal rendelkezik. A képek csoportosítása megkönnyíti a képek kezelését. További információ: képdefiníciók. [](../virtual-machines/windows/shared-image-galleries.md#image-definitions)

## <a name="prerequisites"></a>Előfeltételek

* **Egy Azure Batch-fiók.** Batch-fiók létrehozásához tekintse meg a Batch rövid útmutatóit a [Azure Portal](quick-create-portal.md) vagy az [Azure CLI](quick-create-cli.md)használatával.

* **Megosztott Képgyűjteményi rendszerkép** További információ és a megosztott rendszerkép előkészítésének lépései: [megosztott rendszerkép-katalógus létrehozása az Azure CLI-vel](../virtual-machines/linux/shared-images.md) vagy [közös rendszerkép-katalógus létrehozása a Azure Portal használatával](../virtual-machines/linux/shared-images-portal.md).

> [!NOTE]
> A megosztott rendszerképnek a Batch-fiókkal megegyező előfizetésben kell lennie. A megosztott rendszerkép különböző régiókban lehet, ha a Batch-fiókkal megegyező régióban vannak replikák.

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Készlet létrehozása megosztott rendszerképből az Azure CLI használatával

Ha az Azure CLI használatával szeretne létrehozni egy készletet a megosztott rendszerképből, `az batch pool create` használja az parancsot. Határozza meg a megosztott rendszerkép azonosítóját `--image` a mezőben. Győződjön meg arról, hogy az operációs rendszer típusa és az SKU megfelel a következő által megadott verzióknak:`--node-agent-sku-id`

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>Készlet létrehozása megosztott rendszerképből a következő használatával:C#

Azt is megteheti, hogy létrehoz egy készletet egy megosztott C# rendszerképből az SDK használatával.

```csharp
private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
    ...
}
```

## <a name="considerations-for-large-pools"></a>A nagyméretű készletek szempontjai

Ha egy megosztott rendszerkép használatával több száz vagy több ezer virtuális géppel rendelkező készletet szeretne létrehozni, kövesse az alábbi útmutatást.

* **A megosztott képgyűjtemény replikáinak száma.**  A legfeljebb 600 példánnyal rendelkező készletek esetében javasoljuk, hogy tartsa meg legalább egy replikát. Ha például egy 3000 virtuális géppel rendelkező készletet hoz létre, akkor legalább 5 replikát kell tartania a rendszerképből. Javasoljuk, hogy a jobb teljesítmény érdekében mindig több replikát őrizzen meg, mint a minimális követelmények.

* **Átméretezés időtúllépése** Ha a készlet rögzített számú csomópontot tartalmaz (ha nem rendelkezik az autoskálázással), `resizeTimeout` növelje a készlet tulajdonságát a készlet méretétől függően. Minden 1000 virtuális gép esetében az ajánlott átméretezési időkorlát legalább 15 percet vesz igénybe. Például egy 2000 virtuális géppel rendelkező készlet ajánlott átméretezési időtúllépése legalább 30 percet vesz igénybe.

## <a name="next-steps"></a>További lépések

* A Batch részletes áttekintése: [nagy léptékű párhuzamos számítási megoldások létrehozása a Batch szolgáltatással](batch-api-basics.md).
