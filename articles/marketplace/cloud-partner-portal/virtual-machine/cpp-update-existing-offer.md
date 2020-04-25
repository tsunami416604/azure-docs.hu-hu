---
title: Meglévő VM-ajánlat frissítése az Azure Marketplace-en
description: Ismerteti, hogyan lehet frissíteni egy meglévő virtuálisgép-ajánlatot az Azure Marketplace-en.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: dsindona
ms.openlocfilehash: 934be24d07c01c76c8caf5e16af4b765df79c964
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82142917"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>Meglévő VM-ajánlat frissítése az Azure Marketplace-en

> [!IMPORTANT]
> 2020. április 13-ától kezdődően megkezdjük az Azure-beli virtuálisgép-ajánlatok felügyeletének áthelyezését a partneri központba. Az áttelepítés után létrehozhatja és kezelheti az ajánlatokat a partner Centerben. Kövesse az Azure-beli [virtuális gép létrehozása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) az áttelepített ajánlatok kezeléséhez című témakör utasításait.

Ez a cikk végigvezeti a virtuális gép (VM) ajánlatának a [Cloud Partner Portalban](https://cloudpartner.azure.com/) való frissítésének különböző szempontjain, majd újból közzéteszi az ajánlatot.

Az ajánlat frissítésének számos általános oka van, többek között a következők:

-  Új virtuálisgép-rendszerkép verziójának hozzáadása meglévő SKU-hoz
-  Az SKU-ban elérhető régiók módosítása
-  Új SKU-i hozzáadása
-  A piactér metaadatainak frissítése az ajánlathoz vagy az egyedi SKU-hoz
-  Az utólagos elszámolású ajánlatok díjszabásának frissítése

A fenti módosításokkal kapcsolatban a portál az **összehasonlítási** és **előzményi** funkciókat kínálja.  

>[!Note]
>A Cloud Solution Providers (CSP) Partner Channel opt-in mostantól elérhető.  Az ajánlat Microsoft CSP-partneri csatornákon keresztüli forgalmazásával kapcsolatos további információkért tekintse meg a [Cloud Solution Providers](../../cloud-solution-providers.md) című témakört.

## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>A VM-ajánlat vagy SKU nem engedélyezett módosításai

A virtuálisgép-ajánlat vagy SKU néhány attribútuma nem módosítható, ha az ajánlat az Azure piactéren érhető el, főként:

-  Az ajánlat **azonosítója** és **közzétevő-azonosítója**
-  Meglévő SKU-i **SKU-azonosító**
-  Meglévő SKU-i adatlemezek száma
-  Számlázási/licencelési modell módosítása a meglévő SKU-ra
-  A közzétett SKU árának növekedése


## <a name="common-update-operations"></a>Gyakori frissítési műveletek

Bár a virtuális gépekre vonatkozó ajánlatokban számos különféle jellemzőt módosíthat, a következő műveletek gyakoriak.

### <a name="update-the-vm-image-version-for-a-sku"></a>SKU-beli virtuálisgép-rendszerkép verziójának frissítése

Gyakori, hogy a virtuálisgép-lemezképek rendszeres időközönként frissülnek a biztonsági javításokkal, a további funkciókkal és így tovább.  Ilyen esetekben az alábbi lépések végrehajtásával szeretné frissíteni az SKU-ra hivatkozó virtuálisgép-rendszerképet:

1.  Jelentkezzen be a [Cloud Partner Portalba](https://cloudpartner.azure.com/).

2.  Az **összes ajánlat**területen keresse meg a frissítendő ajánlatot.

3.  A **SKU** -i lapon kattintson a frissíteni kívánt virtuálisgép-rendszerképhez társított SKU-ra.

4.  Az új virtuálisgép-lemezkép hozzáadásához a **lemez verziója**területen kattintson az **+ új lemez verziója** elemre.

5.  Adja meg az új VM-lemezképek **lemezének verzióját**. A lemez verziójának a [szemantikai verzió](https://semver.org/) formátumát kell követnie. A verziónak X. Y. Z formátumúnak kell lennie, ahol az X, az Y és a Z egész számok. Ellenőrizze, hogy a megadott új verzió nagyobb-e az összes korábbi verziónál. Ellenkező esetben az új verzió ismételt közzététele nem jelenik meg a portálon vagy az Azure Marketplace-en.

6.  Az operációs rendszer **VHD URL-címe**mezőbe írja be az operációs rendszer virtuális merevlemezéhez létrehozott [megosztott elérési aláírás (SAS) URI azonosítóját](./cpp-get-sas-uri.md) . 

    > [!WARNING] 
    > Az adatlemezek száma nem változtatható meg az SKU különböző verziói között. Ha az előző verziókban adatlemezek lettek konfigurálva, akkor az új verziónak azonos számú adatlemezzel kell rendelkeznie.

7.  A **Közzététel** gombra kattintva elindíthatja a munkafolyamatot az új virtuálisgép-verzió Azure Marketplace-en való közzétételéhez.


### <a name="change-region-availability-of-a-sku"></a>SKU-régió elérhetőségének módosítása

Idővel előfordulhat, hogy az ajánlatot/SKU-t több régióban is elérhetővé kívánja tenni.  Azt is megteheti, hogy le szeretné állítani az ajánlat/SKU támogatását egy adott régióban.
A rendelkezésre állás módosításához kövesse az alábbi lépéseket:

1.  Jelentkezzen be a [Cloud Partner Portalba](https://cloudpartner.azure.com/).

2.  Az **összes ajánlat** területen keresse meg a frissíteni kívánt ajánlatot.

3.  A **SKU** -i lapon kattintson arra az SKU-ra, amelyen módosítani szeretné a rendelkezésre állását.

4.  Az **ország/régió rendelkezésre állása** mezőben kattintson az **országok kiválasztása** gombra.

5.  A régió rendelkezésre állása előugró ablakában adja hozzá vagy távolítsa el az adott SKU régióit.

6.  Kattintson a **Közzététel** gombra a közzétételi munkafolyamat elindításához a SKU-régió rendelkezésre állásának frissítéséhez.

Ha egy SKU-t egy új régióban kíván elérhetővé tenni, az **exportálási díjszabási** funkció segítségével megadhatja az adott régió díjszabását. Ha olyan régiót ad hozzá, amely korábban már elérhető volt, nem fogja tudni frissíteni a díjszabását, mert az árképzési változások nem engedélyezettek.


### <a name="add-a-new-sku"></a>Új SKU hozzáadása

A következő lépésekkel hozhatja elérhetővé egy új SKU-t a meglévő ajánlathoz: 

1.  Jelentkezzen be a [Cloud Partner Portalba](https://cloudpartner.azure.com/).

2.  Az **összes ajánlat** területen keresse meg a frissíteni kívánt ajánlatot.

3.  A **SKU** -i lapon kattintson az **új SKU hozzáadása** elemre, és adjon meg egy **SKU azonosítót** az előugró ablakban.

4.  Tegye közzé újra a virtuális gépet a [virtuális gépek Azure Marketplace-en való közzétételével foglalkozó](./cpp-publish-offer.md)cikkben részletezett módon.

5.  Kattintson a **Közzététel** gombra a munkafolyamat elindításához az új SKU közzétételéhez.


### <a name="update-offer-marketplace-metadata"></a>Ajánlat Marketplace-metaadatok frissítése

A következő lépésekkel frissítheti a piactér metaadatait – a vállalat nevét, emblémáit stb. – az ajánlathoz társítva: 

1.  Jelentkezzen be a [Cloud Partner Portalba](https://cloudpartner.azure.com/).

2.  Az **összes ajánlat** területen keresse meg a frissíteni kívánt ajánlatot.

3.  Goto a **piactér** lapon kövesse a [virtuális gép közzététele az Azure Marketplace](./cpp-publish-offer.md) -en a metaadatok módosításait ismertető cikk utasításait.

4.  Kattintson a **Közzététel** gombra a munkafolyamat elindításához a módosítások közzétételéhez.


### <a name="update-pricing-on-published-offers"></a>A közzétett ajánlatok díjszabásának frissítése

Az utólagos elszámolású ajánlat közzététele után nem növelheti közvetlenül az SKU díjszabását.  (Ugyanakkor létrehozhat egy új SKU-t ugyanabban az ajánlatban, törölheti a régi SKU-t, majd újból közzéteheti az ajánlatot az új ügyfelek számára.)  Ezzel szemben a közzétett ajánlatok árát a következő lépések segítségével csökkentheti:

1.  Jelentkezzen be a [Cloud Partner Portalba](https://cloudpartner.azure.com/).

2.  Az **összes ajánlat**területen keresse meg a frissítendő ajánlatot.

3.  Kattintson arra az SKU-ra, amelynek a díjszabását csökkenteni kívánja.

4.  Ha a díjszabást a 1x1 grafikus felhasználói felületén állította be, akkor az árat közvetlenül a felhasználói felületen módosíthatja. Ha a díjszabást Importálás/exportálás táblázaton keresztül állítja be, csak az importálási/exportálási funkció használatával csökkentheti az árakat.

3.  Kattintson a **Save** (Mentés) gombra.

4.  Kattintson a **Közzététel** gombra a munkafolyamat elindításához a módosítások közzétételéhez.

Az új csökkentett díjszabás az új ügyfelek számára jelenik meg, ha a webhelyen él.  Ez az új díj az alábbi módokon befolyásolja az ügyfeleket:

- Az új ügyfelek díját az új díjszabás alapján számítjuk fel. 
- A meglévő ügyfelek esetében az árak csökkenése visszamenőlegesen jelenik meg a számlázási ciklus elején, amely alatt a csökkenés érvénybe lép.
Ha már felszámolták azt a ciklust, amely alatt az árak csökkenése megtörtént, akkor a következő elszámolási időszakban visszatérítést kapnak, hogy lefedje a csökkentett árat.


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>Egyszerűsített pénznemek díjszabása

Szeptember 1 2018-ától kezdődően a portálon megjelenik egy **egyszerűsített valuta díjszabását** tartalmazó új szakasz. A Microsoft egyszerűsíti az Azure Marketplace üzletágat azáltal, hogy lehetővé teszi, hogy több kiszámítható díjszabást és gyűjteményt biztosítson ügyfeleinek szerte a világon. Ez a egyszerűsítés magában foglalja a pénznemek számának csökkentését, amelyekben ügyfeleinket számlázjuk.

Az új szakasz díjszabása ezekben az új pénznemekben történik.Miután az összes ügyfelet áttelepítette az új kiegyenlítési pénznemekre, az eredeti díjszabási szakasz kivonásra kerül, és csak az egyszerűsített pénznem díjszabása marad.

2018 november 1-ig az új árat kell megállapítania azon régiók esetében, amelyekben az elszámolás pénzneme módosul. Nem fogja tudni megemelni az olyan régiók árát, amelyekben az elszámolás pénzneme nem változik.

> [!NOTE] 
> Ha API-kat használ az ajánlat közzétételéhez, új szakaszt láthat az ajánlat JSON-fájlján belül. Ezt az ajánlat típusától függően a `virtualMachinePricingV2` vagy `monthlyPricingV2`az függvénynek kell megadnia. 

Ha bármilyen kérdése van a változással kapcsolatban, forduljon az [Azure Marketplace támogatási szolgálatához](../../support-azure-marketplace.md).


## <a name="compare-feature"></a>Szolgáltatás összehasonlítása

Ha módosításokat végez egy már közzétett ajánlaton, kihasználhatja az **összehasonlítás** funkciót az elvégzett módosítások naplózásához. A szolgáltatás használata:

1.  A szerkesztési folyamat bármely pontján kattintson az ajánlat **összehasonlítás** gombjára.

    ![Funkció összehasonlítása gomb](./media/publishvm_037.png)


2.  Megtekintheti a marketing-eszközök és-metaadatok egymás melletti verzióit.


## <a name="history-of-publishing-actions"></a>Közzétételi műveletek előzményei

A korábbi közzétételi tevékenységek megtekintéséhez kattintson az **Előzmények** elemre a Cloud Partner Portal bal oldali navigációs sávján. Itt megtekintheti az Azure Marketplace-ajánlatok élettartama során elvégzett időbélyeges műveleteket.  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

