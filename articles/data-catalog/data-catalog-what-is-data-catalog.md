---
title: "Bevezetés az Azure Data Catalog használatába | Microsoft Docs"
description: "Ez a cikk a Microsoft Azure Data Catalog áttekintését tartalmazza, beleértve a szolgáltatás funkcióit, valamint a problémákat, amelyek áthidalására a szolgáltatást tervezték. A Data Catalog olyan képességeket biztosít, amelyek minden felhasználónak – az elemzőktől az adatszakértőkön át a fejlesztőkig – lehetővé teszik az adatforrások regisztrálását, felderítését, megértését és felhasználását."
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
ms.date: 01/23/2017
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: cd3184059a0970bb7c9b59803cf37125abbbd879
ms.openlocfilehash: 19d4c3990c88f82f60cd253ffdcbd2005d00ad79


---
# <a name="what-is-azure-data-catalog"></a>Mi az az Azure Data Catalog?
Az Azure Data Catalog egy teljes körűen felügyelt felhőszolgáltatás, amely lehetővé teszi a felhasználóknak, hogy számukra szükséges adatforrásokat fedezzenek fel, és hogy a talált adatforrásokat értelmezzék, miközben a szervezeteket is segíti minél többet kihozni a meglévő befektetéseikből. A Data Catalog olyan képességeket biztosít, amelyek minden felhasználó számára – az elemzőktől az adatszakértőkön át a fejlesztőkig – lehetővé teszik az adatforrások felderítését, értelmezését és felhasználását. A Data Catalog egy közösségi metaadat- és megjegyzésmegosztási modellt is tartalmaz, amelynek segítségével minden felhasználó közzéteheti a saját ismereteit, és egy adatközösség hozható létre.

## <a name="discovery-challenges-for-data-consumers"></a>Az adatok felfedezésének kihívásai az adatfelhasználók számára
A vállalati adatforrások felfedezése hosszú ideje egy organikus, kollektív tudáson alapú folyamat. Ez számos kihívást támaszt a vállalatok számára, akik az információs adategységeiket a lehető leghatékonyabban szeretnék kihasználni.

* A felhasználók nem tudnak az adatforrások létezéséről, amíg egy másik folyamat során nem kerülnek velük kapcsolatba. Nincs egy központi nyilvántartás az adatforrásokról.
* Ha egy felhasználó nem ismeri az adatforrás pontos helyét, nem tud egy ügyfélalkalmazással kapcsolódni a benne található adatokhoz. Az adatok felhasználásának követelménye, hogy a felhasználók ismerjék a kapcsolati karakterláncot vagy az elérési utat.
* Hacsak egy felhasználó nem ismeri egy adatforrás dokumentációjának a helyét, nem tudja értelmezni az adatok felhasználási célját. Az adatforrások és a dokumentációk különböző helyeken találhatók és különböző módokon lehet őket felhasználni.
* Ha egy felhasználónak kérdései vannak egy adategységgel kapcsolatban, offline módon kell felvennie a kapcsolatot az adatokért felelős szakértővel vagy csapattal. Nincs explicit kapcsolat az adatok és a használatukra vonatkozó szakértői meglátásokkal rendelkező személyek között.
* Hacsak egy felhasználó nincs tisztában az adatforráshoz való hozzáférés kérelmezésének menetével, az adatforrás és a hozzá tartozó dokumentáció felfedezésével még mindig nem fér hozzá az adatokhoz, amelyekre szüksége van.

## <a name="discovery-challenges-for-data-producers"></a>Az adatok felfedezésének kihívásai az adatalkotók számára
Miközben az adatfelhasználók mindezekkel a kihívásokkal néznek szembe, az információs adategységek létrehozásáért és fenntartásáért felelős felhasználóknak szintén megvannak a saját kihívásaik.

* Az adatforrások leíró metaadatokkal való ellátása gyakran hiábavaló, mivel az ügyfélalkalmazások általában nem veszik figyelembe az adatforrásokban tárolt leírásokat.
* Az adatforrásokhoz tartozó dokumentáció létrehozása gyakran hiábavaló, mert a dokumentációt folyamatosan szinkronban kell tartani az adatforrásokkal, a felhasználók pedig nem bíznak a dokumentációban, amelyeket gyakran elavultnak tartanak.
* Állandó kihívás az adatforrásokhoz való hozzáférés korlátozása, és annak biztosítása, hogy az adatfelhasználók ismerjék a hozzáférések kérelmezésének menetét.

Az adatforrásokhoz tartozó dokumentációk létrehozása és fenntartása összetett és időigényes feladat. A dokumentációkat az adatforrás minden felhasználója számára elérhetővé tenni pedig gyakran még inkább az.

Ezek a kihívások együttesen jelentős korlátokat állítanak a vállalatok elé, akik szorgalmazni kívánják a vállalati adatok felhasználását és értelmezését.

## <a name="azure-data-catalog-can-help"></a>Az Azure Data Catalog segíthet
A Data Catalog szolgáltatást arra tervezték, hogy megoldja ezeket a problémákat, és lehetővé tegye a vállalatok számára, hogy a lehető leghatékonyabban használják ki a meglévő információs eszközeiket. A Data Catalog segít az adatforrásokat könnyen felfedezhetővé és értelmezhetővé tenni azon felhasználók számára, akiknek szükségük van a bennük foglalt adatokra.

A Data Catalog egy felhőalapú szolgáltatást biztosít, amelybe az adatforrásokat regisztrálni lehet. Az adatok az eredeti helyükön maradnak, de a metaadatok másolatai bekerülnek a Data Catalog katalógusába, az adatforrások helyére mutató hivatkozásokkal együtt. A metaadatok indexelésének köszönhetően az adatforrások egy egyszerű keresés által felfedezhetők, majd értelmezhetők.

Miután egy adatforrás regisztrálva lett, a metaadatai bővíthetők, akár a regisztrálását elvégző felhasználó, akár a vállalaton belüli más felhasználók által. Bármely felhasználó megjegyzésekkel láthatja el az adatforrásokat, amelyekben leírásokat, címkéket és egyéb metaadatokat, például dokumentációkat és hozzáférés-kérelmezési eljárásokat adhat meg. Ezek a leíró metaadatok kiegészítik az adatforrásból regisztrált szerkezeti metaadatokat (például oszlopneveket és adattípusokat).

Az adatforrások regisztrálásának elsődleges célja, hogy a források felfedezhetők, értelmezhetők és felhasználhatók legyenek. Ha a vállalat felhasználóinak adatokra van szükségük a munkájukhoz (legyen az üzleti intelligencia, alkalmazásfejlesztés, adattudomány vagy bármilyen egyéb jellegű feladat, amelyhez megfelelő adatokra van szükség), a Data Catalog felfedezési eljárásának használatával gyorsan megtalálhatják az igényeiknek megfelelő adatokat, értelmezhetik azokat és kiértékelhetik, hogy megfelelnek-e a céljaiknak, majd az adatforrások egy tetszőleges eszköz általi megnyitásával felhasználhatják az adatokat. Emellett a Data Catalog lehetővé teszi, hogy a katalógushoz a felhasználók is hozzájáruljanak, és címkéket, dokumentációkat és megjegyzéseket adjanak a már regisztrált adatforrásokhoz, vagy új adatforrásokat regisztráljanak, amelyeket aztán a katalógus felhasználóinak közössége felfedezhet, értelmezhet és felhasználhat.

![Az Azure Data Catalog képességei](./media/data-catalog-what-is-data-catalog/data-catalog-capabilities.png)

## <a name="get-started-with-data-catalog"></a>Bevezetés a Data Catalog használatába
A Data Catalog használatának azonnali megkezdéséhez látogasson el a [www.azuredatacatalog.com](https://www.azuredatacatalog.com) webhelyre.

Az első lépésekről szóló útmutatót [itt](data-catalog-get-started.md) érheti el.

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



<!--HONumber=Jan17_HO4-->


