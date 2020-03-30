---
title: Az üzleti szószedet beállítása az Azure Data Catalogban
description: Útmutató cikk, amely kiemeli az üzleti szószedetet az Azure Data Catalog-ban a regisztrált adatelemek címkézéséhez és közös üzleti szókincsének meghatározásához és használatához.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 1065abecb1f0ef57eb13b1ec3f194f07ae01eaee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976791"
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>A szabályozott címkézés üzleti szószedetének beállítása

## <a name="introduction"></a>Bevezetés

Az Azure Data Catalog lehetővé teszi az adatforrás-felderítést, így könnyen felfedezheti és megismerheti az elemzés elvégzéséhez és a döntések meghozatalához szükséges adatforrásokat. Ezek a képességek a legnagyobb hatást fejtik ki, ha megtalálja és megismeri a rendelkezésre álló adatforrások legszélesebb körét.

Az eszközadatok jobb megértését elősegítő adatkatalógus-szolgáltatás a címkézés. A címkézés használatával kulcsszavakat társíthat egy eszközhöz vagy oszlophoz, ami viszont megkönnyíti az eszköz felderítését kereséssel vagy böngészéssel. A címkézés segítségével könnyebben megértheti az eszköz környezetét és szándékát.

A címkézés azonban néha saját problémákat is okozhat. Néhány példa a címkézés által bevezethető problémákra:

* Egyes eszközökön rövidítések használata és más szövegek kibővített szövege. Ez az inkonzisztencia akadályozza az eszközök felderítését, még akkor is, ha a szándék az volt, hogy az eszközöket ugyanazzal a címkével címkézze.
* A jelentés lehetséges eltérései, a kontextustól függően. Például egy ügyfél-adatkészlet Bevétel nevű *címvel* ügyfélszerint bevételnek lehet kitenni, de a negyedéves értékesítési adatkészleten lévő címke negyedéves bevételt jelenthet a vállalat számára.  

Ezen és más hasonló kihívások kezelése érdekében a Data Catalog tartalmaz egy üzleti szószedetet.

A Data Catalog üzleti szószedet használatával a szervezet dokumentálhatja a legfontosabb üzleti kifejezéseket és azok definícióit, hogy közös üzleti szókincset hozzon létre. Ez a cégirányítási lehetővé teszi az adathasználat konzisztenciáját a szervezetben. Miután egy kifejezést definiált az üzleti szószedetben, hozzárendelhető egy adateszközhöz a katalógusban. Ez a megközelítés, *szabályozott címkézés*, ugyanaz a megközelítés, mint a címkézés.

## <a name="glossary-availability-and-privileges"></a>Szószedet elérhetősége és jogosultságai

Az üzleti szószedet csak az Azure Data Catalog Standard Edition kiadásában érhető el. A Data Catalog ingyenes kiadása nem tartalmaz szószedetet, és nem biztosít képességeket a szabályozott címkézéshez.

Az üzleti szószedetet a Data Catalog portál navigációs **menüjének Szószedet** beállításával érheti el.  

![Adatkatalógus - Hozzáférés az üzleti szószedethez](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

A Data Catalog rendszergazdái és a szószedet-rendszergazdák szerepkör tagjai létrehozhatnak, szerkeszthetnek és törölhetnek szószedetkifejezéseket az üzleti szószedetben. Minden adatkatalógus-felhasználó megtekintheti a kifejezésdefiníciókat és az eszközök szószedetes kifejezésekkel való címkézését.

![Adatkatalógus – Új szószedet kifejezés hozzáadása](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Szószedetkifejezések létrehozása

A Data Catalog rendszergazdái és a glossary rendszergazdák az **Új kifejezés** gombra kattintva hozhatnak létre szószedetkifejezéseket. Minden szószedetkifejezés a következő mezőket tartalmazza:

* A kifejezés üzleti definíciója
* Az eszköz vagy oszlop tervezett felhasználását vagy üzleti szabályait rögzítő leírás
* Azoknak az érdekelt feleknek a listája, akik a legtöbbet ismerik a kifejezést
* A szülőkifejezés, amely meghatározza azt a hierarchiát, amelyben a kifejezés szerveződik

## <a name="glossary-term-hierarchies"></a>Szószedeti kifejezéshierarchiák

A Data Catalog üzleti szószedet használatával a szervezet az üzleti szókincsét a kifejezések hierarchiájaként írhatja le, és létrehozhat olyan kifejezések osztályozását, amelyek jobban képviselik az üzleti besorolását.

Egy kifejezésnek egyedinek kell lennie a hierarchia egy adott szintjén. Az ismétlődő nevek nem engedélyezettek. A hierarchia szintjeinek száma nincs korlátozva, de a hierarchia gyakran könnyebben érthető, ha három vagy kevesebb szint van.

A hierarchiák használata az üzleti szószedetben nem kötelező. Ha üresen hagyja a szülő kifejezés mezőt a szószedeti kifejezésekhez, a szószedetben egy egyszerű (nem hierarchikus) kifejezéslistát hoz létre.  

## <a name="tagging-assets-with-glossary-terms"></a>Eszközök címkézése szószedeti kifejezésekkel

Miután a szószedeti kifejezéseket definiálták a katalógusban, az eszközök címkézésének élménye úgy van optimalizálva, hogy a szószedetben keressen, amikor a felhasználó beírja a címkét. A Data Catalog portál megjeleníti a megfelelő szószedeti kifejezések listáját. Ha a felhasználó kijelöl egy szószedetkifejezést a listából, a kifejezés címkeként (más néven szószedetcímként) kerül az eszközhöz. A felhasználó úgy is dönthet, hogy új címkét hoz létre, ha olyan kifejezést ír be, amely nem szerepel a szószedetben (más néven felhasználói címke).

![Egy felhasználói címkével és két szószedetcímkével ellátott adateszköz](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> A felhasználói címkék az egyetlen olyan címketípus, amelyet a Data Catalog ingyenes kiadása támogat.

### <a name="hover-behavior-on-tags"></a>Rámutatás viselkedése a címkéken

A Data Catalog portálon a két címketípus vizuálisan elkülönül, és különböző rámutatási viselkedést mutat. Amikor az egérmutatót egy felhasználói címke fölé viszi, láthatja a címke szövegét, valamint a címkét hozzáadó felhasználót vagy felhasználókat. Amikor az egérmutatót egy szószedetcímke fölé viszi, megjelenik a szószedet kifejezés definíciója és egy hivatkozás az üzleti szószedet megnyitásához a kifejezés teljes definíciójának megtekintéséhez.

### <a name="search-filters-for-tags"></a>Címkék szűrőinek keresése

A szószedetcímkék és a felhasználói címkék egyaránt kereshetőek, és a keresésben szűrőként is alkalmazhatja őket.

## <a name="summary"></a>Összefoglalás

Az Azure Data Catalog üzleti szószedetének és a szabályozott címkézésnek a használatával egységes módon azonosíthatja, kezelheti és fedezheti fel az adateszközöket. Az üzleti szószedet elősegítheti az üzleti szókincs tanulását a szervezet tagjai által. A szószedet is támogatja az értelmes metaadatok rögzítését, ami leegyszerűsíti az eszközök felderítését és megértését.

## <a name="next-steps"></a>További lépések

* [REST API dokumentáció üzleti szószedetműveletekhez](/rest/api/datacatalog/data-catalog-glossary)
