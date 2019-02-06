---
title: Egyéni rendszerképpel Azure Batch-készlet kiépítése |} A Microsoft Docs
description: Hozzon létre egy Batch-készlet egyéni rendszerképpel történő üzembe helyezése számítási csomópontok, amelyek a szoftverek és az alkalmazás számára szükséges adatokat tartalmazzák. Egyéni rendszerképek olyan hatékony módja a Batch számítási feladatok futtatásához a számítási csomópontok konfigurálása.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 10/04/2018
ms.author: lahugh
ms.openlocfilehash: 0bc43b82a987ab065677bdbb56de73ef341c249d
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55752126"
---
# <a name="use-a-custom-image-to-create-a-pool-of-virtual-machines"></a>Hozzon létre egy virtuálisgép-készletek egyéni rendszerkép használatával 

Virtuálisgép-konfiguráció használata egy Azure Batch-készlet létrehozásakor meg kell adnia az operációs rendszer biztosít a készlet egyes számítási csomópontok Virtuálisgép-rendszerkép. Létrehozhat egy virtuálisgép-készletek, vagy egy támogatott Azure Marketplace-rendszerképpel, vagy egyéni rendszerképpel (Virtuálisgép-rendszerkép létrehozása és konfigurálása saját maga). Az egyedi képnek kell lennie egy *felügyelt rendszerkép* erőforrás Azure-előfizetéshez és régióban, mint a Batch-fiókot.

## <a name="why-use-a-custom-image"></a>Egyéni rendszerkép miért érdemes használni?

Egy egyéni rendszerképet ad meg, ha rendelkezik az operációs rendszer konfigurációs és operációs rendszerének és adatlemezeinek használandó típusát szabályozhatja. Az egyéni rendszerkép alkalmazások és az összes Batch-készlet csomópontjainak elérhetővé válnak, amint kiépítésüket referenciaadatokat is tartalmazhat.

Egyéni rendszerkép használatával időt takaríthat meg a készlet számítási csomópontjait futtatni a Batch számítási feladatok előkészítésében. Használja az Azure Marketplace-rendszerképpel, és telepítse a szoftver minden számítási csomóponton üzembe helyezés után, hatékonyabb lehet egyéni rendszerkép használatával.

A forgatókönyv konfigurált egyéni rendszerkép használatával megadhat több előnye is van:

- **Az operációs rendszer (OS) konfigurálása**. Testre szabhatja a lemezkép operációsrendszer-lemez konfigurációja. 
- **A telepítés előtti alkalmazások.** A telepítés előtti az operációsrendszer-lemez, amely hatékonyabb és kisebb hibalehetőséget magában rejtő, alkalmazások telepítése után kiépítése a számítási csomópontok indítási tevékenységet használ, mint az alkalmazások.
- **Újraindítás időt takaríthat meg a virtuális gépeken.** Alkalmazás telepítése általában igényel, amely időigényes a virtuális gép újraindítása. Újraindítási időpontot előre telepíti az alkalmazásokat mentheti. 
- **Másolja a nagyon nagy mennyiségű adat egyszer.** Győződjön meg a felügyelt egyéni lemezképek statikus adatok része egy felügyelt rendszerképet adatlemezek másolásával. Ez csak egyszer kell elvégezni, és a készlet minden csomópontján elérhetővé adatokat.
- **A választott lemeztípusokat.** Választhat, hogy a premium Storage tárolást használ az operációsrendszer-lemez és az adatlemezt.
- **Készletek növekszik, nagy méretű.** Felügyelt egyéni lemezképek használatával létrehoz egy készletet, ha a készlet a kép blob VHD-k példányát, nem növelhető. 


## <a name="prerequisites"></a>Előfeltételek

- **Egy felügyelt rendszerképet erőforrás**. Egyéni rendszerkép használatával virtuális gépek készletének létrehozása, szüksége van, vagy hozzon létre egy felügyelt rendszerképet erőforrás Azure-előfizetéshez és régióban, mint a Batch-fiók. A kép kell létrehozni a virtuális gép operációsrendszer-lemez, és szükség esetén a csatlakoztatott adatlemezek pillanatképeiből. További információkért és a egy felügyelt rendszerképet előkészületek tekintse meg a következő szakaszban. 
  - Egy egyedi egyéni lemezkép használata minden egyes készletet hoz létre.
  - Készlet létrehozása a lemezképet, a Batch API-k használatával, adja meg a **erőforrás-azonosító** a képe, amely az űrlap `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. A portál használatához használja a **neve** a kép.  
  - A felügyelt lemezkép-erőforrás léteznie kell, hogy a vertikális felskálázás a készlet teljes élettartama, és távolíthatja el a készlet törlése után.

- **Az Azure Active Directory (AAD) hitelesítés**. A Batch-ügyfél API-t az AAD-hitelesítést kell használnia. Azure Batch-támogatás az AAD leírása itt található [az Active Directory hitelesítést Batch-szolgáltatási megoldások](batch-aad-auth.md).

## <a name="prepare-a-custom-image"></a>Egyéni lemezkép előkészítése

Az Azure-ban előkészítheti egy felügyelt rendszerképet egy Azure virtuális gép operációsrendszer- és adatlemezek pillanatképeiből, általánosított virtuális gépből az Azure managed disks szolgáltatással vagy feltöltött helyszíni általánosított virtuális merevlemezből. A Batch-készletek egyéni rendszerképpel megbízhatóan méretezheti, javasoljuk, hogy létrehozása egy felügyelt rendszerképet használó *csak* az első módszer: a Virtuálisgép-lemezek pillanatképei. Lásd az alábbi lépéseket egy virtuális gép előkészítése, a pillanatkép készítése és a rendszerkép létrehozása a pillanatképből. 

### <a name="prepare-a-vm"></a>A virtuális gép előkészítése 

A lemezkép egy új virtuális Gépet hoz létre, ha használja az Azure Marketplace-rendszerképpel, mint az alaprendszerképet a felügyelt rendszerkép támogatja a kötegelt és testre szabni.  Azure Batch által támogatott Azure Marketplace-en képhivatkozásokkal listájának lekéréséhez tekintse meg a [lista csomóponti ügynök SKU-k](/rest/api/batchservice/account/listnodeagentskus) műveletet. 

> [!NOTE]
> További licenc és az alaplemezkép beszerzési feltételek rendelkező külső lemezképe nem használható. A Marketplace-rendszerképek kapcsolatos információkért tekintse meg az útmutató a [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) vagy [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) virtuális gépeket.


* Győződjön meg arról, hogy a virtuális Gépet hozunk létre egy felügyelt lemezt. Ez az alapértelmezett tároló beállítása, a virtuális gép létrehozásakor.
* Ne telepítse az Azure-bővítmény, az egyéni szkriptek futtatására szolgáló bővítmény, például a virtuális gépen. Ha a rendszerkép előre telepített bővítményt tartalmaz, az Azure problémába ütközhet, a Batch-készlet üzembe helyezésekor.
* Győződjön meg arról, hogy az alap operációsrendszer-képet, adja meg az alapértelmezett ideiglenes meghajtót használja-e. A Batch-csomópontügynök jelenleg ezt várja az alapértelmezett ideiglenes meghajtót.
* Ha a virtuális gép fut, a csatlakozás RDP-(a Windows) vagy (Linux) esetén az SSH-n keresztül. Szükséges szoftver telepítését, vagy másolja ki a kívánt adathoz.  

### <a name="create-a-vm-snapshot"></a>Hozzon létre egy virtuális gép pillanatképét

Pillanatkép egy virtuális merevlemez teljes, a csak olvasható példányát. Pillanatkép létrehozása a virtuális gép operációs rendszer vagy az adatlemezeket, használhatja az Azure portal vagy a parancssori eszközöket. Lépéseket és a beállítások segítségével készít pillanatképet, lásd az útmutató a [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) vagy [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) virtuális gépeket.

### <a name="create-an-image-from-one-or-more-snapshots"></a>Rendszerkép létrehozása egy vagy több pillanatképekből

Létrehozhat egy felügyelt rendszerképet egy pillanatképből, használja az Azure parancssori eszközök például a [az lemezkép létrehozása](/cli/azure/image) parancsot. Adjon meg egy operációsrendszer-lemez pillanatfelvételének és opcionálisan egy vagy több lemez pillanatképeinek rendszerképet hozhat létre.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Készlet létrehozása egyéni rendszerképből a portálon

Miután mentette az egyéni rendszerkép, és ismeri az erőforrás-azonosító vagy név, a Batch-készlet létrehozása a rendszerképből. A következő lépések bemutatják, hogyan hoz létre egy készletet az Azure Portalról.

> [!NOTE]
> Ha hoz létre a készletet a Batch API-k egyikével, ügyeljen arra, hogy az identitás AAD-hitelesítést használ a jogosult a rendszerkép-erőforráshoz. Lásd: [az Active Directory hitelesítést Batch-szolgáltatási megoldások](batch-aad-auth.md).
>

1. Az Azure portálon lépjen Batch-fiókjára. Ez a fiók az erőforráscsoport, amely tartalmazza az egyéni rendszerkép azonos előfizetésben és régióban kell lennie. 
2. Az a **beállítások** ablak bal oldalán válassza ki a **készletek** menüpontot.
3. Az a **készletek** ablakban válassza ki a **Hozzáadás** parancsot.
4. Az a **készlet hozzáadása** ablakban válassza **egyéni lemezkép (Linux/Windows)** származó a **képtípus** legördülő listából. Az a **egyéni Virtuálisgép-rendszerkép** legördülő menüben válassza ki a rendszerkép nevének (rövid alak az erőforrás-azonosító).
5. Válassza ki a megfelelő **közzétevő/ajánlat/Sku** az egyéni rendszerkép.
6. Adja meg a fennmaradó beállításokat, beleértve a **csomópontméret**, **dedikált csomópontok célszáma**, és **alacsony prioritású csomópontok célszáma**, illetve bármely kívánt, nem kötelező beállítások.

    Például a Microsoft Windows Server Datacenter 2016 egyéni lemezkép a **készlet hozzáadása** ablakban a lent látható módon jelenik meg:

    ![Készlet hozzáadása az egyéni Windows-rendszerképből](media/batch-custom-images/add-pool-custom-image.png)
  
Egyéni lemezkép alapján egy meglévő készlet ellenőrzéséhez tekintse meg a **operációs rendszer** tulajdonság az erőforrás összefoglaló szakaszában a **készlet** ablak. Ha a készletet jött létre egyéni rendszerképpel, van-e állítva **egyéni Virtuálisgép-rendszerkép**.

A készlethez társított összes egyéni lemezképek jelennek meg a készleten **tulajdonságok** ablak.

## <a name="considerations-for-large-pools"></a>A készletek nagy méretű szempontjai

Ha azt tervezi, több száz virtuális gép vagy több egyéni lemezkép alapján hoz létre egy készletet, fontos kövesse az előző útmutatás egy VM-pillanatképet készített rendszerkép használatához.

Vegye figyelembe a következőket is:

- **Méretkorlátozásai** – Batch a készlet méretét 2500 dedikált számítási csomópontok vagy 1000 alacsony prioritású csomópontok, korlátozza az egyéni rendszerkép használatával.

  Ha ugyanazt a lemezképet (vagy több lemezképet mögöttes pillanatképeket alapján) több készletet létrehozni, a készletek összesített számítási csomópontjain nem lépik túl az előző. Nem ajánlott, több mint egyetlen kép vagy a mögöttes pillanatkép használata.

  Korlátok csökkenteni lehet, ha a készlet konfigurálása [bejövő NAT-készletek](pool-endpoint-configuration.md).

- **Átméretezés időtúllépése** : Ha a készlet tartalmaz egy rögzített csomópontok száma (nem az automatikus skálázási), növelje a készlet például 20 – 30 perc értékre resizeTimeout tulajdonságát. Ha a készlet nem éri el a cél méretét az időkorláton belül, hajtson végre egy újabb [átméretezési művelet](/rest/api/batchservice/pool/resize).

  Ha több mint 300 számításicsomópont-készlet, szüksége lehet elérni a célméretet többször is feldolgozza a készlet átméretezése.

## <a name="next-steps"></a>További lépések

- A Batch részletesebb áttekintéséért lásd: [Develop nagy léptékű párhuzamos számítási megoldások Batch segítségével történő](batch-api-basics.md).
