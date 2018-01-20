---
title: "Az Azure Data Catalog gyakori kérdések |} Microsoft Docs"
description: "Azure Data Catalog, beleértve az adatforrás-felderítés, Megjegyzés és felügyeleti képességek kapcsolatos gyakori kérdésekre."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 5c7e209a-458c-4bb4-96bb-7ed178f9528a
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: 1f69912e654ac243a0c4f30426d17ce4199e498f
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Az Azure Data Catalog gyakori kérdések
Ez a cikk az Azure Data Catalog szolgáltatással kapcsolatos gyakran feltett kérdésekre adott válaszokat tartalmazza.

## <a name="what-is-azure-data-catalog"></a>Mi az az Azure Data Catalog?
A Data Catalog egy teljes körűen felügyelt szolgáltatás, a Microsoft Azure, a regisztráció és a vállalati adatforrások felderítését a rendszer ellátó üzemeltetett. A Data Catalog bármely felhasználó, az adatelemzők és fejlesztők számára, hogy az elemzők is regisztrálni, felderítését, megértéséhez, és felhasználását adatforrások.

## <a name="what-customer-challenges-does-it-solve"></a>Milyen felhasználói felkéri minderre megoldani?
A Data Catalog adatforrás felderítés és az "sötét adatok" kihívásai megoldást, így a felhasználók felderítésére és megérteni a vállalati adatforrások.

## <a name="what-are-its-target-audiences"></a>Mik a célközönséget?
A Data Catalog célja a műszaki és nem technikai jellegű felhasználók számára, beleértve:

* Adatok fejlesztők és BI és az elemzések szakemberek számára: adatok és analitikák tartalom felhasználásához másoknak előállító felelős személyeket.
* Adatok megbízott: személyek, akiknek az adatokat, mit jelent, és hogyan célja, hogy használható ismerete.
* Az adatfelhasználók: tudják könnyen felfedezheti, személyek ismerje meg, és az adatokhoz az általuk választott eszköz használatával a feladatok elvégzéséhez szükséges.
* Központi informatikai: személyeket, akik kell adatforrások több száz felderíthető üzleti felhasználók, és ki kell tartania a felügyelet adatok használatának, illetve akik.

## <a name="what-is-its-availability-by-region"></a>Mi az a régió rendelkezésre állása?
Jelenleg Data Catalog szolgáltatások érhetők el a következő adatközpontokban:

* USA nyugati régiója
* USA keleti régiója
* Nyugat-Európa
* Észak-Európa
* Kelet-Ausztrália
* Délkelet-Ázsia

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>Az adategységek számának korlátai
A Data Catalog szabad Edition 5000 regisztrált adategységeket korlátozódik.

A Data Catalog Standard Edition legfeljebb 100 000 regisztrált adategységeket támogatja.

Minden objektum regisztrálva a Data Catalog, táblák, például egy adategységet, nézetek, a fájlok és a jelentések, számát.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>Mik azok a támogatott forrás- és eszköz adattípust?
A jelenleg támogatott adatforrások listájáért lásd: [Data Catalog DSR](data-catalog-dsr.md).

## <a name="how-do-i-request-support-for-another-data-source"></a>Hogyan a egy másik adatforrás támogatási kérelem?
Funkciókérések és egyéb visszajelzését küldje el, keresse fel a [visszajelzés fórumbejegyzést Azure Data Catalog](https://feedback.azure.com/forums/906052-data-catalog/category/320788-data-sources).

## <a name="how-do-i-get-started-with-data-catalog"></a>Hogyan kezdjem el a Data Catalog?
A rendszer a legjobb módszer a kezdéshez [Bevezetés a Data Catalog](data-catalog-get-started.md). Ez a cikk a végpontok közötti áttekintést a szolgáltatás képessége.

## <a name="how-do-i-register-my-data"></a>Hogyan regisztrálhatom adataimat?
Az adatok a Data Catalog regisztrálása:
1. Az Azure Data Catalog-portálon a a **közzététel** terület, az Azure Data Catalog-regisztráló eszköz elindításához. 
2. A Data Catalog adatforrás-regisztráló eszköz, a bejelentkezés ugyanazokat a hitelesítő adatokat használja a Data Catalog-portál eléréséhez.
3. Válassza ki az adatforrást és az adott eszközöket regisztrálni kívánt.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>Mely tulajdonságok nem azt kibontásához regisztrált adategységeket?
A tulajdonságokat különböznek az adatforrást az adatforrás, de általában a Data Catalog közzétételi szolgáltatás kibontja a következő információkat:

* Eszköz neve
* Eszköztípus
* Eszköz leírása
* Attribútum-vagy oszlopnév
* Attribútum/oszloptípus-adatokat
* Attribútum/oszlop leírása

> [!IMPORTANT]
> Adatok eszközök regisztrálása a Data Catalog nem áthelyezése vagy másolása az adatok a felhőben. Az eszközök metaadatok adatforrásból származó eszközök regisztrálása másolja át az Azure-ba, de az adatok továbbra is a meglévő adatforrás helyen. Ez a szabály a kivétel, ha úgy dönt, hogy előzetes rekordok vagy adatok profil töltse fel az eszközök regisztrálásakor. Ha befoglal egy előzetes, legfeljebb 20 rekordok másolta át minden eszközhöz, és tárolja a Data Catalog pillanatképként. Ha egy adatok profil, összesített adatát számított, a katalógusban tárolt metaadatok része. Összesített adatok tartalmazhatnak táblák, a null értékeket oszloponként aránya vagy a minimális, maximális és átlagos értékeit oszlopok méretét. 
>
>

> [!NOTE]
> Adatforrások, például az SQL Server Analysis Services, amelyek rendelkeznek egy első osztályú **leírás** tulajdonság, a Data Catalog adatforrás-regisztráló eszköz bontja ki az adott tulajdonság értéke. Az SQL Server rendszerű relációs adatbázisokat, amelyek nem rendelkeznek egy első osztályú **leírás** tulajdonság, a Data Catalog adatforrás-regisztráló eszköz kiolvassa az értéket a **ms_description** kiterjesztett tulajdonsága objektumok és oszlopokat. További információkért lásd: [bővített tulajdonságai párbeszédpanel használata adatbázis-objektumokon](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>Mennyi ideig kell vesz igénybe, hogy újonnan regisztrált eszközök megjelennek a katalógusban?
A Data Catalog a eszközök regisztrálása után 5 – 10 másodperc után jelennek meg a Data Catalog-portált egy adott időn lehet.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Hogyan megjegyzésekkel és a metaadatok kiegészítése a saját regisztrált adategységeket?
A legegyszerűbben úgy adja meg a metaadatok regisztrált eszközök, jelölje ki az eszközt a Data Catalog-portált, és adja meg az az értékeket a Tulajdonságok vagy séma ablaktáblán a kiválasztott objektum.

Bizonyos metaadatait szakértők és címkék, például a regisztrációs folyamat során is megadhatja. Az értékeket, akkor adja meg a Data Catalog közzétételi szolgáltatásban alkalmazása jelenleg regisztrált összes eszközt. Válassza ki, ha a közelmúltban regisztrált objektumok további megjegyzés a portálon, a **View Portal** gombra a Data Catalog adatforrás-regisztráló eszköz végső képernyőjén.

## <a name="how-do-i-delete-my-registered-data-objects"></a>A regisztrált adategységeket objektumok törlése
Jelölje ki az objektumot a portálon, majd kattintson a Data Catalog objektum segítségével törölheti a **törlése** gombra. Az objektum eltávolítása eltávolítja a metaadatait a Data Catalog, de nincs hatással az alapul szolgáló adatforrásban.

## <a name="what-is-an-expert"></a>Mi az a szakértő?
Szakértőnek az egy személy, aki rendelkezik egy tájékozott perspektíva kapcsolatos egy objektumot. Az objektum rendelkezhet több szakértői. Szakértőnek kell lennie a "tulajdonos" objektumhoz, de olyan egyszerűen személy, aki tudja, hogyan az adatok és kell használni.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>Megosztása információk a Data Catalog-csapattal Ha problémát észlel a lemezen?
Jelentse az esetleges problémákat, az információkat, és kérdései vannak, lépjen a [Azure Data Catalog fórum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>A katalógus iránt érdeklődik vagyok egy másik adatforrás működik?
Fejlesztjük további adatforrások felvétele a Data Catalog aktívan. Ha azt szeretné, tekintse meg a támogatott adott adatforrásra, azt javasoljuk (vagy hangos támogatásához, ha már javasolt) nyissa meg a [visszajelzés fórumbejegyzést Azure Data Catalog](https://feedback.azure.com/forums/906052-data-catalog).

## <a name="how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>Hogyan Azure Data Catalog kapcsolódik a Data Catalog a Power BI az Office 365?
Azure Data Catalog egy Power BI-ban a Data Catalog fejlődéséhez, tulajdonképpen. Től rugó 2017 az Azure Data Catalog ahhoz, hogy a megosztás és a felderítés a lekérdezéseket az Excel 2016 és a Power Query az Excel programhoz szolgál. Az Excel Azure Data Catalog képességei a Power BI Pro-licenccel rendelkező felhasználók számára érhetők el.

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>Milyen engedélyekkel kell eszközök regisztrálására a Data Catalog?
Az adatkatalógus-regisztráló eszköz futtatásához az adatforrásban, amely lehetővé teszi a metaadatok kiolvasása a forrás jogosultság szükséges. Előnézet is tartalmazza, lehetővé teszi az adatok a regisztrált objektumból olvasni engedélyekkel kell rendelkeznie.

A Data Catalog azt is lehetővé teszi, hogy a katalógus-rendszergazdák korlátozása, hogy mely felhasználók és csoportok adhat hozzá metaadatokat a katalógus. További információkért lásd: [biztonságossá tétele a hozzáférést a data catalog és adategységeket](data-catalog-how-to-secure-catalog.md).

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>A Data Catalog lesz elérhető, valamint a helyszíni telepítéshez?
A Data Catalog egy felhőalapú szolgáltatás, amely képes biztosítani a hibrid adatforrás felderítés megoldás a felhőalapú és a helyszíni adatforrások használható. Jelenleg nem terv a Data Catalog szolgáltatás a helyi futó verziója.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>Is további vagy szélesebb metaadatokat is kibonthat az adatforrástól érkező értesítésektől regisztrálni?
Dolgozunk a Data Catalog képességei aktívan. Ha azt szeretné, hogy a regisztrálás során az adatforrás kinyert metaadatokat, javasoljuk, (vagy az, ha már javasolt szavazás) az a [visszajelzés fórumbejegyzést Azure Data Catalog](https://feedback.azure.com/forums/906052-data-catalog). 

Ha szeretné oszlop séma metaadatok, előzetes verziójú funkciók, illetve az adatok profilok, az adatforrásokat, ahol a metaadatok nem kibontott által az adatforrás-regisztráló eszköz, a Data Catalog API segítségével adja hozzá ezeket a metaadatokat. További információkért lásd: [Azure Data Catalog REST API](https://docs.microsoft.com/rest/api/datacatalog/).

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Hogyan korlátozhatom a regisztrált adategységek láthatóságának, úgy, hogy csak bizonyos felhasználók derítheti fel azokat?
Válassza ki az adategységeket a Data Catalog, és kattintson a **tulajdonba** gombra. Adategységhez a Data Catalog tulajdonosai vagy a minden felhasználó tulajdonában lévő eszközök felderítése vagy korlátozható a láthatóságuk az egyes felhasználók számára engedélyezi a láthatóság beállítások módosításával. További információkért lásd: [az Azure Data Catalog adategységek felügyelete](data-catalog-how-to-manage.md).

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Hogyan frissíthetők a tartozó regisztráció egy adategységet, hogy az adatforrás a módosítások megjelennek a katalógusban?
A katalógus már regisztrált adategységeket metaadatainak frissítéséhez, egyszerűen regisztrálja újra az adatforrás, amely tartalmazza az eszközöket. Módosításai az adatforráshoz, például az oszlopok telepítenek vagy eltávolítja a tábla vagy nézet, frissülnek a katalógusban, de a felhasználók által biztosított jegyzetek megmaradnak.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>A fentiekben itt nem választ. Ahol folytathatja a válaszokat?
Lépjen a [Azure Data Catalog fórum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). A kérdések nem itt úton található.
