---
title: Egyéni készlet – Azure Batch | Microsoft dokumentumok
description: Hozzon létre egy batch készletet a megosztott képtárral egyéni lemezképek kiépítési számítási csomópontok, amelyek tartalmazzák a szoftver és az alkalmazáshoz szükséges adatokat. Az egyéni lemezképek hatékony módja a számítási csomópontok konfigurálása a Batch számítási feladatok futtatásához.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 08/28/2019
ms.author: labrenne
ms.openlocfilehash: 95f27d913cd288c186bae1a6375212b072f50bb4
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422435"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-pool"></a>Egyéni képtár létrehozása a Megosztott képtár segítségével

Amikor létrehoz egy Azure Batch-készletet a virtuális gép konfigurációja használatával, megkell adnia egy virtuálisgép-lemezképet, amely a készlet minden számítási csomópontjához biztosítja az operációs rendszert. Virtuális gépek készletét létrehozhatja egy támogatott Azure Marketplace-lemezképpel, vagy létrehozhat egy egyéni lemezképet a [Megosztott képtár](../virtual-machines/windows/shared-image-galleries.md)segítségével.

## <a name="benefits-of-the-shared-image-gallery"></a>A megosztott képtár előnyei

Ha a Megosztott képtár az egyéni lemezkép, akkor szabályozhatja az operációs rendszer típusát és konfigurációját, valamint az adatlemezek típusát. A megosztott lemezkép tartalmazhat alkalmazásokat és referenciaadatokat, amelyek a kötegkészlet összes csomópontján elérhetővé válnak, amint azok kivannak építve.

A lemezkép több verzióját is használhatja a környezetéhez szükség szerint. Ha egy lemezkép-verzió t használ egy virtuális gép létrehozásához, a rendszerkép verziója új lemezek et hoz létre a virtuális gép.

A megosztott lemezkép használatával időt takaríthat meg a készlet számítási csomópontjainak előkészítésében a Batch-munkaterhelés futtatásához. Lehetőség van egy Azure Marketplace-lemezkép használatára, és a kiépítés után minden számítási csomóponton telepíthet szoftvert, de a megosztott lemezkép használata általában hatékonyabb. Emellett több replikák a megosztott rendszerkép, így ha sok virtuális gép (több mint 600 virtuális gép), időt takaríthat meg a készlet létrehozása.

A forgatókönyvhöz konfigurált megosztott lemezkép használata számos előnnyel járhat:

* **Használja ugyanazokat a képeket a régiókban.** A megosztott kép replikák különböző régiókban, így az összes készlet használja ugyanazt a lemezképet.
* **Konfigurálja az operációs rendszert.** Testre szabhatja a lemezkép operációs rendszerlemezének konfigurációját.
* **Alkalmazások előtelepítése.** Az operációs rendszer lemezén az alkalmazások előzetes telepítése hatékonyabb és kevésbé hibaérzékeny, mint az alkalmazások telepítése a számítási csomópontok indítási feladattal történő kiépítése után.
* **Nagy mennyiségű adat másolása egyszer.** A kezelt megosztott lemezkép részévé teheti a statikus adatokat, ha egy felügyelt lemezre másolja. Ezt csak egyszer kell elvégezni, és az adatokat elérhetővé teszi a készlet minden csomópontja számára.
* **A medencéket nagyobb méretekre növelve.** A Megosztott képtár segítségével nagyobb készleteket hozhat létre a testreszabott képekkel és a megosztott kép replikákkal együtt.
* **Jobb teljesítmény, mint az egyéni kép.** A megosztott képek használatával a készlet állandósult állapotának eléréséhez szükséges idő akár 25%-kal gyorsabb, és a virtuális gép tétlenkés ideje akár 30%-kal rövidebb is lehet.
* **Lemezverziózás és csoportosítás a könnyebb kezelés érdekében.** A képcsoportosítási definíció információkat tartalmaz arról, hogy miért jött létre a kép, milyen operációs rendszerre készült, valamint a lemezkép használatáról. A képek csoportosítása megkönnyíti a képkezelést. További információ: [Képdefiníciók](../virtual-machines/windows/shared-image-galleries.md#image-definitions).

## <a name="prerequisites"></a>Előfeltételek

> [!NOTE]
> Hitelesítenie kell az Azure AD használatával. Ha megosztott kulcs-auth-ot használ, hitelesítési hibát fog kapni.  

* **Egy Azure Batch-fiók.** Batch-fiók létrehozásához tekintse meg a Batch-rövid útmutatókat az [Azure Portalon](quick-create-portal.md) vagy az [Azure CLI-n](quick-create-cli.md)keresztül.

* **Megosztott képtár kép**. Megosztott lemezkép létrehozásához felügyelt képerőforrással kell rendelkeznie vagy létrehoznia kell. A lemezképet a virtuális gép operációsrendszer-lemezének és opcionálisan a csatolt adatlemezek pillanatképeinek pillanatképeiből kell létrehozni. További [információ: Felügyelt lemezkép előkészítése](#prepare-a-managed-image).

> [!NOTE]
> A megosztott lemezképnek ugyanabban az előfizetésben kell lennie, mint a Batch-fióknak. A megosztott lemezkép lehet a különböző régiókban, amíg a replikák ugyanabban a régióban, mint a Batch-fiók.

## <a name="prepare-a-managed-image"></a>Felügyelt lemezkép előkészítése

Az Azure-ban a következő innen készíthet el felügyelt lemezképet:

* Az Azure virtuális gép operációs rendszerének és adatlemezeinek pillanatképei
* Általános Azure-alapú virtuális gép felügyelt lemezekkel
* A felhőbe feltöltött általános helyszíni virtuális merevlemez

A batch készletek megbízható méretezéséhez egy egyéni lemezképpel, javasoljuk, hogy *hozzon* létre egy felügyelt rendszerképet csak az első módszerrel: a virtuális gép lemezeinek pillanatképeihasználatával. Tekintse meg a következő lépéseket a virtuális gép előkészítéséhez, pillanatkép készítése, és hozzon létre egy képet a pillanatképből.

### <a name="prepare-a-vm"></a>Virtuális gép előkészítése

Ha új virtuális gép a lemezképhez, használja a batch által támogatott, a batch által támogatott, a felügyelt lemezkép alaplemezképe ként egy első féltől származó Azure Marketplace-lemezkép használata. Alapképként csak a first party képek használhatók. Az Azure Marketplace-en az Azure Batch által támogatott Azure Marketplace-rendszerrendszer-hivatkozások teljes listájának beolvassa: [List node agent SKUs](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) operation.

> [!NOTE]
> Nem használhat olyan külső lemezképet, amely nek további licenc- és vásárlási feltételei vannak alaplemezképként. Ezekről a Marketplace-lemezképekről a [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) vagy a [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) virtuális gépekre vonatkozó útmutatásban talál tájékoztatást.

* Győződjön meg arról, hogy a virtuális gép felügyelt lemezzel jön létre. Ez az alapértelmezett tárolási beállítás, amikor virtuális gép létrehozásakor.
* Ne telepítse az Azure-bővítményeket, például az egyéni parancsfájl-bővítményt a virtuális gépre. Ha a lemezkép egy előre telepített bővítményt tartalmaz, az Azure problémákba ütközhet a batch készlet telepítésekor.
* Csatlakoztatott adatlemezek használata esetén csatlakoztatnia kell és formáznia kell a virtuális gépből származó lemezeket azok használatához.
* Győződjön meg arról, hogy a megadott operációsrendszer-lemezkép az alapértelmezett ideiglenes meghajtót használja. A Batch csomópont ügynök jelenleg az alapértelmezett ideiglenes meghajtót várja.
* Miután a virtuális gép fut, csatlakozzon hozzá RDP (Windows) vagy SSH (Linux) keresztül. Telepítse a szükséges szoftvert, vagy másolja a kívánt adatokat.  

### <a name="create-a-vm-snapshot"></a>Virtuálisgép-pillanatkép létrehozása

A pillanatkép a virtuális merevlemez teljes, csak olvasható példánya. A virtuális gép operációs rendszerének vagy adatlemezeinek pillanatképének létrehozásához használhatja az Azure Portalt vagy a parancssori eszközöket. A pillanatkép létrehozásának lépéseit és lehetőségeit a [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) [vagy](../virtual-machines/windows/snapshot-copy-managed-disk.md) windows os virtuális gépekre vonatkozó útmutatásban talál.

### <a name="create-an-image-from-one-or-more-snapshots"></a>Kép létrehozása egy vagy több pillanatképből

Felügyelt lemezkép pillanatképből való létrehozásához használja az Azure parancssori eszközeit, például az [az image create](/cli/azure/image) parancsot. Hozzon létre egy lemezképet egy operációs rendszer lemezpillanatképének megadásával, és adott esetben egy vagy több adatlemez-pillanatképet.

### <a name="create-a-shared-image-gallery"></a>Megosztott képtár létrehozása

Miután sikeresen létrehozta a felügyelt lemezképet, létre kell hoznia egy megosztott képgalériát, hogy az egyéni kép elérhető legyen. Ha meg szeretné tudni, hogyan hozhat létre megosztott képgalériát a képekhez, olvassa el a [Megosztott képtár létrehozása az Azure CLI-vel](../virtual-machines/linux/shared-images.md) vagy [a Megosztott képtár létrehozása az Azure Portalon című témakört.](../virtual-machines/linux/shared-images-portal.md)

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Készlet létrehozása megosztott lemezképből az Azure CLI használatával

Ha az Azure CLI használatával hozzon létre `az batch pool create` egy készletet a megosztott lemezképből, használja a parancsot. Adja meg a megosztott `--image` képazonosítót a mezőben. Győződjön meg arról, hogy az operációs rendszer típusa és a Termékváltozat megegyezik a`--node-agent-sku-id`

> [!NOTE]
> Hitelesítenie kell az Azure AD használatával. Ha megosztott kulcs-auth-ot használ, hitelesítési hibát fog kapni.  

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>Készlet létrehozása megosztott képből c használatával #

Másik lehetőségként létrehozhat egy készletet egy megosztott lemezképből a C# SDK használatával.

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

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>Készlet létrehozása megosztott lemezképből az Azure Portalon

Az alábbi lépésekkel hozzon létre egy készletet egy megosztott lemezképből az Azure Portalon.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
1. Nyissa meg a **Batch-fiókok at,** és válassza ki a fiókját.
1. Új készlet létrehozásához válassza **a Készletek,** majd a **Hozzáadás** lehetőséget.
1. A **Képtípusa** csoportban válassza a **Megosztott képtár lehetőséget.**
1. Töltse ki a fennmaradó szakaszokat a felügyelt lemezképadataival.
1. Válassza **az OK gombot.**

![Készlet létrehozása a portállal megosztott képből.](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>A nagy medencékkel kapcsolatos szempontok

Ha azt tervezi, hogy hozzon létre egy készlet több száz vagy több ezer virtuális gép vagy több megosztott lemezkép használatával, kövesse az alábbi útmutatást.

* **Megosztott képtár replika számok.**  Minden legfeljebb 600 példányt lehelő készletesetében azt javasoljuk, hogy legalább egy replikát tartson meg. Ha például 3000 virtuális géppel rendelkező készletet hoz létre, legalább 5 replikát kell megtartania a lemezképről. Mindig javasoljuk, hogy a jobb teljesítmény érdekében a minimális követelményeknél több replikát tartson.

* **Időtúltöltés átméretezése.** Ha a készlet rögzített számú csomópontot tartalmaz (ha nem automatikus `resizeTimeout` skálázás), növelje a készlet tulajdonságát a készlet méretétől függően. Minden 1000 virtuális gép esetében az ajánlott átméretezési időtúlméret legalább 15 perc. Például az ajánlott átméretezési időtúltöltés egy 2000 virtuális géptel rendelkező készlet legalább 30 perc.

## <a name="next-steps"></a>További lépések

* A Batch részletes áttekintését a [Nagyméretű párhuzamos számítási megoldások fejlesztése a Batch segítségével](batch-api-basics.md)című témakörben találja.
