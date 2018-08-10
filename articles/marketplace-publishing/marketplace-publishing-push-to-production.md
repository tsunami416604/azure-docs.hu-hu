---
title: Üzembe helyezni az ajánlatot az Azure piactér |} A Microsoft Docs
description: Ismerje meg, és haladjon végig az utasításokat követve üzembe helyezni az ajánlatot – virtuálisgép-lemezkép, fejlesztői szolgáltatás, adatszolgáltatás, stb. – az Azure piactéren.
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: 8f79b891-84e2-4f41-ba0d-66420e2c6b2e
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2016
ms.author: hascipio
ms.openlocfilehash: 12dc81642905cd9449a1032c7ab57298e6b69ba8
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714281"
---
# <a name="deploy-your-offer-to-the-azure-marketplace"></a>Üzembe helyezni az ajánlatot az Azure piactéren
Ha elégedett az előfizetési csomag keretében (azaz tesztelt forgatókönyvet, marketing-tartalmat, stb.) indítsa el, a kérés készen áll, és **továbbítsa az éles** a a **közzététel** lapon.  

1. A forgatókönyv szerint az négy lépést lapját a közzétételi portálon kell lennie, zöld és befejeződött. Virtuálisgép-ajánlat esetén győződjön meg arról, hogy teljesülnek-e az alábbi útmutatást.
   
    ![rajz][img-pubportal-walkthru-checked]
2. Válassza ki a **közzététel** lap bal oldalán lévő listából.
   
    ![rajz][img-pubportal-menu-publish]
3. A gombra kattintva **jóváhagyást az éles környezetben való üzembe helyezésre**. A kérelmet, miután a jóváhagyó csapat végrehajtja a végső lektorálásra, és ezután az ajánlat lesz elérhető az Azure Marketplace-en.
   
    ![rajz][img-pubportal-publish-pushproduction]

> [!IMPORTANT]
> Virtuális gépek esetén a kérés jóváhagyást az éles környezetben való üzembe helyezésre gombra kattintás után az alábbi lépéseket történik a jelenet mögött. Lesz a közzététel lapon az egyes lépések állapotának megtekintése a közzétételi portálon. Ellenőrizze ezen a lapon rendszeres időközönként (mindaddig, amíg az állapota "Szerepel a listában") az end végpontjától javítás igénylő hiba információkért.
> 
> * Először a termelési kérelem kerül a minősítési csapatot, amely a virtuális merevlemez ellenőrzése. Azonban ha már felsorolt ajánlat frissítése folyamatban van, és a kérés csak marketing-módosítás van itt, majd a minősítési lépésben a rendszer kihagyta.
> * A tartalomérvényesítés csapatot, amely az ajánlat marketing tartalmának ellenőrzése a következő lépésben a kérelem jár.
> * Ha a fenti lépéseket a sikeres, majd az ajánlatra jóvá van hagyva éles környezetben. Jelenleg az állapot "közzétételének" a közzétételi portálon. Azonban az "Szerepel a listában" állapot nem jelenti azt, hogy a folyamat befejeződött. Az alábbi lépéseket kell befejezése előtt az Azure piactéren az ajánlat érhető el.
> * Az ajánlat jóváhagyja a fenti lépést az éles környezetben, miután az ajánlat replikációs indítsa el az összes Azure-adatközpontok között. Általában 24-48hours a replikálás befejezéséhez szükséges, de is igénybe vehet egy héttel a vhd méretétől függően. Ha viszont már felsorolt ajánlat frissítése folyamatban van, és csak marketing-módosítás van itt, majd a replikálást, gyorsabb.
> * Ha a replikálás is befejeződik, majd az ajánlat lesz elérhető az Azure piactéren.
> 
> Mindig törölheti az ajánlat bár egy **Draft** állapota (azaz soha nem **az átmeneti üzem** vagy **éles leküldése**). Az a **előzmények** lapra, majd a **elveti a draft** gomb törölni a tervezet az oldal alján.
> 
> 

## <a name="production-checklist-for-all-virtual-machine-offers"></a>Minden virtuálisgép-ajánlat éles ellenőrzőlista
* Ellenőrizze, hogy a Microsoft Azure Certified minősítésű partner
* Az SKU-k lapon a beállítás "elrejtőzni Ez a Termékváltozat a Marketplace-en, mert azt kell mindig vásárolható keresztül megoldássablon" állapotúként kell megjelölni Igen csak akkor, ha a Termékváltozat-Megoldássablon részét képezi. Minden más esetben ez a beállítás mindig állapotúként kell megjelölni nem.
* Ne feledje: Nem kell módosítani a Termékváltozatot látható-e beállítása után a Termékváltozat szerepel a listán. Ez a funkció nem támogatott.
* Győződjön meg arról, hogy az Azure Marketplace-en embléma útmutatást az alábbiakban az emblémát formátumhoz.
* Az ajánlat és a Termékváltozat leírása nem lehet azonos.
* Termékváltozat címe és ajánlat hosszú összegzése nem lehet azonos.
* Termékváltozat-cím és ajánlat összegzés nem lehet azonos.
* Termékváltozat-címek nem lehet azonos egy több termékváltozatok.

**Az Azure Marketplace-en embléma irányelvei**

* Az Azure arculata egyszerű színpalettát használ. Tartsa számát az elsődleges és másodlagos színt az embléma alacsony.
* Az Azure Portal a témák színei fehér, és fekete. Ezért kerülje ezeket a színeket, a emblémák hátterének színét. Néhány színekkel, amely biztosítja, a emblémák neves az Azure Portalon. Javasoljuk az egyszerű alapszínek használatát. Ha használ áttetsző hátterű, majd győződjön meg arról, hogy az embléma/szöveg nem fehér vagy fekete.
* Ne használjon egy színátmenetes hátterének az embléma.
* Szöveg-, akár a vállalat vagy a márkanév, elkerüli az embléma.
* Az embléma megjelenését és működését "egyszerű" kell lennie, és kerülje átmenetekhez.
* Az embléma nem kell nyújtani.

**A Hero embléma irányelveket:**

* A Hero embléma megadása nem kötelező. A közzétevő nem szeretné Hero emblémát tölthet fel. **Azonban Miután feltöltött a hero ikon nem lehet törölni a közzétételi portálon. Ugyanakkor a partner kell kövesse a Azure Marketplace-en Hero ikonok másnak az ajánlat nem hagyható jóvá az éles környezetbe.**
* A közzétevő megjelenített neve, a Termékváltozat jogcímre és a hosszú összegzése ajánlat fehér a betűk színe megjelenik. Ezért kerülje a Hero ikon a háttérben gondoskodik a bármely világos szín. Fekete, fehér és átlátható háttér nem engedélyezett a Hero-ikonokat.
* A közzétevő nevét, SKU-cím, az ajánlat hosszú összegzése megjelenítéséhez, és a Létrehozás gombra vannak beágyazva programozott módon a Hero embléma után az ajánlat kerül a listában szereplő. Így nem szöveg kell adnia, a Hero embléma tervezésekor. Ne változtassa meg üres területet a jobb oldalon, mert a szöveget (pl. közzétevő megjelenített neve, SKU title, az ajánlat hosszú összegzése) szerepelni fognak programozott módon, van ott. Az üres helyet a szöveg legyen 415 x 100, a jobb oldali (és a balról 370px ellensúlyozza,).

## <a name="additional-production-checklist-for-already-listed-virtual-machine-offers"></a>További éles ellenőrzőlista már listán szereplő virtuális gép kínál
* Ellenőrizze, hogy van-e már a céges ajánlat ugyanilyen nevű ajánlat. Ha igen, akkor hozzá kell adnia egy új verziója a Termékváltozat egy új ismétlődő ajánlat létrehozása helyett a meglévő szerződéssel.
* Adatlemez ugyanazon Termékváltozat két verziója közötti ne módosítsa.
* Az Azure Marketplace-en nem támogatja a felsorolt Termékváltozatokra vonatkozó díjszabás módosítása, a következőkre hat a számlázás a meglévő ügyfelek. Győződjön meg arról, hogy ne változtassa a listában szereplő SKU-k a régiókban, ahol érhető el a Termékváltozat díjszabása. Azonban adja hozzá az új termékváltozatokra, vagy új régiók hozzáadása egy meglévő Termékváltozat.

## <a name="next-steps"></a>További lépések
Az ajánlat élesíti, miután az ügyfél teszteléshez ellenőrizze, hogy minden a szerződések és -funkcióinak megfelelően működjön, az éles környezetben tesztelt, és átmeneti környezetben érvényesítve.

## <a name="see-also"></a>Lásd még
* [Első lépések: az ajánlat közzététele az Azure piactéren](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]:media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]:media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]:media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png
