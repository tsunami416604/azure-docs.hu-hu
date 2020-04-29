---
title: Piactéri ajánlatok frissítése | Azure piactér
description: Az Azure-és AppSource-piactéren elérhető frissítési ajánlatok a Cloud Partner Portal használatával
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: dsindona
ms.openlocfilehash: 101369a050770be3acd9534cef6229037fe1c366
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80288512"
---
# <a name="update-azure-marketplace-and-appsource-offers"></a>Azure Marketplace-és AppSource-ajánlatok frissítése

A közzétételt követően különböző típusú frissítések is alkalmazhatók az ajánlatra.  A [Cloud Partner Portal](https://cloudpartner.azure.com/) segítséget nyújt az ajánlat attribútumainak megfelelő módosításához, beleértve a következőket:

-  Új virtuális gép (VM) vagy csomag verziójának hozzáadása meglévő SKU-hoz
-  Megváltoztathatja az SKU-ban elérhető régiókat
-  Új SKU-i hozzáadása
-  A Piactéri metaadatok frissítése az ajánlatokhoz vagy az SKU-hoz 
-  Az utólagos elszámolású ajánlatok díjszabásának frissítése

A portál olyan funkciókkal is rendelkezik, mint például a funkciók összehasonlítása, valamint az ajánlatok előzményeinek megtekintése, amely segítséget nyújt a módosítások kezelésében.  Egy ajánlat vagy SKU módosítása után újból közzé kell tenni a módosításokat, mielőtt a módosítások "élő" állapotba kerülnek.  Ez a cikk végigvezeti a Piactéri ajánlat frissítésének különböző szempontjain.

## <a name="unpermitted-changes-to-an-offersku"></a>Egy ajánlat/SKU nem engedélyezett módosításai

Egy ajánlat vagy SKU néhány attribútuma nem módosítható, ha közzé lett téve a piactéren.  A megfelelő mezők le vannak tiltva a portál **szerkesztő** lapján, például:  

- Ajánlat azonosítója és közzétevő azonosítója
- SKU-AZONOSÍTÓ 
- Meglévő SKU-i adatlemezek száma
- A meglévő SKU-i számlázási/licencelési modell változásai
- Verziók címkéi, például:`1.0.1`


## <a name="common-update-operations"></a>Gyakori frissítési műveletek

A következő szakasz ismerteti, hogyan végezheti el a legtöbb frissítési műveletet.  Ezek a műveletek nem érhetők el az összes ajánlat típusához.  A műveletek elindításához be kell jelentkeznie a Cloud Partner Portalba.


### <a name="update-offer-contacts"></a>Ajánlati névjegyek frissítése

Az alábbi lépésekkel frissítheti az ajánlat támogatási partnereit.
1. A **minden ajánlat** lapon válassza ki az ajánlatot.
2. Válassza a **névjegyek** lapot. frissítse a névjegyeket.
3. Válassza ki a **Mentés** gombot.
4. A közzétételi folyamat elindításához válassza a **Közzététel** lehetőséget.


### <a name="change-regions-an-offer-or-sku-is-available-in"></a>Megváltoztathatja a régiókban elérhető ajánlatot vagy SKU-t

Idővel előfordulhat, hogy az ajánlatot/SKU-t több régióban is elérhetővé kívánja tenni.
Azt is megteheti, hogy le szeretné állítani az ajánlat/SKU támogatását egy adott régióban.
A módosítások végrehajtásához kövesse az alábbi lépéseket.

1. A **minden ajánlat** oldalon keresse meg a frissíteni kívánt ajánlatot.

Azure Marketplace-ajánlatok:

1. Válassza a **SKUs** fület.  Válassza ki a módosítandó SKU-t.
1. Az **ország/régió rendelkezésre állása** mezőben kattintson az **országok kiválasztása** gombra.
1. A régió elérhetősége párbeszédpanelen adja hozzá vagy távolítsa el az adott SKU régióit.

AppSource-ajánlatok:

1. Válassza ki a **kirakat részletei** lapot.
1. A **támogatott országok/régiók** felirat mellett kattintson a **támogatott országok/régiók**elemre. 
1. A támogatott országok/régiók párbeszédpanelen adja hozzá vagy távolítsa el az ajánlathoz tartozó régiókat.

Bármelyik piactér esetében:

1. A közzétételi folyamat elindításához kattintson a **Közzététel** gombra. 

Ha egy SKU-t egy új régióban kíván elérhetővé tenni, lehetősége van az adott régió díjszabásának megadására az **exportálási díjszabási adatok** funkcióján keresztül. Ha korábban már elérhető régiót ad hozzá, a díjszabás nem frissíthető, mert az árképzési változások nem engedélyezettek.


### <a name="add-a-new-sku"></a>Új SKU hozzáadása 

Ha új SKU-t szeretne elérhetővé tenni egy meglévő ajánlathoz, kövesse az alábbi lépéseket:

1. A **minden ajánlat** oldalon keresse meg az ajánlatot.
3. Az **SKU** -i űrlap alatt kattintson az **új SKU hozzáadása** elemre, és adjon meg egy **SKU azonosítót** az előugró ablakban.
4. Kövesse a [virtuális gépekre vonatkozó ajánlat közzétételének](../virtual-machine/cpp-publish-offer.md)részletes lépéseit.
5. A közzétételi folyamat elindításához kattintson a **Közzététel** gombra.


### <a name="update-offer-marketplace-assets"></a>Frissítési ajánlat Marketplace-eszközök

Lehetnek olyan helyzetek, amikor frissítenie kell a piactér szöveg-és képobjektumait, például a céges emblémákat, az ajánlat leírását stb. Az alábbi lépéseket követve frissítheti ezeket az eszközöket.

1. A **minden ajánlat** oldalon keresse meg az ajánlatát. 
2. Válassza a **piactér** fület, és kövesse az ajánlat *Marketplace lapján* található útmutatást.
3. A közzétételi folyamat elindításához kattintson a **Közzététel** gombra.


### <a name="update-pricing-on-published-offers"></a>A közzétett ajánlatok díjszabásának frissítése

Az utólagos elszámolású ajánlat közzététele után nem növelheti egy meglévő SKU árát.  Ehelyett hozzon létre egy SKU-t ugyanabban az ajánlatban, törölje a régi SKU-t, majd tegye közzé újból az ajánlatot. Csökkentheti a korábban közzétett ajánlatok árát. Az ajánlat árának csökkentése:

1. Válassza ki azt az SKU-t, amelynek a díjszabását csökkenteni kívánja.
2. Az alacsonyabb árat ugyanazt a mechanizmust kell megadnia, amelyet eredetileg használt: vagy közvetlenül a portál felhasználói felületén vagy az import/export táblázattal.
3. Kattintson a **Save** (Mentés) gombra.
4. A közzétételi folyamat elindításához kattintson a **Közzététel** gombra.

A díjszabás az új ügyfelek számára látható, ha a piactéren él, és minden új ügyfél az új, csökkentett árat fogja kifizetni.  A meglévő ügyfelek esetében az árak csökkenése visszamenőlegesen jelenik meg a számlázási ciklus elején, amely alatt a csökkenés érvénybe lép.  Ha már felszámolták azt a ciklust, amely alatt az árak csökkenése megtörtént, akkor a következő elszámolási időszakban visszatérítést kapnak, hogy lefedje a csökkentett árat.


## <a name="compare-feature"></a>Szolgáltatás összehasonlítása

Ha módosításokat hajt végre egy közzétett ajánlaton, az *összehasonlítás* funkció használatával naplózhatja a módosításokat. A funkció kihasználása:

1. A szerkesztési folyamat bármely pontján az ajánlat **szerkesztő** lapján az **összehasonlítás** gombra kattinthat.
2. Az összehasonlítási ablak a Piactéri ajánlathoz képest a mentett módosítások párhuzamos verzióit jeleníti meg az ajánlatban. 

![Az ajánlat összehasonlítása gomb a szerkesztő lapon](./media/offer-compare-button.png)


## <a name="history-of-publishing-actions"></a>Közzétételi műveletek előzményei

A korábbi közzétételi tevékenységek megtekintéséhez válassza a Cloud Partner Portal bal oldali függőleges menüsorának **Előzmények** lapját.  Az Előzmények oldalon számos jellemző rugalmas szűrést tesz lehetővé, és támogatja az oszlopok rendezését.  Az egyes közzétételi események időbélyegzővel vannak ellátottak.  További információ: [naplózási Előzmények lap](../portal-tour/cpp-history-page.md).


## <a name="next-steps"></a>További lépések

A Cloud Partner Portal [egy közzétett SKU vagy ajánlat törlésére](./cpp-delete-offer.md)is használhatja.
