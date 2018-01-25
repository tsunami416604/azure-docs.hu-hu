---
title: "Bevezetés az Azure Data Catalog használatába | Microsoft Docs"
description: "Ez a cikk a Microsoft Azure Data Catalog áttekintését tartalmazza, beleértve a szolgáltatás funkcióit, és azokat a problémákat, amelyeket a szolgáltatás segítségével áthidalhat. A Data Catalog minden felhasználó számára lehetővé teszi adatforrások regisztrálását, felfedezését, értelmezését és felhasználását."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: cc733907-17ec-4153-9f0c-5b3754b2db19
ms.service: data-catalog
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: edf6e1d8b86d9773b54353bb70f000bcdf40e5ec
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="what-is-azure-data-catalog"></a>Mi az az Azure Data Catalog?
Az Azure Data Catalog egy teljes körűen felügyelt felhőszolgáltatás, amely segít a felhasználóknak megtalálni a keresett adatforrásokat és értelmezni őket. Eközben a Data Catalog a vállalatoknak is segít még több értéket kinyerni a meglévő befektetésekből. 

A Data Catalog segítségével bármely felhasználó (elemző, adattudós, vagy fejlesztő) felfedezhet, értelmezhet és felhasználhat adatforrásokat. A Data Catalog tartalmaz egy közösségi modellt is a metaadatok és megjegyzések kiszervezéséhez. A vállalat felhasználói egyetlen központi helyen oszthatják meg ismereteiket egymással és építhetnek közösséget és kultúrát az adatok köré.

## <a name="discovery-challenges-for-data-consumers"></a>Az adatok felfedezésének kihívásai az adatfelhasználók számára
A vállalati adatforrások felfedezése hosszú ideje egy organikus, kollektív tudáson alapú folyamat. Ez számos kihívást gördít azon vállalatok elé, akik az információs adategységeiket a lehető leghatékonyabban szeretnék kihasználni:

* A felhasználók lehet, hogy csak akkor szereznek tudomást egy adatforrás létezéséről, amikor egy másik munkafolyamat során kapcsolatba kerülnek vele. Nincs egyetlen központi hely az adatforrások nyilvántartására.
* A felhasználók ügyfélalkalmazásaikkal csak akkor tudják elérni az adott adatforrást, ha tudják annak pontos helyét. A hagyományos adatfelhasználási folyamatok során a felhasználóknak tudniuk kell a megfelelő kapcsolati karakterláncot, vagy az elérési utat.
* Hacsak nem tudják a felhasználók az adott adatforráshoz tartozó dokumentáció helyét, nem fogják tudni pontosan, hogyan is kell az adatokat felhasználni. Lehet, hogy az adatforrások és dokumentáció csak különböző helyeken és különböző módokon érhetőek el.
* Ha a felhasználóknak kérdése van egy adott információval kapcsolatban, személyesen kell megkeresniük az adatokért felelős szakembert vagy csapatot. Nincs explicit kapcsolat az adatok és az azok felhasználására szakértői módon rálátó emberek között.
* Hacsak egy felhasználó nincs tisztában az adatforráshoz való hozzáférés kérelmezésének menetével, az adatforrás és a hozzá tartozó dokumentáció felfedezésével még mindig nem fér hozzá a szükséges adatokhoz.

## <a name="discovery-challenges-for-data-producers"></a>Az adatok felfedezésének kihívásai az adatalkotók számára
Miközben az adatfelhasználók mindezekkel az említett kihívásokkal néznek szembe, az információs adategységek létrehozásáért és fenntartásáért felelős felhasználóknak szintén megvannak a saját kihívásaik:

* Az adatforrások felcímkézése tájékoztató jellegű metaadatokkal gyakran hiábavalónak bizonyul. Az ügyfélalkalmazások általában figyelmen kívül hagyják az adatforrásban tárolt tájékoztatókat.
* Adatforrásokhoz dokumentációt készíteni gyakran hiábavalónak bizonyul. Szinkronban tartani az adatforrást és annak dokumentációját folyamatos felelősséget jelent, és a felhasználók nem is nagyon bíznak egy elavultnak tűnő dokumentációban.
* Az adatforrásokhoz tartozó dokumentációk létrehozása és fenntartása összetett és időigényes feladat. A dokumentációkat az adatforrás minden felhasználója számára elérhetővé tenni pedig gyakran még inkább az.
* Állandó kihívás az adatforrásokhoz való hozzáférés korlátozása, és annak biztosítása, hogy az adatfelhasználók ismerjék a hozzáférések kérelmezésének menetét.

Az ilyen kihívások együttesen jelentős korlátokat állítanak a vállalatok elé, akik szorgalmazni kívánják a vállalati adatok felhasználását és értelmezését.

## <a name="azure-data-catalog-can-help"></a>Az Azure Data Catalog segíthet
A Data Catalog szolgáltatást arra tervezték, hogy megoldja ezeket a problémákat, és segítsen a vállalatoknak a lehető leghatékonyabban kihasználni a meglévő információs eszközeiket. A Data Catalog az adatforrásokat könnyen felfedezhetővé és értelmezhetővé teszi azon felhasználók számára, akik a bennük foglalt adatokat kezelik.

A Data Catalog egy felhőalapú szolgáltatást biztosít, amelybe az adatforrásokat regisztrálni lehet. Az adatok az eredeti helyükön maradnak, de a metaadatok másolatai bekerülnek a Data Catalog katalógusába, az adatforrások helyére mutató hivatkozásokkal együtt. A metaadatok indexelésének köszönhetően az adatforrások egy egyszerű keresés által felfedezhetővé és könnyen értelmezhetővé válnak a felhasználók számára.

Miután egy adatforrás regisztrálva lett, a metaadatai bővíthetők, akár az azt regisztráló felhasználó, akár a vállalaton belüli többi felhasználó által. Bármely felhasználó megjegyzésekkel láthatja el az adatforrásokat, amelyekben leírásokat, címkéket és egyéb metaadatokat, például dokumentációkat és hozzáférés-kérelmezési eljárásokat adhat meg. Ezek a leíró metaadatok kiegészítik az adatforrásból regisztrált szerkezeti metaadatokat (például oszlopneveket és adattípusokat).

Az adatforrások regisztrálásának elsődleges célja, hogy a források felfedezhetők, értelmezhetők és felhasználhatók legyenek. A vállalati felhasználóknak adatokra lehet szükségük az üzleti intelligencia, az alkalmazásfejlesztés, az adattudomány és minden más olyan tevékenység terén, ahol fontos a megfelelő adatok használata. Ők a Data Catalog segítségével könnyedén megtalálhatják a szükségleteiknek megfelelő adatokat, értelmezhetik, hogy mennyire felelnek meg felhasználási szempontjaiknak, és fel is használhatják őket úgy, hogy az adatforrást a választott eszközzel nyitják meg. 

Ezzel egy időben a regisztrált adatforrások címkézésével, dokumentáció, illetve jegyzetek készítésével a felhasználók közre is tudnak működni a katalógus készítésében. További adatforrásokat is regisztrálni tudnak, amelyeket a katalógus felhasználói közössége szabadon felfedezhet, értelmezhet és felhasználhat.

![A Data Catalog képességei](./media/data-catalog-what-is-data-catalog/data-catalog-capabilities.png)

## <a name="learn-more-about-data-catalog"></a>További információ a Data Catalog szolgáltatásról
A Data Catalog képességeinek bővebb megismeréséhez lásd a következő témaköröket:

* [Adatforrások regisztrálása](data-catalog-how-to-register.md)
* [Adatforrások felfedezése](data-catalog-how-to-discover.md)
* [Adatforrások ellátása megjegyzésekkel](data-catalog-how-to-annotate.md)
* [Adatforrások dokumentálása](data-catalog-how-to-documentation.md)
* [Kapcsolódás az adatforrásokhoz](data-catalog-how-to-connect.md)
* [Munkavégzés big data típusú adatokkal](data-catalog-how-to-big-data.md)
* [Adategységek felügyelete](data-catalog-how-to-manage.md)
* [Az üzleti szószedet összeállítása](data-catalog-how-to-business-glossary.md)
* [Gyakori kérdések](data-catalog-frequently-asked-questions.md)

## <a name="next-steps"></a>További lépések
A Data Catalog használatával itt ismerkedhet meg:
* [Microsoft Azure Data Catalog](https://www.azuredatacatalog.com)
* [Ismerkedés az Azure Data Catalog szolgáltatással](data-catalog-get-started.md)
