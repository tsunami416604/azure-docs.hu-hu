---
title: Az Azure Data Catalog terminológiája
description: Ez a cikk az Azure Data Catalog dokumentációjában használt fogalmak és kifejezések bemutatása.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: d6c813dec3922334f7462b1226ea22371fd5f43b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736292"
---
# <a name="azure-data-catalog-terminology"></a>Az Azure Data Catalog terminológiája

Ez a cikk az Azure Data Catalog dokumentációjában használt fogalmak és kifejezések bemutatása.

## <a name="catalog"></a>Katalógus

Az Azure Data Catalog egy felhőalapú metaadat-tárház, amelyben adatforrások és adateszközök regisztrálhatók. A katalógus központi tárolási helyként szolgál az adatforrásokból kinyert szerkezeti metaadatokhoz és a felhasználók által hozzáadott leíró metaadatokhoz.

## <a name="data-source"></a>Adatforrás

Az adatforrás olyan rendszer vagy tároló, amely az adateszközöket kezeli. Ilyenek például az SQL Server-adatbázisok, az Oracle-adatbázisok, az SQL Server Analysis Services-adatbázisok (táblázatos vagy többdimenziós) és az SQL Server Reporting Services kiszolgálói.

## <a name="data-asset"></a>Adateszköz

Az adateszközök olyan adatforrásokon belüli objektumok, amelyek regisztrálhatók a katalógusban. Ilyenek például az SQL Server táblák és nézetek, az Oracle táblák és nézetek, az SQL Server Analysis Services mezeinek, a dimenzióknak és a kpi-knek, valamint az SQL Server Reporting Services jelentései.

## <a name="data-asset-location"></a>Adateszköz helye

A katalógus tárolja egy adatforrás vagy adateszköz helyét, amely segítségével ügyfélalkalmazással csatlakozhat a forráshoz. A hely formátuma és részletei az adatforrás típusától függően változnak. Például egy SQL Server tábla a négy részes neve – kiszolgálónév, adatbázisnév, sémanév, objektumnév – alapján azonosítható, míg az SQL Server Reporting Services jelentése az URL-címe alapján azonosítható.

## <a name="structural-metadata"></a>Szerkezeti metaadatok

A szerkezeti metaadatok az adatforrásból kinyert metaadatok, amelyek egy adateszköz szerkezetét írják le. Ez magában foglalja az eszközök helyét, az objektum nevét és típusát, valamint további típusspecifikus jellemzőket. A táblák és nézetek szerkezeti metaadatai például tartalmazzák az objektum oszlopainak nevét és adattípusát.

## <a name="descriptive-metadata"></a>Leíró metaadatok

A leíró metaadatok olyan metaadatok, amelyek az adateszköz célját vagy szándékát írják le. Általában leíró metaadatok at akatalógus-felhasználók az Azure Data Catalog portálon, de azt is kinyerhető az adatforrásból a regisztráció során. Például az Azure Data Catalog regisztrációs eszköz kinyeri a leírásokat a Description tulajdonság az SQL Server Analysis Services és az SQL Server Reporting Services, és a [ms_description kiterjesztett tulajdonság](https://technet.microsoft.com/library/ms190243.aspx) az SQL Server-adatbázisok, ha ezek a tulajdonságok értékekkel lett feltöltve.

## <a name="request-access"></a>Hozzáférés kérése

Az adategység leíró metaadatai információkat tartalmazhatnak az adateszközhöz vagy adatforráshoz való hozzáférés kérésével kapcsolatban. Ez az információ az adateszköz helyével együtt jelenik meg, és az alábbi lehetőségek közül egyet vagy többet tartalmazhat:

* Az adatforráshoz való hozzáférés biztosításáért felelős felhasználó vagy csoport e-mail címe.
* A dokumentált folyamat URL-címe, amelyet a felhasználóknak követniük kell az adatforráshoz való hozzáféréshez.
* Az adatforráshoz való hozzáféréshez használható identitás- és hozzáférés-kezelő eszköz (például a Microsoft Identity Manager) URL-címe.
* Szabadszöveges bejegyzés, amely leírja, hogy a felhasználók hogyan férhetnek hozzá az adatforráshoz.

## <a name="preview"></a>Előzetes verzió

Az Azure Data Catalog előzetes verziója legfeljebb 20 rekord pillanatképe, amely a regisztráció során kinyerhető az adatforrásból, és a katalógusban tárolható az adateszköz metaadataival. Az előzetes verzió segítségével az adategységet felderítő felhasználók jobban megérthetik annak funkcióját és célját. Más szóval a mintaadatok megjelenítése értékesebb lehet, mint az oszlopnevek és adattípusok megjelenítése.
Az előnézetek csak táblák és nézetek esetén támogatottak, és a regisztráció során a felhasználónak explicit módon ki kell választania azokat.

## <a name="data-profile"></a>Adatprofil

Az Azure Data Catalog adatprofilja egy táblázatszintű és oszlopszintű metaadatok pillanatképe egy regisztrált adateszközről, amely a regisztráció során kinyerhető az adatforrásból, és a katalógusban tárolható az adateszköz metaadataival. Az adatprofil segítségével az adategységet felderítő felhasználók jobban megérthetik annak funkcióját és célját. Az előzetesekhez hasonlóan az adatprofilokat a felhasználónak is explicit módon ki kell választania a regisztráció során.

> [!NOTE]
> Az adatprofil kinyerése költséges művelet lehet nagy táblák és nézetek esetén, és jelentősen megnövelheti az adatforrás regisztrálásához szükséges időt.


## <a name="user-perspective"></a>Felhasználói perspektíva

Az Azure Data Catalog ban bármely felhasználó leíró metaadatokat adhat meg egy regisztrált adateszközhöz. Minden felhasználónak külön perspektívája van az adatokról és azok felhasználásáról. A kiszolgálóért felelős rendszergazda például megadhatja a szolgáltatásiszint-szerződés (SLA) vagy a biztonsági mentési ablakok részleteit; az adatgondnok hivatkozásokat adhat az adatok által támogatott üzleti folyamatok dokumentációjához; és az elemző olyan leírásokat adhat meg, amelyek a leginkább relevánsak más elemzők számára, és amelyek a legértékesebbek lehetnek azoknak a felhasználóknak, akiknek fel kell fedezniük és meg kell érteniük az adatokat.

Ezek a perspektívák mindegyike eredendően értékes, és az Azure Data Catalog minden felhasználó számára értelmes információkat adhat meg, míg minden felhasználó használhatja ezeket az információkat az adatok és azok céljának megértéséhez.

## <a name="expert"></a>Szakértő

A szakértő olyan felhasználó, akiről megállapították, hogy tájékozott "szakértői" perspektívával rendelkezik egy adateszközhöz. Bármely felhasználó hozzáadhatja magát vagy egy másik felhasználót egy eszköz szakértőjeként. Ha szakértőként szerepel, az nem közvetít további jogosultságokat az Azure Data Catalogban; ez megenged használók -hoz könnyen elhelyez azok perspektíva amit van a leg--bb valószínű -hoz lenni hasznos mikor felülvizsgáló egy eszköz' leíró metadata.

## <a name="owner"></a>Tulajdonos

A tulajdonos olyan felhasználó, aki további jogosultságokkal rendelkezik egy adateszköz kezeléséhez az Azure Data Catalogban. A felhasználók átvehetik a regisztrált adateszközök tulajdonjogát, és a tulajdonosok társtulajdonosként más felhasználókat is hozzáadhatnak. További információ: [Az adateszközök kezelése](data-catalog-how-to-manage.md)  

> [!NOTE]
> A tulajdonjog és a felügyelet csak az Azure Data Catalog standard kiadásában érhető el.

## <a name="registration"></a>Regisztráció

A regisztráció az adateszköz-metaadatok kinyerése egy adatforrásból, és másolja az Azure Data Catalog szolgáltatásba. A regisztrált adateszközök ezután jegyzetekkel egészíthetők ki és fedezhetők fel.

## <a name="next-steps"></a>További lépések

[Rövid útmutató: Azure-adatkatalógus létrehozása](data-catalog-get-started.md) 
