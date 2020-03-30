---
title: Marketplace-ajánlatok frissítése | Azure Piactér
description: Ajánlatok frissítése az Azure és az AppSource piactereken a Cloud Partner Portal használatával
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: dsindona
ms.openlocfilehash: 101369a050770be3acd9534cef6229037fe1c366
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288512"
---
# <a name="update-azure-marketplace-and-appsource-offers"></a>Az Azure Piactér és az AppSource-ajánlatok frissítése

Az ajánlat közzétételét követően különféle frissítéseket alkalmazhat az ajánlatra.  A [Cloud Partner Portal](https://cloudpartner.azure.com/) segítséget nyújt az ajánlatok attribútumainak megfelelő módosításában, többek között a következőkben:

-  Új virtuálisgép-lemezkép- vagy csomagverzió hozzáadása meglévő termékváltozathoz
-  A termékváltozat által elérhető régiók módosítása
-  Új ska-k hozzáadása
-  A piactér metaadatainak frissítése az ajánlatokhoz vagy a ski-khez 
-  Az árak frissítése a felosztó-kifizett ajánlatokon

A portál olyan funkciókkal is rendelkezik, mint például a funkciók összehasonlítása és az ajánlatok funkcióinak előzményeinek megtekintése, amelyek segítenek a változások kezelésében.  Miután módosított egy ajánlatot vagy termékváltozatot, azt újra közzé kell tenni, mielőtt a módosítások "élővé" válnának.  Ez a cikk végigvezeti a piactéri ajánlat frissítésének különböző aspektusain.

## <a name="unpermitted-changes-to-an-offersku"></a>Az ajánlat/Termékváltozat nem engedélyezett módosításai

Vannak olyan attribútumok egy ajánlat vagy termékváltozat, amely nem módosítható, ha már közzé a piacon.  A megfelelő mezők le vannak tiltva a portál **Szerkesztő** lapján, például:  

- Ajánlatazonosító és közzétevőazonosítója
- Termékváltozat-azonosító 
- Meglévő sk-ek adatlemezeinek száma
- Meglévő sk-ek számlázási/licencmodell-változásai
- Verziócímkék, például:`1.0.1`


## <a name="common-update-operations"></a>Gyakori frissítési műveletek

A következő szakaszok bemutatják, hogyan hajthatja végre a legtöbb frissítési műveletet.  Ezek a műveletek nem érhetők el minden ajánlattípushoz.  Ezen műveletek bármelyikének elindításához be kell jelentkeznie a Cloud Partner Portalra.


### <a name="update-offer-contacts"></a>Ajánlati kapcsolatok frissítése

Az alábbi lépésekkel frissítheti az ajánlat támogatási kapcsolattartóit.
1. A **Minden ajánlat** lapon válassza ki az ajánlatot.
2. Válassza a **Névjegyalbum** lapot.
3. Válassza ki a **Mentés** gombot.
4. A **közzétételi** folyamat elindításához válassza a Közzététel lehetőséget.


### <a name="change-regions-an-offer-or-sku-is-available-in"></a>Az ajánlat vagy termékváltozat módosítása a régiókban

Idővel érdemes lehet az ajánlat/Termékváltozat elérhetővé tenni több régióban.
Másik lehetőségként előfordulhat, hogy egy adott régióban le szeretné állítani az ajánlat/Termékváltozat támogatását.
A módosítások végrehajtásához hajtsa végre az alábbi lépéseket.

1. A **Minden ajánlat** lapon keresse meg a frissíteni kívánt ajánlatot.

Az Azure Piactér ajánlataihoz:

1. Válassza a **SK-k** lapot.  Válassza ki a módosítani kívánt termékváltozatot.
1. Kattintson az **Országok kijelölése** gombra az **Ország/régió elérhetősége** mezőben.
1. A régió elérhetősége párbeszédpanelen adja hozzá vagy távolítsa el a termékváltozat régióit.

AppSource ajánlatokhoz:

1. Válassza a **Kirakat részletei** lapot.
1. A **Támogatott országok/régiók** címke mellett kattintson a **Támogatott országok/régiók**elemre. 
1. A támogatott országok/régiók párbeszédpanelen adja hozzá vagy távolítsa el az ajánlat régióit.

Bármelyik piactéren:

1. A **közzétételi** folyamat elindításához kattintson a Közzététel gombra. 

Ha egy termékváltozat elérhetővé válik egy új régióban, az adott régióra vonatkozó anameddig árakat adhat meg az **Export Árképzési adatok** funkción keresztül. Ha olyan régiót ad hozzá, amely korábban elérhető volt, nem frissítheti annak díjszabását, mert az árképzésmódosításanem engedélyezett.


### <a name="add-a-new-sku"></a>Új termékváltozat hozzáadása 

Ha egy új termékváltozatot szeretne elérhetővé tenni egy meglévő ajánlathoz, kövesse az alábbi lépéseket:

1. Az **Összes ajánlat** lapon keresse meg az ajánlatot.
3. A **Termékváltozatok** képernyőn kattintson az **Új termékváltozat hozzáadása** elemre, és adja meg a **termékváltozat-azonosítót** az előugró ablakban.
4. Kövesse a [virtuálisgép-ajánlat közzététele](../virtual-machine/cpp-publish-offer.md)című részben ismertetett további lépéseket.
5. A **közzétételi** folyamat elindításához kattintson a Közzététel gombra.


### <a name="update-offer-marketplace-assets"></a>Ajánlatpiac-eszközök frissítése

Előfordulhat, hogy olyan forgatókönyvek, ahol frissítenie kell a piactér szöveges és képeszközök, mint például a vállalati logók, ajánlat leírása, stb. Az eszközök frissítéséhez kövesse az alábbi lépéseket.

1. A **Minden ajánlat** oldalon keresse meg az ajánlatot. 
2. Válassza a **Piactér** lapot, és kövesse az ajánlat *Marketplace lapján* található utasításokat.
3. A **közzétételi** folyamat elindításához kattintson a Közzététel gombra.


### <a name="update-pricing-on-published-offers"></a>Közzétett ajánlatok árának frissítése

A felosztó-kiosztó ajánlat közzététele után nem növelheti egy meglévő termékváltozat árát.  Ehelyett hozzon létre egy termékváltozatot ugyanazon ajánlat alapján, törölje a régi termékváltozatot, majd tegye közzé újra az ajánlatot. Csökkentheti a korábban közzétett ajánlatok árát. Az ajánlatár csökkentése:

1. Válassza ki azt a termékváltozatot, amelynek díjszabását csökkenteni szeretné.
2. Az alacsonyabb árat az eredetileg használt mechanizmussal kell beállítania: vagy közvetlenül a portál felhasználói felületén, vagy az importálási/exportálási táblázatban.
3. Kattintson a **Mentés** gombra.
4. A **közzétételi** folyamat elindításához kattintson a Közzététel gombra.

Az árképzés látható az új ügyfelek számára, ha már él a piacon, és minden új ügyfél majd fizetni az új csökkentett árat.  A meglévő ügyfelek esetében az árcsökkenés visszamenőleges hatállyal tükröződik a számlázási ciklus kezdetére, amelynek során az árcsökkenés hatályba lépett.  Ha már kiszámlázták nekik azt a ciklust, amely alatt árcsökkenés történt, a következő számlázási ciklusuk során visszatérítést kapnak a csökkentett ár fedezésére.


## <a name="compare-feature"></a>Jellemző összehasonlítása

Ha módosításokat hajt végre egy közzétett ajánlaton, az *Összehasonlítás* funkcióval naplózhatja a módosításokat. A funkció kihasználása:

1. A szerkesztési folyamat bármely pontján az ajánlat **szerkesztőlapján** található **Összehasonlítás** gombra kattinthat.
2. Az összehasonlító ablak az ajánlat mentett módosításainak egymás melletti verzióit jeleníti meg a piactéri ajánlathoz képest. 

![Ajánlat összehasonlítása gomb a Szerkesztő lapon](./media/offer-compare-button.png)


## <a name="history-of-publishing-actions"></a>A közzétételi műveletek előzményei

A korábbi közzétételi tevékenység megtekintéséhez válassza az **Előzmények** lapot a Felhőpartner-portál bal függőleges menüsorán.  Az Előzmények lap rugalmas szűrést biztosít számos jellemző szerint, és támogatja az oszlopok rendezését.  Minden közzétételi esemény időbélyeggel van ellátva.  További információt a [Naplózási előzmények lap című témakörben](../portal-tour/cpp-history-page.md)talál.


## <a name="next-steps"></a>További lépések

A Felhőpartner-portál segítségével is [törölhet idát közzétett termékváltozatot vagy ajánlatot.](./cpp-delete-offer.md)
