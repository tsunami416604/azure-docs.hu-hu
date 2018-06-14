---
title: Az ajánlat telepítése az Azure piactéren |} Microsoft Docs
description: További információk, és végezze el az utasításokat az ajánlatot--telepítendő virtuálisgép-lemezkép, fejlesztői szolgáltatás, adatszolgáltatás, stb. – az Azure piactéren.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 8f79b891-84e2-4f41-ba0d-66420e2c6b2e
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2016
ms.author: mbaldwin
ms.openlocfilehash: 8df7b0e49e17612743b02596e99f7d1fbe8c6803
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
ms.locfileid: "29943241"
---
# <a name="deploy-your-offer-to-the-azure-marketplace"></a>Az ajánlat telepítése az Azure piactéren
Ha elégedett az ajánlatot (Ez azt jelenti, amelyeket tesztelt forgatókönyvet, marketing tartalom stb.), és készen áll indítása, a kérelem **éles leküldése** a a **közzététel** lapon.  

1. A forgatókönyv szerint négy lépést a közzétételi lap portal befejeződött, és a zöld legyen. A virtuális gép ajánlatok esetén győződjön meg arról, hogy betartják-e az alábbi útmutatást.
   
    ![rajz][img-pubportal-walkthru-checked]
2. Válassza ki a **közzététel** lap bal oldalán a listából.
   
    ![rajz][img-pubportal-menu-publish]
3. Kattintson a gombra **üzemi leküldéses jóváhagyás-igénylést**. A kérelem, ha a jóváhagyási team végrehajtja a végső áttekintése, és majd ajánlatát lesz elérhető az Azure piactéren.
   
    ![rajz][img-pubportal-publish-pushproduction]

> [!IMPORTANT]
> Virtuális gépek esetén a kérelem jóváhagyása üzemi környezetben, leküldéses gombra kattintva az alábbi lépéseket kell végrehajtani a leképezni kívánt jelenetben mögött. A közzétételi tekintse meg a folyamatban van a közzététel lapon minden egyes lépést tudnak portálon. Ellenőrizze ezen a lapon rendszeres időközönként (mindaddig, amíg a állapota: "Felsorolt") a átfogóan javítási igénylő hiba információkat.
> 
> * Először a termelési kérelem ellenőrzése a virtuális merevlemez hitelesítő csoport ugrik. Azonban ha már felsorolt ajánlatát frissít, és a kérelem rendelkezik azt csak a módosítás marketing, majd a hitelesítésszolgáltató a lépés kimarad.
> * A következő lépésben a kérelem ellenőrzése az ajánlat marketing tartalmának tartalomérvényesítési csoport tudomást.
> * Ha a fenti lépések sikeres, majd az ajánlat jóváhagyja éles környezetben. Ilyenkor állapota lesz "szereplő" a közzétételi portáljára. Azonban az "Felsorolt" állapot nem feltétlenül jelenti azt, hogy a folyamat be nem fejeződik. Az alábbi lépéseket kell teljes ajánlatot csak az Azure piactéren.
> * Az ajánlat jóváhagyja a fenti lépést az éles környezetben, ha az ajánlat replikációs között az Azure adatközpontjaiban indítsa el. Általában 24-48hours a replikálás befejezéséhez szükséges, de a hét attól függően, hogy a virtuális merevlemez méretére is tarthat. Azonban ha már felsorolt ajánlatát frissít, és rendelkezik lett, csak a módosítás marketing, akkor a replikáció értéke gyorsabb.
> * Ha a replikálás befejeződik, majd az ajánlat lesz elérhető az Azure piactéren.
> 
> Törölheti a ajánlat mindig le egy **vázlat** állapota (azaz, soha nem **átmeneti leküldése** vagy **éles leküldése**). Az a **előzmények** lapra, majd a **vázlat elvetése** gomb tervezet törli a lap alján.
> 
> 

## <a name="production-checklist-for-all-virtual-machine-offers"></a>Minden virtuális gép ajánlatok éles ellenőrzőlistája
* Győződjön meg arról, hogy-e a Microsoft Azure Certified partneren
* Az SKU lapon a beállítást "elrejtése e SKU a piactérről, mert azt kell mindig vásárolható keresztül a megoldássablon" állapotúként kell megjelölni Igen csak akkor, ha a Termékváltozat része a Megoldássablon. Minden más esetben ez a beállítás mindig állapotúként kell megjelölni nem.
* Ne feledje: Nem kell módosítani a Termékváltozat láthatósági beállítása után a Termékváltozat szerepel. Ez a funkció nem támogatott.
* Győződjön meg arról, hogy a emblémák az Azure piactér emblémáinak használatáról az alábbi igazodik.
* Ajánlat és SKU leírása nem lehet azonos.
* SKU a Title és kínálnak hosszú összefoglaló nem lehet azonos.
* Termékváltozat-cím és kínálnak összegzése nem lehet azonos.
* Termékváltozat-címek nem lehet azonos az ajánlatot több SKU.

**Az Azure piactér emblémáinak használatáról**

* Az Azure terv egy egyszerű színpaletta rendelkezik. Továbbra is a számát az elsődleges és másodlagos színt a az embléma alacsony.
* Az Azure portál a témának színeket fehér és fekete. Ezért kerülje ezeket a színeket, a emblémák hátterének színét. Néhány színt, amely az Azure portálon jól láthatóan elhelyezett tenné a emblémák használja. Azt javasoljuk, hogy egyszerű alapszínek. Ha átlátszó háttérrel használ, akkor győződjön meg arról, hogy az embléma/szöveg nincs fehér vagy fekete.
* Ne használjon egy színátmenetes hátterének az emblémának.
* Ne helyezzen szöveg, még akkor is a vállalat vagy a márka neve, az embléma.
* Az embléma megjelenését és működését "egyszerű" kell, és átmenetek kerülendő.
* Az embléma nem kell felhőbe.

**A kiemelt embléma irányelveket:**

* A kiemelt embléma nem kötelező megadni. A közzétevő nem szeretné kiemelt embléma feltöltéséhez. **Azonban egyszer feltöltött hero ikon nem lehet törölni a közzétételi portálon. Ekkor a partner kell útmutatást Azure piactér kiemelt ikonok más ajánlatot nem hagyható jóvá üzemi környezetben.**
* A közzétevő megjelenített név, a Termékváltozat jogcímre és a hosszú összefoglalás ajánlat fehér betűszíne jelenik meg. Ezért ne bármely könnyű szín megőrzi a kiemelt ikon a háttérben. Fekete, fehér és átlátható háttér nem engedélyezett hőse ikonok.
* A közzétevő nevét, a Termékváltozat-cím, a hosszú összefoglalás ajánlat megjelenítése és a Létrehozás gombra vannak beágyazott programozott módon belül a kiemelt embléma után az ajánlat felsorolt kerül. Ezért kell meg szöveget a kiemelt embléma tervezésekor. Ne változtassa meg üres területet a jobb oldalon, mert a szöveg (pl. közzétevő megjelenített név, SKU-cím, hosszú összefoglalás ajánlat) lesznek a tagjai programozott módon lépünk Önnel ott keresztül. A szöveg üres területet kell lennie a jobb oldali 415 x 100 (és a balról 370px ellensúlyozza).

## <a name="additional-production-checklist-for-already-listed-virtual-machine-offers"></a>Kínál már listán szereplő virtuális gép további éles ellenőrzőlistája
* Ellenőrizze, hogy már a vállalat ajánlat néven ajánlatot. Ha igen, majd adja hozzá a Termékváltozat egy új verziója olyan új ismétlődő ajánlat létrehozása helyett a meglévő lehetőség.
* Adatlemez nem szükséges módosítani a azonos Termékváltozat két verziója közötti.
* Az Azure piactéren nem támogatja a felsorolt SKU árképzési megváltoztatását, az hatással van a meglévő ügyfeleknek számlázási. Győződjön meg arról, hogy nem módosítja a régiókban, amennyiben rendelkezésre áll-e a Termékváltozat felsorolt termékváltozatok árképzési. Azonban új termékváltozatok hozzáadása vagy új régiók hozzáadása egy meglévő Termékváltozat.

## <a name="next-steps"></a>További lépések
Az ajánlat élő kerül, ha tesztelje a forgatókönyvet, hogy a szerződések és -funkcióinak megfelelő működéséhez az éles környezetben, mint az összes tesztelése, és az átmeneti érvényesíthetők ellenőrzése.

## <a name="see-also"></a>Lásd még
* [Első lépések: az ajánlat közzététele az Azure piactéren](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]:media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]:media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]:media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png
