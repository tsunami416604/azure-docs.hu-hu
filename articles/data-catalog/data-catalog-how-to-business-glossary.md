---
title: "Állítsa be az üzleti szószedet, az Azure Data Catalog szerinti címkézését szabályozott |} Microsoft Docs"
description: "Az üzleti szószedet az Azure Data Catalog kiemelés meghatározása és a segítségével egy közös üzleti szószedet címkéhez útmutató cikk regisztrált adategységek keresése."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: b3d63dbe-1ae7-499f-bc46-42124e950cd6
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: a80b7fd0c21851a6670431e9b8647ca5cf5f51ec
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>A címkézés szabályozott az üzleti szószedet beállítása
## <a name="introduction"></a>Bevezetés
Az Azure Data Catalog lehetővé teszi, hogy az adatforrás felderítés, így könnyen felderítése és értelmezni azon adatforrásokat, amelyek elemzést és döntéseket kell. Ezeket a képességeket a legnagyobb mértékben ellenőrizze, ha található, és elérhető adatforrások legszélesebb skáláját ismertetése.

Egy Data Catalog szolgáltatás, amely elősegíti a eszközök adatok megértése címkézés van. Címkézés használatával is társíthat kulcsszavak egy eszköz vagy egy oszlop, amely viszont megkönnyíti a keresést, vagy keresse meg az eszköz felderítése. Címkézés is segítségével megismerheti, további egyszerűen a környezet és az eszköz leképezés.

Azonban címkézés néha problémákat okozhat saját. Néhány példa a problémák, amelyek címkézése a következők:

* Egyes eszközök rövidítések és mások bővített szöveg használatát. Ez inkonzisztenciája miatt az eszközök, a felderítés akadályozza meg, annak ellenére, hogy a célt az volt, hogy az eszközök címkét a azonos címkével.
* Ez azt jelenti, attól függően, hogy a környezetben esetleges változásait. Például a címke neve *bevétel* az ügyfél az adatkészlet jelentheti ügyfél bevétel, de a negyedéves értékesítési dataset azonos címkével negyedéves bevétel jelentheti a vállalat számára.  

Ezeket és más hasonló kihívást érdekében a Data Catalog egy üzleti szószedetet tartalmaz.

A Data Catalog üzleti szószedet segítségével egy szervezet üzleti kifejezések és meghatározások létrehozása egy közös üzleti szószedet is dokumentum. A cégirányítási lehetővé teszi, hogy következetes használati adatok a teljes szervezetben. Miután az üzleti szószedet definiálva van egy kifejezés, hozzárendelhető egy adategységhez a katalógusban. Ez a megközelítés *szabályozott címkézés*, ugyanezt a megközelítést, mint a címkézés van.

## <a name="glossary-availability-and-privileges"></a>Szószedet rendelkezésre állás és a jogosultságokat
Az üzleti szószedet csak a Standard Edition Azure Data Catalog érhető el. A Data Catalog szabad Edition nem tartalmazza a termékben, és nem biztosít képességek az irányadó címkézésre.

Az üzleti szószedet keresztül érheti el a **szószedet** lehetőséget a Data Catalog-portál navigációs menüjében.  

![Az üzleti szószedet elérése](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Katalógus-rendszergazdák és a szószedet-Rendszergazdák szerepkör tagjai létrehozása, szerkesztése és törlése az üzleti szószedet szószedet feltételeit. A Data Catalog senki kifejezés definíciókat és címke eszközök szószedet kifejezéseivel jelölhetik megtekintheti.

![Egy új szószedetben hozzáadása](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>SZÓJEGYZÉK létrehozása
Katalógus-rendszergazdák és rendszergazdák szószedet kattintva hozhat létre Szójegyzék a **új kifejezés** gombra. Minden egyes szószedetben a következő mezőket tartalmazza:

* A kifejezés egy üzleti definíciója.
* Egy leírást, amely rögzíti a tervezett használattól vagy az eszköz vagy az oszlop üzleti szabályok
* Az érdekelt felek, akik a legtöbb tudni kifejezés listáját
* A szülő kifejezés, amely meghatározza a hierarchia kifejezés vannak rendezve

## <a name="glossary-term-hierarchies"></a>Szószedet kifejezés hierarchiák
A Data Catalog üzleti szószedet segítségével egy szervezet az üzleti szószedet leírhatja kifejezések hierarchikus, és létrehozhat egy besorolási feltételek az üzleti elnevezési rendszert jobban jelölő.

Egy kifejezés egyedinek kell lennie a hierarchiában megadott mértékét. Duplikált nevek használata nem engedélyezett. A hierarchia szintjének száma nincs korlátozva, de a hierarchiában van gyakran több könnyen érthető amikor három szintje vagy kevesebb.

Az üzleti szószedet hierarchiái használata nem kötelező. SZÓJEGYZÉK a szülő kifejezés mező üresen hagyja listát hoz létre (nem hierarchikus) simán kifejezések a szószedet a.  

## <a name="tagging-assets-with-glossary-terms"></a>Szószedet kifejezéseivel jelölhetik eszközök címkézése
Után Szójegyzék a katalógus belül, a címkézés eszközök élménye megfelelően lett optimalizálva a felhasználó beír egy címkét a szószedet keresési. A Data Catalog-portál választhat egyező szószedetben listáját jeleníti meg. Ha a felhasználó kijelöli a szószedetben a listából, kifejezés hozzáadódik az eszköz (más néven a szószedet címkével) címkeként. A felhasználó is választhat egy új címke létrehozásához írja be a keresőkifejezést, amely nem szerepel a szószedet (más néven a felhasználói kód).

![Egy felhasználó címkével és két szószedet címkék címkézett adategységet](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> Felhasználói címke található a Data Catalog szabad Edition támogatott címke csak ilyen típusú.
>
>

### <a name="hover-behavior-on-tags"></a>Vigye a címkék viselkedése
A Data Catalog-portálon a címkék kétféle vizuálisan önálló, és jelen különböző rámutatáskor viselkedések. Ha egy felhasználó címke mutat, láthatja a címke szövegét és a felhasználó vagy felhasználók, akik jelentek meg a címke. Ha egy szószedet címke mutat, is látja a szószedetben és a hivatkozások megnyithassanak az üzleti szószedet a teljes definíciójának megtekintése.

### <a name="search-filters-for-tags"></a>A címkék keresési szűrők
Szószedet címkék és a felhasználói címkék nem kereshető is, és alkalmazhatja azokat a keresési szűrőként.

## <a name="summary"></a>Összegzés
Az üzleti szószedet az Azure Data Catalog, és lehetővé teszi, hogy az irányadó címkézést használva, azonosítására, kezelése és egységes módon adategységeket. Az üzleti szószedet előléptetheti az üzleti szószedet szervezet tagjai tudomást. A szószedet is támogatja a rögzítés jelentéssel bíró metaadatok, amely egyszerűbbé teszi az eszköz felderítése és értelmezését.

## <a name="next-steps"></a>További lépések
* [Üzleti szószedet műveletek REST API dokumentációja](https://msdn.microsoft.com/library/mt708855.aspx)
