---
title: Azure Batch készlet kiépítése egyéni rendszerképből | Microsoft Docs
description: Hozzon létre egy batch-készletet egy egyéni rendszerképből olyan számítási csomópontok kiépítéséhez, amelyek tartalmazzák az alkalmazáshoz szükséges szoftvereket és adatadatokat. Az egyéni lemezképek hatékony módszer a számítási csomópontok konfigurálására a Batch-munkaterhelések futtatásához.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 04/15/2019
ms.author: lahugh
ms.openlocfilehash: 54456ff48ca7104cc1ba10ddc47cec1bc364ddf6
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323689"
---
# <a name="use-a-custom-image-to-create-a-pool-of-virtual-machines"></a>Egyéni rendszerkép használata virtuális gépek készletének létrehozásához 

Ha Azure Batch-készletet hoz létre a virtuális gép konfigurációjával, meg kell adnia egy virtuálisgép-rendszerképet, amely a készlet minden számítási csomópontja számára biztosítja az operációs rendszert. Létrehozhat egy virtuális gépek készletét egy támogatott Azure Marketplace-lemezképpel vagy egy egyéni lemezképpel (egy saját maga által létrehozott és konfigurált virtuálisgép-lemezképpel). Az egyéni rendszerképnek a Batch-fiókkal azonos Azure-előfizetéshez és-régióhoz tartozó *felügyelt rendszerkép* -erőforrásnak kell lennie.

## <a name="benefits-of-custom-images"></a>Az Egyéni rendszerképek előnyei

Egyéni rendszerkép megadásakor szabályozhatja az operációs rendszer konfigurációját, valamint a használni kívánt operációs rendszer és adatlemezek típusát. Az egyéni rendszerkép tartalmazhat olyan alkalmazásokat és referenciákat, amelyek a Batch-készlet összes csomópontján elérhetővé válnak a kiépítés után.

Az egyéni rendszerkép használatával időt takaríthat meg a készlet számítási csomópontjainak előkészítésével a Batch számítási feladatok futtatásához. Habár a kiépítés után az egyes számítási csomópontokon egy Azure Marketplace-rendszerképet használhat, és az egyéni rendszerkép használatával hatékonyabban telepítheti a szoftvereket.

A forgatókönyvhöz konfigurált egyéni rendszerkép használata több előnyt is biztosíthat:

- **Konfigurálja az operációs rendszert (operációs rendszer)** . Testreszabhatja a lemezkép operációsrendszer-lemezének konfigurációját. 
- **Alkalmazások telepítése előtt.** Alkalmazások előzetes telepítése az operációsrendszer-lemezen, amely hatékonyabb és kevésbé hasonló, mint az alkalmazások telepítése a számítási csomópontok indítási tevékenységgel történő kiépítés után.
- **A virtuális gépek újraindítási idejének mentése.** Az alkalmazás telepítése általában a virtuális gép újraindítását igényli, amely időigényes. Az újraindítási időt az alkalmazások telepítése előtt is mentheti. 
- **Nagyon nagy mennyiségű adatmennyiség másolása egyszer.** A felügyelt egyéni rendszerkép statikus adatok részét képezheti úgy, hogy egy felügyelt rendszerkép adatlemezére másolja. Ezt csak egyszer kell elvégezni, és a készlet minden csomópontja számára elérhetővé kell tenni az adatmennyiséget.
- **Választható lemez típusok.** Az operációsrendszer-lemez és az adatlemez Premium Storage szolgáltatásával választhat.
- **A készleteket nagy méretekre növelheti.** Ha felügyelt egyéni rendszerképet használ egy készlet létrehozásához, a készlet növelheti a lemezkép-blob VHD-k másolását anélkül, hogy el kellene végeznie.

## <a name="prerequisites"></a>Előfeltételek

- **Felügyelt rendszerkép erőforrása**. Ha egyéni rendszerkép használatával kívánja létrehozni a virtuális gépek készletét, akkor a Batch-fiókkal azonos Azure-előfizetésben és régióban kell létrehoznia egy felügyelt rendszerkép-erőforrást, vagy létre kell hoznia azt. A lemezképet létre kell hozni a virtuális gép operációsrendszer-lemezének pillanatképei és opcionálisan csatlakoztatott adatlemezei között. A felügyelt rendszerképek előkészítésével kapcsolatos további információkért és lépésekhez tekintse meg a következő szakaszt.
  - Használjon egyedi egyéni rendszerképet a létrehozott készletekhez.
  - Ha a Batch API-k használatával szeretne készletet létrehozni a képpel,  akkor az űrlaphoz `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`tartozó rendszerkép erőforrás-azonosítóját kell megadnia. A portál használatához használja a rendszerkép **nevét** .  
  - A felügyelt rendszerkép-erőforrásnak léteznie kell a készlet élettartamához a felskálázás engedélyezéséhez, és a készlet törlése után eltávolítható.

- **Azure Active Directory (HRE) hitelesítés**. A Batch-ügyfél API-HRE hitelesítést kell használnia. Azure Batch a HRE támogatása a [Batch szolgáltatással kapcsolatos megoldások Active Directory](batch-aad-auth.md)használatával történő hitelesítésében van dokumentálva.

## <a name="prepare-a-custom-image"></a>Egyéni rendszerkép előkészítése

Az Azure-ban felkészítheti a felügyelt rendszerképeket egy Azure-beli virtuális gép operációs rendszerének és adatlemezének pillanatképei közül egy felügyelt lemezekkel rendelkező, általánosított Azure-beli virtuális gépről, vagy egy feltöltött, általános helyszíni VHD-ről. Ha a Batch-készleteket egy egyéni rendszerkép használatával szeretné megbízhatóan méretezni, javasoljuk, hogy *csak* az első metódussal hozzon létre egy felügyelt képet: a virtuális gép lemezei Pillanatképek használatával. Tekintse át a virtuális gép előkészítésének lépéseit, készítsen pillanatképet, és hozzon létre egy rendszerképet a pillanatképből.

### <a name="prepare-a-vm"></a>Virtuális gép előkészítése

Ha új virtuális gépet hoz létre a rendszerképhez, használja a Batch által támogatott első féltől származó Azure Marketplace-rendszerképet a felügyelt rendszerkép alaprendszerképének megfelelően. Alaprendszerképként csak az első féltől származó képek használhatók. Az Azure Batch által támogatott Azure Marketplace-rendszerkép-referenciák teljes listájának megjelenítéséhez tekintse meg a [csomópont-ügynök SKU](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) -azonosítóinak listázása műveletet.

> [!NOTE]
> Alaprendszerképként nem használhat olyan külső gyártótól származó rendszerképet, amely további licenccel és vásárlási feltételekkel rendelkezik. További információ ezekről a Piactéri lemezképekről [: Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) vagy [Windows rendszerű](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) virtuális gépek útmutatója.


* Győződjön meg arról, hogy a virtuális gép felügyelt lemezzel lett létrehozva. A virtuális gép létrehozásakor ez az alapértelmezett tárolási beállítás.
* Ne telepítse az Azure-bővítményeket, például az egyéni szkriptek bővítményét a virtuális gépen. Ha a lemezkép előre telepített bővítményt tartalmaz, az Azure problémákba ütközhet a Batch-készlet telepítésekor.
* Csatolt adatlemezek használatakor a lemezeket csatlakoztatnia kell a virtuális gépről, és formázni kell őket a használatuk során.
* Győződjön meg arról, hogy az Ön által megadott alap operációsrendszer-rendszerkép az alapértelmezett ideiglenes meghajtót használja. A Batch-csomópont ügynöke jelenleg az alapértelmezett ideiglenes meghajtót várja.
* Ha a virtuális gép fut, csatlakozzon hozzá RDP-kapcsolaton keresztül (Windows rendszeren) vagy SSH-n keresztül (Linux rendszeren). Telepítse a szükséges szoftvereket, vagy másolja a kívánt fájlokat.  

### <a name="create-a-vm-snapshot"></a>Virtuális gép pillanatképének létrehozása

A pillanatkép egy virtuális merevlemez teljes, írásvédett másolata. A virtuális gép operációs rendszerének vagy adatlemezének pillanatképének létrehozásához használhatja a Azure Portal vagy a parancssori eszközöket. A pillanatképek létrehozásához szükséges lépéseket és beállításokat a [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) vagy a [Windows rendszerű](../virtual-machines/windows/snapshot-copy-managed-disk.md) virtuális gépek útmutatója tartalmazza.

### <a name="create-an-image-from-one-or-more-snapshots"></a>Rendszerkép létrehozása egy vagy több pillanatképből

Felügyelt rendszerkép pillanatképből való létrehozásához használja az Azure parancssori eszközeit, például az az [rendszerkép Create](/cli/azure/image) parancsot. Rendszerkép létrehozásához az operációsrendszer-lemez pillanatképének megadásával és opcionálisan egy vagy több adatlemez-pillanatképet kell megadnia.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Készlet létrehozása egyéni rendszerképből a portálon

Miután mentette az egyéni rendszerképet, és ismeri annak erőforrás-AZONOSÍTÓját vagy nevét, hozzon létre egy batch-készletet a rendszerképből. A következő lépések bemutatják, hogyan hozhat létre készletet a Azure Portalból.

> [!NOTE]
> Ha a készletet a Batch API-k egyikével hozza létre, győződjön meg arról, hogy a HRE-hitelesítéshez használt identitás rendelkezik a rendszerkép-erőforráshoz szükséges engedélyekkel. Lásd: [a Batch szolgáltatással kapcsolatos megoldások hitelesítése Active Directory](batch-aad-auth.md)használatával.
>
> A készlet élettartamához a felügyelt rendszerkép erőforrásának léteznie kell. Ha a mögöttes erőforrás törölve lett, a készlet nem méretezhető. 

1. Az Azure portálon lépjen Batch-fiókjára. Ennek a fióknak ugyanabban az előfizetésben és régióban kell lennie, mint az egyéni rendszerképet tartalmazó erőforráscsoport. 
2. A bal oldali **Beállítások** ablakban válassza a **készletek** menüpontot.
3. A **készletek** ablakban válassza a **Hozzáadás** parancsot.
4. A **készlet hozzáadása** ablakban válassza az **Egyéni rendszerkép (Linux/Windows)** lehetőséget a **rendszerkép típusa** legördülő menüből. Az egyéni virtuálisgép- **rendszerkép** legördülő menüben válassza ki a rendszerkép nevét (az erőforrás-azonosító rövid formáját).
5. Válassza ki a megfelelő **közzétevőt/ajánlatot/SKU-** t az egyéni rendszerképhez.
6. Megadhatja a fennmaradó szükséges beállításokat, beleértve a **csomópont méretét**, a **célként kijelölt csomópontokat**és az **alacsony prioritású csomópontokat**, valamint a kívánt választható beállításokat.

    A Microsoft Windows Server Datacenter 2016 egyéni rendszerkép esetében például a **készlet hozzáadása** ablak jelenik meg, ahogy az alábbi ábrán látható:

    ![Készlet hozzáadása egyéni Windows-rendszerképből](media/batch-custom-images/add-pool-custom-image.png)
  
Annak megállapításához, hogy egy meglévő készlet egyéni rendszerképen alapul-e, tekintse meg az **operációs rendszer** tulajdonságot a **készlet** ablak erőforrás összegzése szakaszában. Ha a készlet egyéni rendszerképből lett létrehozva, akkor az **Egyéni**virtuálisgép-rendszerképre van beállítva.

A készlethez társított összes egyéni rendszerkép a készlet **Tulajdonságok** ablakában jelenik meg.

## <a name="considerations-for-large-pools"></a>A nagyméretű készletek szempontjai

Ha több száz virtuális géppel vagy más egyéni rendszerkép használatával szeretne létrehozni egy készletet, fontos, hogy kövesse az előző útmutatást a virtuálisgép-pillanatképből létrehozott rendszerképek használatához.

Vegye figyelembe a következőket is:

- **Méretkorlát** – a Batch korlátozza a készlet méretét 2500 dedikált számítási csomópontokra, vagy 1000 alacsony prioritású csomópontokra, amikor egyéni rendszerképet használ.

  Ha ugyanazt a lemezképet (vagy ugyanazon mögöttes pillanatkép alapján több lemezképet) használja több készlet létrehozásához, a készletekben lévő összes számítási csomópont nem lépheti túl az előző korlátozásokat. Nem javasoljuk, hogy egy rendszerképet vagy annak mögöttes pillanatképét egyetlen készletnél ne használja.

  A határértékek csökkenhetnek, ha a készletet [bejövő NAT](pool-endpoint-configuration.md)-készletekkel konfigurálja.

- **Átméretezés időtúllépése** – ha a készlet rögzített számú csomópontot tartalmaz (nem automatikus méretezés), növelje a készlet resizeTimeout tulajdonságát egy értékre (például 20-30 percre). Ha a készlet nem éri el a célként megadott méretet az időtúllépési időszakon belül, hajtson végre egy másik átméretezési [műveletet](/rest/api/batchservice/pool/resize).

  Ha több mint 300 számítási csomóponttal rendelkező készletet tervez, előfordulhat, hogy a készlet méretét többször át kell méreteznie a cél méretének eléréséhez.

## <a name="considerations-for-using-packer"></a>A csomagoló használatának szempontjai

Felügyelt rendszerkép-erőforrás közvetlenül a Csomagolóval való létrehozása csak a felhasználói előfizetés módú batch-fiókokkal végezhető el. A Batch Service Mode-fiókok esetében először létre kell hoznia egy virtuális merevlemezt, majd importálnia kell a virtuális merevlemezt egy felügyelt rendszerkép-erőforrásba. A készlet lefoglalási módjától (felhasználói előfizetés vagy batch szolgáltatás) függően a felügyelt rendszerkép-erőforrások létrehozásának lépései eltérőek lesznek.

Győződjön meg arról, hogy a felügyelt rendszerkép létrehozásához használt erőforrás létezik az egyéni rendszerképre hivatkozó bármely készlet élettartama tekintetében. Ennek elmulasztása esetén a készlet lefoglalási hibáit és/vagy az átméretezési hibákat okozhatják. 

Ha a rendszerkép vagy a mögöttes erőforrás el lett távolítva, a következőhöz hasonló hibaüzenetet kaphat: `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`. Ha ez történik, győződjön meg arról, hogy a mögöttes erőforrás nem lett eltávolítva.

További információ a virtuális gép létrehozásáról a csomagoló használatával: Linux- [rendszerkép létrehozása a csomagolóval](../virtual-machines/linux/build-image-with-packer.md) vagy Windows- [rendszerkép](../virtual-machines/windows/build-image-with-packer.md)készítése a csomagolóval.

## <a name="next-steps"></a>További lépések

- A Batch részletes áttekintése: [nagy léptékű párhuzamos számítási megoldások létrehozása a Batch szolgáltatással](batch-api-basics.md).
