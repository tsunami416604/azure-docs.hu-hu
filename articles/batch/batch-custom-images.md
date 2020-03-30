---
title: Egyéni készlet kiépítése felügyelt lemezképből – Azure Batch | Microsoft dokumentumok
description: Hozzon létre egy batch készletet egy felügyelt rendszerkép-erőforrásból a számítási csomópontok kiépítési az alkalmazás szoftverével és adataival.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 09/16/2019
ms.author: labrenne
ms.openlocfilehash: 1ef6be2ba9364203dceba54ab51325c05dbbbe41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77020148"
---
# <a name="use-a-managed-image-to-create-a-pool-of-virtual-machines"></a>Felügyelt lemezkép használata virtuális gépek készletének létrehozásához

Egyéni lemezkép létrehozásához a batch készlet virtuális gépeihez (VM-ek) használhatja a [Megosztott képtár](batch-sig-images.md)vagy egy *felügyelt lemezkép-erőforrás* t.

> [!TIP]
> A legtöbb esetben egyéni képeket kell létrehoznia a Megosztott képtár használatával. A megosztott képtár használatával gyorsabban kiépítheti a készleteket, nagyobb mennyiségű virtuális gépet méretezhet, és a virtuális gépek kiépítésekor nagyobb megbízhatóságot érhet el. További információ: [A Megosztott képtár használata egyéni készlet létrehozásához.](batch-sig-images.md)

## <a name="prerequisites"></a>Előfeltételek

- **Felügyelt lemezkép-erőforrás**. Virtuális gépek készletének egyéni lemezkép használatával történő létrehozásához rendelkeznie kell, vagy hozzon létre egy felügyelt lemezkép-erőforrást ugyanabban az Azure-előfizetésben és régióban, mint a Batch-fiók. A lemezképet a virtuális gép operációsrendszer-lemezének és opcionálisan a csatolt adatlemezek pillanatképeinek pillanatképeiből kell létrehozni. További információt és a felügyelt lemezkép előkészítésének lépéseit a következő szakaszban talál.
  - Minden létrehozott készlethez használjon egyedi egyéni lemezképet.
  - Ha a Batch API-k használatával szeretne készletet létrehozni a lemezképpel, adja `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`meg a lemezkép **erőforrás-azonosítóját,** amely az űrlapból áll. A portál használatához használja a kép **nevét.**  
  - A felügyelt rendszerkép-erőforrásnak léteznie kell a készlet élettartama alatt a felskálázás engedélyezéséhez, és a készlet törlése után eltávolítható.

- **Az Azure Active Directory (AAD) hitelesítése.** A batch-ügyfél API-nak AAD-hitelesítést kell használnia. Az AAD Azure Batch-támogatása az Active Directoryval rendelkező [Batch szolgáltatásmegoldások hitelesítése szolgáltatásmegoldásokban](batch-aad-auth.md)van dokumentálva.

## <a name="prepare-a-custom-image"></a>Egyéni kép előkészítése

Az Azure-ban a következő innen készíthet el felügyelt lemezképet:

- Az Azure virtuális gép operációs rendszerének és adatlemezeinek pillanatképei
- Általános Azure-alapú virtuális gép felügyelt lemezekkel
- A felhőbe feltöltött általános helyszíni virtuális merevlemez

A batch készletek megbízható méretezéséhez egy egyéni lemezképpel, javasoljuk, hogy *hozzon* létre egy felügyelt rendszerképet csak az első módszerrel: a virtuális gép lemezeinek pillanatképeihasználatával. Tekintse meg a következő lépéseket a virtuális gép előkészítéséhez, pillanatkép készítése, és hozzon létre egy képet a pillanatképből.

### <a name="prepare-a-vm"></a>Virtuális gép előkészítése

Ha új virtuális gép a lemezképhez, használja a batch által támogatott, a batch által támogatott, a felügyelt lemezkép alaplemezképe ként egy első féltől származó Azure Marketplace-lemezkép használata. Alapképként csak a first party képek használhatók. Az Azure Marketplace-en az Azure Batch által támogatott Azure Marketplace-rendszerrendszer-hivatkozások teljes listájának beolvassa: [List node agent SKUs](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) operation.

> [!NOTE]
> Nem használhat olyan külső lemezképet, amely nek további licenc- és vásárlási feltételei vannak alaplemezképként. Ezekről a Marketplace-lemezképekről a [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) vagy a [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) virtuális gépekre vonatkozó útmutatásban talál tájékoztatást.

- Győződjön meg arról, hogy a virtuális gép felügyelt lemezzel jön létre. Ez az alapértelmezett tárolási beállítás, amikor virtuális gép létrehozásakor.
- Ne telepítse az Azure-bővítményeket, például az egyéni parancsfájl-bővítményt a virtuális gépre. Ha a lemezkép egy előre telepített bővítményt tartalmaz, az Azure problémákba ütközhet a batch készlet telepítésekor.
- Csatlakoztatott adatlemezek használata esetén csatlakoztatnia kell és formáznia kell a virtuális gépből származó lemezeket azok használatához.
- Győződjön meg arról, hogy a megadott operációsrendszer-lemezkép az alapértelmezett ideiglenes meghajtót használja. A Batch csomópont ügynök jelenleg az alapértelmezett ideiglenes meghajtót várja.
- Miután a virtuális gép fut, csatlakozzon hozzá RDP (Windows) vagy SSH (Linux) keresztül. Telepítse a szükséges szoftvert, vagy másolja a kívánt adatokat.  

### <a name="create-a-vm-snapshot"></a>Virtuálisgép-pillanatkép létrehozása

A pillanatkép a virtuális merevlemez teljes, csak olvasható példánya. A virtuális gép operációs rendszerének vagy adatlemezeinek pillanatképének létrehozásához használhatja az Azure Portalt vagy a parancssori eszközöket. A pillanatkép létrehozásának lépéseit és lehetőségeit a [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) [vagy](../virtual-machines/windows/snapshot-copy-managed-disk.md) windows os virtuális gépekre vonatkozó útmutatásban talál.

### <a name="create-an-image-from-one-or-more-snapshots"></a>Kép létrehozása egy vagy több pillanatképből

Felügyelt lemezkép pillanatképből való létrehozásához használja az Azure parancssori eszközeit, például az [az image create](/cli/azure/image) parancsot. Lemezkép-pillanatkép és adott esetben egy vagy több adatlemez-pillanatkép megadásával hozhat létre lemezképet.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Készlet létrehozása egyéni lemezképből a portálon

Miután mentette az egyéni lemezképet, és ismeri annak erőforrásazonosítóját vagy nevét, hozzon létre egy batch készletet abból a lemezképből. A következő lépések bemutatják, hogyan hozhat létre egy készletet az Azure Portalon.

> [!NOTE]
> Ha a készletet a Batch API-k egyikével hozza létre, győződjön meg arról, hogy az AAD-hitelesítéshez használt identitás rendelkezik-e a lemezkép-erőforráshoz. Lásd: [Kötegelt szolgáltatásmegoldások hitelesítése az Active Directoryval.](batch-aad-auth.md)
>
> A felügyelt lemezkép erőforrásának léteznie kell a készlet élettartama alatt. Ha az alapul szolgáló erőforrást törlik, a készlet nem méretezhető.

1. Az Azure portálon lépjen Batch-fiókjára. Ennek a fióknak ugyanabban az előfizetésben és régióban kell lennie, mint az egyéni lemezképet tartalmazó erőforráscsoportnak.
2. A bal oldali **Beállítások** ablakban válassza a **Készletek** menüelemet.
3. A **Készletek** ablakban válassza a **Hozzáadás** parancsot.
4. A **Készlet hozzáadása** ablakban válassza az **Egyéni lemezkép (Linux/Windows)** lehetőséget a **Képtípus** legördülő menüből. Az **egyéni virtuálisgép-lemezkép** legördülő menüből válassza ki a lemezkép nevét (az erőforrás-azonosító rövid formája).
5. Válassza ki a megfelelő **Publisher/Offer/Sku** az egyéni lemezképhez.
6. Adja meg a fennmaradó szükséges beállításokat, beleértve a **csomópont méretét**, a cél **dedikált csomópontokat**és **az alacsony prioritású csomópontokat,** valamint a kívánt választható beállításokat.

    Egy Microsoft Windows Server Datacenter 2016 egyéni lemezkép esetén például a **Készlet hozzáadása** ablak az alábbi módon jelenik meg:

    ![Készlet hozzáadása egyéni Windows-lemezképből](media/batch-custom-images/add-pool-custom-image.png)
  
Annak ellenőrzéséhez, hogy egy meglévő készlet egyéni lemezképen alapul-e, tekintse meg az **Operációs rendszer** tulajdonságot a **Készlet** ablak erőforrás-összegzési szakaszában. Ha a készlet et egyéni lemezképből hozták létre, **akkor egyéni virtuálisgép-lemezkép**lesz beállítva.

A készlethez társított összes egyéni kép megjelenik a készlet **Tulajdonságok** ablakában.

## <a name="considerations-for-large-pools"></a>A nagy medencékkel kapcsolatos szempontok

Ha azt tervezi, hogy hozzon létre egy készletet több száz virtuális gép vagy több egyéni lemezkép használatával, fontos, hogy kövesse az előző útmutatást egy virtuális gép pillanatképből létrehozott lemezkép használatával.

Vegye figyelembe a következő szempontokat is:

- **Méretkorlátok** – A kötega készlet méretét 2500 dedikált számítási csomópontra vagy 1000 alacsony prioritású csomópontra korlátozza, ha egyéni lemezképet használ.

  Ha ugyanazt a lemezképet (vagy ugyanazon alapul szolgáló pillanatkép alapján több lemezképet) használ, több készlet létrehozásához, a készletekben lévő összes számítási csomópont nem haladhatja meg az előző korlátokat. Nem javasoljuk, hogy egy kép, vagy annak alapjául szolgáló pillanatkép több mint egy készlet.

  A korlátok csökkenthetők, ha a [készletet bejövő NAT-készletekkel konfigurálja.](pool-endpoint-configuration.md)

- **Időtúlméretezés** – Ha a készlet rögzített számú csomópontot tartalmaz (nem automatikus skálázás), növelje a készlet átsizeTimeout tulajdonságát egy például 20–30 perces értékre. Ha a készlet nem éri el a célméretét az időtúlidőszakon belül, hajtson végre egy másik [átméretezési műveletet.](/rest/api/batchservice/pool/resize)

  Ha azt tervezi, egy készlet több mint 300 számítási csomópontok, előfordulhat, hogy át kell méretezni a készlet többször, hogy elérje a célméretet.
  
A [Megosztott képtár](batch-sig-images.md)használatával nagyobb készleteket hozhat létre a testreszabott képekkel és a több megosztott kép replikával. A megosztott képek használatával a készlet állandósult állapotának eléréséhez szükséges idő akár 25%-kal gyorsabb, és a virtuális gép tétlenkés ideje akár 30%-kal rövidebb is lehet.

## <a name="considerations-for-using-packer"></a>A Csomagoló használatának szempontjai

Felügyelt lemezkép-erőforrás közvetlenül a Packer csak akkor végezhető el a felhasználói előfizetési mód Batch-fiókok. Batch szolgáltatás mód fiókok, először létre kell hoznia egy virtuális merevlemezt, majd importálja a virtuális merevlemezt egy felügyelt rendszerkép-erőforrásba. A készletfoglalási módtól (felhasználói előfizetés vagy Batch-szolgáltatás) függően a felügyelt lemezkép-erőforrás létrehozásának lépései eltérőek lehetnek.

Győződjön meg arról, hogy a felügyelt lemezkép létrehozásához használt erőforrás az egyéni lemezképet tartalmazó készletek élettartama alatt létezik. Ennek elmulasztása készletfoglalási hibákat és/vagy átméretezési hibákat eredményezhet.

Ha a képet vagy az alapul szolgáló erőforrást eltávolítja, a következőhöz hasonló hibaüzenet jelenhet meg: `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`. Ha ez a hiba jelenik meg, győződjön meg arról, hogy az alapul szolgáló erőforrás nem lett eltávolítva.

Ha többet szeretne tudni arról, hogy miként hozhat létre virtuális gépeket a Packer használatával, olvassa el [a Linux-lemezkép létrehozása packerrel](../virtual-machines/linux/build-image-with-packer.md) vagy [a Windows-lemezkép készítése a Packerrel című témakört.](../virtual-machines/windows/build-image-with-packer.md)

## <a name="next-steps"></a>További lépések

A Batch részletes áttekintését a [Nagyméretű párhuzamos számítási megoldások fejlesztése a Batch segítségével](batch-api-basics.md)című témakörben találja.
