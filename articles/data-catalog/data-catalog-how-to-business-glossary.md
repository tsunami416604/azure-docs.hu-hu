---
title: Az Azure Data Catalog az üzleti szószedet összeállítása
description: Útmutató a cikk az Azure Data Catalog az üzleti szószedet kiemelése meghatározására és a egy közös üzleti szószedet címkéhez használatával regisztrált adategységeket.
author: markingmyname
ms.author: maghan
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: 0337b2a7a228052dacfb35982ee6e8d62372fb29
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59264216"
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>A címkézés szabályozott állítsa be az üzleti szószedet összeállítása

## <a name="introduction"></a>Bevezetés

Az Azure Data Catalog adatforrás-felderítés, lehetővé teszi, így könnyen felderítése és értelmezni azon adatforrásokat, amelyek elemzéseket végezhet, és döntéseket kell. Ezek a képességek a lehető leghatékonyabb felhasználásához végrehajtja, amikor a megkeresheti és az elérhető adatforrások széles ismertetése.

Egy Data Catalog szolgáltatást, amely elősegíti a nagyobb ismeretekkel eszközök adatok címkézése van. Címkézés használatával társíthatja a kulcsszavak egy eszköz vagy egy oszlopot, amely viszont megkönnyíti a keresést, vagy keresse az eszköz felderítése. Címkézés is segít több könnyen megérthetik, a környezet és az eszköz szándékot.

Azonban címkézés néha problémát okozhat saját. Néhány példa a problémákat, amelyek címkézése a következők:

* Bizonyos eszközök rövidítések és mások kibontott szöveg használatát. A program inkonzisztenciát a felderítés az eszközök, akadályozza meg, annak ellenére, hogy az azonos címkével ellátott adategységek címkézése volt a leképezés.
* Ez azt jelenti, környezettől függően lehetséges változásait. Például a címke neve *bevétel* egy ügyfél az adatkészlet bevétel szerint ügyfél arra utalhat, de ugyanazt a címkét a negyedéves értékesítési adatkészleteken negyedéves bevétel jelentheti a vállalat számára.  

Ezeket és más hasonló kihívások megoldása érdekében a Data Catalog egy üzleti szószedetet tartalmaz.

A Data Catalog üzleti szószedet használatával egy szervezet legfontosabb üzleti kifejezéseket és azok meghatározásait hozzon létre egy közös üzleti szószedet lehet dokumentálni. Ez a cégirányítási lehetővé teszi, hogy konzisztencia az adathasználatot a szervezeten belül. Miután egy kifejezést az üzleti szószedet van definiálva, hozzárendelhető egy adategységhez a katalógusban. Ez a megközelítés *címkézés szabályozott*, ugyanezzel a módszerrel címkézés másként van.

## <a name="glossary-availability-and-privileges"></a>Szószedet rendelkezésre állás és a jogosultságok

Az üzleti szószedet csak a az Azure Data Catalog Standard kiadásában érhető el. A Data Catalog ingyenes kiadásában nem tartalmazza a szószedet, és azt nem szabályozott címkézésre képességeket biztosít.

Az üzleti szószedet keresztül érheti el a **szószedet** lehetőséget a Data Catalog-portál navigációs menü.  

![Hozzáférés az üzleti szószedet](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Data Catalog-rendszergazdák és a szószedet-Rendszergazdák szerepkör tagjai létrehozása, szerkesztése és törlése az üzleti szószedet az szószedet kifejezései. A Data Catalog minden felhasználó megtekintheti a kifejezés-definíciók és a szószedet kifejezéseivel jelölhetik címke eszközök.

![A szószedet új kifejezésének hozzáadása](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Szószedet kifejezései létrehozása

Data Catalog-rendszergazdák és a szószedet-rendszergazdák kattintva hozhatja létre a szószedet kifejezései a **új kifejezés** gombra. Minden egyes szószedet kifejezésének a következő mezőket tartalmazzák:

* A kifejezés egy üzleti definíciója.
* Egy leírást, amely rögzíti a használatra, vagy az eszköz vagy az oszlop az üzleti szabályok
* Érintettek, akik a legtöbb ismert kifejezés listája
* Szülőkifejezés, amely meghatározza a hierarchiában, amelyben az előfizetési időszak van rendezve

## <a name="glossary-term-hierarchies"></a>Szószedet kifejezés hierarchiák

A Data Catalog üzleti szószedet használatával egy szervezet az üzleti szószedet tud adni a feltételek hierarchiaként, és létrehozható egy besorolási feltételek, amely jobban az üzleti elnevezési rendszert.

A kifejezés egy megadott hierarchia szintjén egyedinek kell lennie. Ismétlődő nevek nem engedélyezett. Nem a hierarchia szintek száma korlátozott, de egy hierarchia több könnyen gyakran megérthető amikor három szinten vagy kevesebb.

Az üzleti szószedet hierarchiái használata nem kötelező. Szószedet kifejezései a szülő kifejezés mezőt üresen hagyja a szószedet kifejezések átalánydíjjal (nem hierarchikus) listájának létrehoz.  

## <a name="tagging-assets-with-glossary-terms"></a>A szószedet kifejezéseivel jelölhetik eszközök címkézése

Szószedet kifejezései definiálva van átveheti a katalógusban, miután az adategységek címkézése élménye van optimalizálva keresse a gyűjteményt, ahogy a felhasználó beír egy címkét. A Data Catalog-portálon választhat egyező szószedet kifejezései listáját jeleníti meg. Ha a felhasználó a szószedet kifejezésének a listából, az előfizetési időszak hozzáadódik az eszköz (más néven szószedetcímke) címkeként. A felhasználó új címke létrehozásához, amely nem szerepel a szószedet kifejezés beírásával is választhat (más néven felhasználói címkét).

![Egy felhasználói címkét és két szószedet címkét címkézett adategységhez](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> A felhasználói címkék a Data Catalog ingyenes kiadása támogatja a címke csak ilyen típusú.

### <a name="hover-behavior-on-tags"></a>Címkék viselkedése rámutatáskor

A Data Catalog-portálon a címkék kétféle és vizuális megjelenítés segítségével különböző található különböző rámutatáskor viselkedéseket. Ha az egérmutatót egy felhasználói címkét, láthatja a címke szöveg és a felhasználó vagy felhasználók, akik a címkék hozzáadását. Ha az egérmutatót szószedetcímke, meghatározása a szószedet kifejezésének és nyissa meg a teljes definíciójának megtekintése az üzleti szószedet mutató hivatkozás is megjelenik.

### <a name="search-filters-for-tags"></a>A címkék keresési szűrői

Szószedet-címkék és a felhasználói címkék is kereshető, és alkalmazhatja azokat a keresés szűrőként.

## <a name="summary"></a>Összegzés

Az üzleti szószedet lehetővé teszi a szabályozott címkézés, és az Azure Data Catalog használatával azonosítsa, kezelheti és egységes módon adategységeket. Az üzleti szószedet learning, az üzleti szószedet a szervezet tagjai léptetheti elő. A szószedet is támogatja az értelmezhető metaadatokat, ami megkönnyíti az adatvagyon felderítését és megértése rögzítése.

## <a name="next-steps"></a>További lépések

* [Az üzleti szószedet tevékenységekhez REST API dokumentációja](/rest/api/datacatalog/data-catalog-glossary)
