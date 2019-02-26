---
title: Frissítse a Marketplace-ajánlatok – az Azure Marketplace-en |} A Microsoft Docs
description: Frissítés kínál az Azure-ra és az AppSource-piactér használatával a Cloud Partner portálra
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pbutlerm
ms.openlocfilehash: 690ce08aa15a9677b04931ed1965ef819614ee84
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355628"
---
# <a name="update-azure-marketplace-and-appsource-offers"></a>Az Azure Marketplace és AppSource ajánlatokat frissítése

Számos különböző típusú frissítéseket alkalmazhat az ajánlatot, hogy közzététele után.  A [Cloud Partner Portalon](https://cloudpartner.azure.com/) segítséget megfelelően módosítja az attribútumokat, egy ajánlatot, többek között:

-  Új virtuális gép (VM) rendszerképet vagy csomagot verzió hozzáadása egy meglévő Termékváltozat
-  Régiókban érhető el a Termékváltozat módosítása
-  Hozzáadás, új termékváltozatok
-  Marketplace-en metaadatainak a ajánlatok és termékváltozatok frissítése 
-  Kínál az utólagos elszámolású díjszabás frissítése

A portál is tartalmaz a szolgáltatások, például lehetővé teszi szolgáltatások összehasonlítása és a egy ajánlatot, amely segítséget nyújt a módosításával funkciói előzményeinek megtekintése.  Az ajánlat vagy a Termékváltozat módosítása után azt kell újra közzé mielőtt a módosítások "élő".  Ez a cikk végigvezeti a különböző aspektusait a marketplace-ajánlat frissítése.

## <a name="unpermitted-changes-to-an-offersku"></a>Egy ajánlat/SKU engedett módosításai

Vannak bizonyos attribútumainak ajánlat, amely nem módosítható, miután sikeresen közzétette a Marketplace-en.  A megfelelő mezőket le vannak tiltva a **szerkesztő** lapján a portálon, például:  

- Ajánlat azonosítója és a Gyártóazonosítóval
- SKU-AZONOSÍTÓJA 
- Adatlemez meglévő SKU-k száma
- Számlázás/licenc adatmodell változásainak a meglévő termékváltozatok
- Verzió címkék, például: `1.0.1`


## <a name="common-update-operations"></a>Gyakori frissítési műveletek

Az alábbi szakaszok azt ismertetik, hogyan hajtsa végre a legtöbb frissítési műveleteket.  Minden ajánlat esetében ezek a műveletek nem érhetők el.  Be kell jelentkeznie a Cloud Partner portálra, indítsa el ezeket a műveleteket.


### <a name="update-offer-contacts"></a>Az ajánlat ügyfelek frissítése

A következő lépések segítségével frissítse a támogatási kapcsolatba lép az ajánlatban.
1. Az a **összes kínál** lapra, jelölje be az ajánlat.
2. Válassza ki a **névjegyek** fülre. Frissítse a névjegyeket.
3. Válassza ki a **Mentés** gombot.
4. Válassza ki **közzététel** a közzétételi folyamat elindításához.


### <a name="change-regions-an-offer-or-sku-is-available-in"></a>Az ajánlat vagy Termékváltozat érhető el a régiók módosítása

Idővel előfordulhat, hogy szeretné az ajánlat/SKU több régióban elérhetővé.
Azt is megteheti érdemes leállítani az ajánlat/SKU támogatása egy adott régióban.
Ezek a módosítások végrehajtásához kövesse az alábbi lépéseket.

1. Az a **minden ajánlat** lapon, keresse meg az ajánlat, amelyet szeretne frissíteni.

Az Azure Marketplace-ajánlatok:

2. Válassza ki a **termékváltozatok** fülre.  Válassza ki a Termékváltozat módosítása.
3. Kattintson a **kiválasztása országok/régiók** gomb alatt a **ország/régió rendelkezésre állási** mező.
4. A régió rendelkezésre állási párbeszédpanelen régiók hozzáadása vagy eltávolítása a Ez a termékváltozat.

Az appsource-ban a következőket kínálja:

2. Válassza ki a **kirakat részletek** fülre.
3. Mellett a **támogatott országok/régiók** címkézését, kattintson a **támogatott országok/régiók**. 
4. Támogatott országok/régiók párbeszédpanelen régiók hozzáadása vagy eltávolítása a ezt az ajánlatot.

Mindkét Marketplace-en:

5. Kattintson a **közzététel** a közzétételi folyamat elindításához. 

A Termékváltozat egy új régióban elérhetővé van folyamatban, ha rendelkezik keresztül adott régió díjszabás megadásának lehetőségét a **díjszabási adatok exportálása** funkciót. Vissza egy régióban, amely korábban ad hozzá, ha nem tudja frissíteni a díjszabás, mert a díjszabási nem engedélyezettek.


### <a name="add-a-new-sku"></a>Adjon hozzá egy új Termékváltozatot 

Ahhoz, hogy egy új Termékváltozatban érhető el egy meglévő ajánlatot, használja az alábbi lépéseket:

1. Az a **minden ajánlat** lapon, keresse meg az ajánlat.
3. Alatt a **termékváltozatok** alkotnak, kattintson a **adja hozzá az új Termékváltozat** , és adja meg egy **SKU-azonosítója** az előugró.
4. Kövesse a részletes lépések a többi [egy virtuálisgép-ajánlat közzététele](../virtual-machine/cpp-publish-offer.md).
5. Kattintson a **közzététel** a közzétételi folyamat elindításához.


### <a name="update-offer-marketplace-assets"></a>Az ajánlat marketplace eszközök frissítése

Előfordulhat, hogy a forgatókönyvek, ahol frissítenie a piactéren, szöveges és lemezkép eszközök, például a vállalati emblémát ajánlat leírása, és így tovább. Ezek az eszközök frissítésére használni az alábbi lépéseket.

1. Az a **minden ajánlat** lapon, keresse meg az ajánlat. 
2. Válassza ki a **Marketplace** lapra, és kövesse az utasításokat az ajánlatban *piactér lapján* témakör.
3. Kattintson a **közzététel** a közzétételi folyamat elindításához.


### <a name="update-pricing-on-published-offers"></a>Frissítse a közzétett ajánlatok díjszabása

Az utólagos számlázású közzététele után egy meglévő Termékváltozat ára nem növelhető.  Ehelyett az ugyanazon az ajánlatnak a Termékváltozat létrehozása, törölje a régi Termékváltozat és majd közzé az ajánlatot még. A díjak a már közzétett ajánlatok csökkenthető. Csökkentheti az ajánlat ár:

1. Válassza ki az SKU-t, amelynek díjszabása csökkentése érdekében.
2. Az alacsonyabb díj ellenében a regisztráláshoz használt eredeti ugyanazt a mechanizmust kell állítani: közvetlenül a portál felhasználói Felületét vagy az importálási/exportálási táblázatkezelő.
3. Kattintson a **Save** (Mentés) gombra.
4. Kattintson a **közzététel** a közzétételi folyamat elindításához.

A díjszabás akkor látható, az új ügyfelek számára, élő a marketplace-en, és új ügyfelek lesz majd kezdem meg új csökkent.  A meglévő ügyfelek az árcsökkenést tükrözi visszamenőlegesen a Start a számlázási ciklus során, ami az árcsökkenést hatékony vált.  Ha rendelkezik már lett számlázásra a ciklus során, ami árcsökkenés történt, visszatérítés kapja meg, hogy biztosítsák a csökkent díj a következő elszámolási időszakban.


## <a name="compare-feature"></a>A szolgáltatás összehasonlítása

Ha módosít egy közzétett kínálnak, használhatja a *összehasonlítása* funkció a módosítások naplózását. Ez a funkció használatához:

1. A szerkesztési folyamat bármely mozzanata, kattintson a **összehasonlítása** gombra a **szerkesztő** az ajánlatban fülre.
2. Összehasonlító ablak a mentett módosítások verzióinak egymás mellett, mint a korábban megszokott a marketplace-ajánlat az ajánlat jeleníti meg. 

![Az ajánlat gombot szerkesztő lapon összehasonlítása](./media/offer-compare-button.png)


## <a name="history-of-publishing-actions"></a>Közzétételi műveletek előzményei

Közzétételi végzett tevékenység előzményeit megtekintéséhez jelölje ki a **előzmények** a Cloud Partner portálra, a bal oldali függőleges menüsávon lapján.  Az Előzmények lapon több jellemzők alapján történő szűrést rugalmas és oszlopok rendezése is támogat.  Minden közzétételi esemény időbélyegzővel.  További információkért lásd: [naplózási Előzmények lapon](../portal-tour/cpp-history-page.md).


## <a name="next-steps"></a>További lépések

Használhatja a Cloud Partner portálra való [közzétett Termékváltozat törlése vagy ajánlat](./cpp-delete-offer.md).
