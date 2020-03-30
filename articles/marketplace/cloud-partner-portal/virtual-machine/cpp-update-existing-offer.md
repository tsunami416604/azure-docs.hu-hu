---
title: Meglévő virtuálisgép-ajánlat frissítése az Azure Piactéren
description: Bemutatja, hogyan frissítheti a meglévő virtuális gép ajánlat az Azure Marketplace-en.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: dsindona
ms.openlocfilehash: fe1397d7ad00a2eb6e9fe04d8bfa50e49839fe34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288784"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>Meglévő virtuálisgép-ajánlat frissítése az Azure Marketplace-en

Ez a cikk végigvezeti a virtuális gép (VM) ajánlatfrissítésének különböző szempontjait a [Cloud Partner Portalon,](https://cloudpartner.azure.com/) majd az ajánlat újbóli közzétételét. 

Számos általános oka van annak, hogy frissítse az ajánlatot, többek között:

-  Új virtuálisgép-lemezkép-verzió hozzáadása a meglévő változatverzióhoz
-  Régiók módosítása termékváltozat érhető el
-  Új ska hozzáadása
-  Az ajánlat vagy az egyes sk-ek piactéri metaadatainak frissítése
-  Díjszabás frissítése a felosztó-kifizethető ajánlatokon

A módosítások segítése érdekében a portál az **Összehasonlítás** és az **Előzmények** funkciókat kínálja.  

>[!Note]
>A felhőszolgáltatók (CSP) partnercsatorna-opt-in már elérhető.  Az ajánlat microsoftos CSP-partnercsatornákon keresztül történő marketingről további információt a [felhőszolgáltatók](../../cloud-solution-providers.md) ban talál.

## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>A virtuális gép ajánlatának vagy termékváltozatának nem engedélyezett módosításai

A virtuális gép ajánlatának vagy termékváltozatának vannak olyan attribútumai, amelyek nem módosíthatók, ha az ajánlat az Azure Piactéren való él, főként:

-  Az ajánlat **ajánlatazonosítója** és **közzétevőazonosítója**
-  **Meglévő** termékváltozatok termékazonosítója
-  Meglévő sk-ek adatlemezeinek száma
-  A meglévő sk-ek számlázási/licencmodell-módosításai
-  Áremelések egy közzétett termékváltozathoz


## <a name="common-update-operations"></a>Gyakori frissítési műveletek

Bár a virtuális gép ajánlata számos jellemzőt módosíthat, a következő műveletek gyakoriak.

### <a name="update-the-vm-image-version-for-a-sku"></a>Termékváltozat virtuális géplemez-verziójának frissítése

Gyakori, hogy a virtuális gép lemezképét rendszeresen frissíteni kell a biztonsági javításokkal, a további szolgáltatásokkal és így tovább.  Ilyen esetekben a következő lépésekkel szeretné frissíteni a termékváltozat által hivatkozott virtuális géplemez-lemezképet:

1.  Jelentkezzen be a [Cloud Partner Portal ba.](https://cloudpartner.azure.com/)

2.  A **Minden ajánlat területen**keresse meg a frissítendő ajánlatot.

3.  A **Termékváltozatok** lapon kattintson a frissítéshez a virtuális gép lemezképéhez társított termékváltozatra.

4.  A **Lemezverzió csoportban**kattintson a **+Új lemezverzió** elemre egy új virtuálisgép-lemez kép hozzáadásához.

5.  Adja meg az új VM Images **Disk verziót**. A lemezverziónak a [szemantikai verzióformátumot](https://semver.org/) kell követnie. A verziók x.y.z formátumúak, ahol X, Y és Z egész számok. Ellenőrizze, hogy a megadott új verzió nagyobb-e, mint az összes korábbi verzió; ellenkező esetben az új verzió újbóli közzététele után nem jelenik meg sem a portálon, sem az Azure Marketplace-en.

6.  Az **operációs rendszer virtuális merevlemezének URL-címéhez**adja meg a virtuális merevlemez operációs rendszerhez létrehozott megosztott [hozzáférésű aláírási (SAS)](./cpp-get-sas-uri.md) URI-t. 

    > [!WARNING] 
    > Az adatlemezek száma nem módosítható a termékváltozat különböző verziói között. Ha a korábbi verziókban adatlemezek voltak konfigurálva, az új verziónak azonos számú adatlemezzel kell rendelkeznie.

7.  Kattintson a **Közzététel** gombra az új virtuálisgép-verzió azure Piactéren való közzétételéhez a közzététel gombra.


### <a name="change-region-availability-of-a-sku"></a>Termékváltozat régióelérhetőségének módosítása

Idővel érdemes lehet az ajánlat/Termékváltozat elérhetővé tenni több régióban.  Másik lehetőségként előfordulhat, hogy egy adott régióban le szeretné állítani az ajánlat/Termékváltozat támogatását.
Az elérhetőség módosításához kövesse az alábbi lépéseket:

1.  Jelentkezzen be a [Cloud Partner Portal ba.](https://cloudpartner.azure.com/)

2.  Az **Összes ajánlat** alatt találja meg a frissíteni kívánt ajánlatot.

3.  A **Termékváltozatok** lapon kattintson arra a termékváltozatra, amelyet módosítani szeretne a rendelkezésre állása.

4.  Kattintson az **Országok kiválasztása** gombra az **Ország/régió elérhetősége** mezőben.

5.  A régióban rendelkezésre állási előugró ablakban adja hozzá vagy távolítsa el a régiókat ehhez a termékváltozathoz.

6.  Kattintson a **Közzététel** gombra a közzétételi munkafolyamat elindításához a termékalkalmazás-régió elérhetőségének frissítéséhez.

Ha egy termékváltozat elérhetővé válik egy új régióban, az adott régióra vonatkozó anameddig árakat adhat meg az **Export Árképzési adatok** funkción keresztül. Ha olyan régiót ad hozzá, amely korábban már elérhető volt, nem fogja tudni frissíteni az árképzést, mert az árképzésmódosításanem engedélyezett.


### <a name="add-a-new-sku"></a>Új termékváltozat hozzáadása

Az alábbi lépésekkel elérhetővé tehet egy új termékváltozatot a meglévő ajánlathoz: 

1.  Jelentkezzen be a [Cloud Partner Portal ba.](https://cloudpartner.azure.com/)

2.  Az **Összes ajánlat** alatt találja meg a frissíteni kívánt ajánlatot.

3.  A **Termékváltozatok** lapon kattintson az **Új termékváltozat hozzáadása** elemre, és adja meg a **termékváltozat-azonosítót** az előugró ablakban.

4.  Tegye közzé újra a virtuális gépet a virtuális gép közzététele az [Azure Piactéren](./cpp-publish-offer.md)című cikkben részletezett módon.

5.  Kattintson a **Közzététel** gombra az új termékváltozat közzétételéhez.


### <a name="update-offer-marketplace-metadata"></a>Ajánlatpiactér metaadatainak frissítése

Az alábbi lépésekkel frissítheti az ajánlathoz társított piactér metaadatait – a vállalat nevét, az emblémákat stb.: 

1.  Jelentkezzen be a [Cloud Partner Portal ba.](https://cloudpartner.azure.com/)

2.  Az **Összes ajánlat** alatt találja meg a frissíteni kívánt ajánlatot.

3.  Nyissa meg a **Piactér** fülre, majd kövesse a cikkben található [utasításokat Közzététele egy virtuális gép az Azure Piactéren](./cpp-publish-offer.md) a metaadatok módosításához.

4.  A módosítások közzétételéhez kattintson a **Közzététel** gombra a munkafolyamat elindításához.


### <a name="update-pricing-on-published-offers"></a>Közzétett ajánlatok díjszabásának frissítése

A felosztó-kiosztó ajánlat közzététele után nem növelheti közvetlenül a termékváltozat díjszabását.  (Azonban létrehozhat egy új termékváltozatot ugyanazzal az ajánlattal, törölheti a régi termékváltozatot, majd újra közzéteheti az ajánlatot az új ügyfelek számára.)  Ezzel szemben a közzétett ajánlat árát a következő lépésekkel csökkentheti:

1.  Jelentkezzen be a [Cloud Partner Portal ba.](https://cloudpartner.azure.com/)

2.  A **Minden ajánlat területen**keresse meg a frissítendő ajánlatot.

3.  Kattintson arra a termékváltozatra, amelynek díjszabását csökkenteni szeretné.

4.  Ha az 1x1 grafikus felhasználói felületen állította be az árképzést, az árat közvetlenül a felhasználói felületen módosíthatja. Ha az árképzést importálási/exportálási táblázaton keresztül állítja be, akkor csak az importálási/exportálási funkcióval csökkentheti az árakat.

3.  Kattintson a **Mentés** gombra.

4.  A módosítások közzétételéhez kattintson a **Közzététel** gombra a munkafolyamat elindításához.

Az új csökkentett árak lesz látható az új ügyfelek, ha él a honlapon.  Ez az új ár a következő módokon érinti az ügyfeleket:

- Az új ügyfeleknek ezt az új díjat számítjuk fel. 
- A meglévő ügyfelek esetében az árcsökkenés visszamenőleges hatállyal tükröződik a számlázási ciklus kezdetére, amelynek során az árcsökkenés hatályba lépett.
Ha már kiszámlázták nekik azt a ciklust, amely alatt árcsökkenés történt, a következő számlázási ciklusuk során visszatérítést kapnak a csökkentett ár fedezésére.


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>Egyszerűsített valutaárképzés

2018. szeptember 1-jétől egy egyszerűsített **valutaárképzés** nevű új szakasz kerül a portálra. A Microsoft egyszerűsíti az Azure Marketplace-üzletágat azáltal, hogy világszerte kiszámíthatóbb árakat és gyűjteményeket engedélyez ügyfeleitől. Ez a racionalizálás magában foglalja azoknak a pénznemekszámnak a csökkentését, amelyekben az ügyfeleket számlázunk.

Az új szakasz az új pénznemekben fog árazást tartani.Miután az összes vevőt áttelepítették ezekre az új kiegyenlítési pénznemekbe, az eredeti árképzési szakasz megszűnik, és csak az Egyszerűsített pénznemárképzés szakasz marad meg.

2018. november 1-ig új árat állíthat be azon régiók számára, amelyekben az elszámolás pénzneme változik. Nem fogja tudni növelni az árat azokban a régiókban, ahol az elszámolás pénzneme nem változik.

> [!NOTE] 
> Ha API-kat használ az ajánlat közzétételéhez, előfordulhat, hogy egy új szakasz jelenik meg az ajánlat JSON-on belül. Ez az `virtualMachinePricingV2` ajánlat típusától `monthlyPricingV2`függően vagy , vagy , jegyzeteket kapna. 

Ha bármilyen kérdése van ezzel a változással kapcsolatban, forduljon az [Azure Piactér támogatási szolgálatához.](../../support-azure-marketplace.md)


## <a name="compare-feature"></a>Jellemző összehasonlítása

Ha módosítja a már közzétett ajánlatot, az **Összehasonlítás** funkcióval naplózhatja a végrehajtott módosításokat. A funkció használata:

1.  A szerkesztési folyamat bármely pontján kattintson az **ajánlat Összehasonlítás** gombjára.

    ![Jellemző összehasonlítása gomb](./media/publishvm_037.png)


2.  A marketingeszközök és a metaadatok egymás melletti verzióinak megtekintése.


## <a name="history-of-publishing-actions"></a>Közzétételi műveletek előzményei

Bármely korábbi közzétételi tevékenység megtekintéséhez kattintson a Cloud Partner Portal bal oldali navigációs menüjének **Előzmények** elemére. Itt megtekintheti az Azure Marketplace-ajánlatok élettartama során végrehajtott időbélyeggel ellátott műveleteket.  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

