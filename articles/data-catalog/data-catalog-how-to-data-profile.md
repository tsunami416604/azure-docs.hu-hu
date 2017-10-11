---
title: "Hogyan adatok profil adatforrások"
description: "Útmutató a cikk adatforrások regisztrálása az Azure Data Catalog tábla és oszlop szintű adatok profilok szerepeljen, és adatok profilok használható az adatforrások megértéséhez kiemelve."
services: data-catalog
documentationcenter: 
author: spelluru
manager: NA
editor: 
tags: 
ms.assetid: 94a8274b-5c9c-4962-a4b1-2fed38a3d919
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/03/2017
ms.author: spelluru
ms.openlocfilehash: 8f4174f0ed74706b8275c8b1f0a62753f2834fa2
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="data-profile-data-sources"></a>Adatforrások adatprofiljának készítése
## <a name="introduction"></a>Bevezetés
**A Microsoft Azure Data Catalog** egy teljes körűen felügyelt felhőszolgáltatás, amely a regisztráció és a rendszer a vállalati adatforrások felderítési funkcionál. Más szóval **Azure Data Catalog** minden útmutatás nyújtása a felhasználók felderítése, megismeréséhez és használatához adatforrások, és segíti a szervezeteket kihasználása érdekében a meglévő adatok körül forog. Ha egy adatforrás regisztrálva van **Azure Data Catalog**, a metaadatai másolt és a szolgáltatás indexelni, de a szövegegység nincs nem végződhet.

A **adatok profilkészítési** szolgáltatása **Azure Data Catalog** megvizsgálja-e a támogatott adatforrások a katalógusban szereplő adatok és statisztikák és adatok információt gyűjt. Nem tartalmazza az adategységet egy profilt. Amikor regisztrál egy adategységet, válassza ki a **adatok profillal együtt** az az adatforrás-regisztráló eszköz.

## <a name="what-is-data-profiling"></a>Mi az a profilkészítési adatokat
Profilkészítési adatokat a regisztrált adatforrás adatokat vizsgálja, és statisztikák és adatok információt gyűjt. Adatforrás-felderítés során a statisztikai információk segítségével meghatározhatja, hogy az adatok az üzleti probléma megoldására megfelelőségét.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

A következő adatforrások támogatja a profilkészítési adatokat:

* SQL Server (beleértve az Azure SQL Database és Azure SQL Data Warehouse) táblák és nézetek
* Oracle-táblák és nézetek
* Teradata-táblák és nézetek
* Hive táblák

Adategységek regisztrálása segít a felhasználóknak többek között a következőket adatokat-profilok az adatforrásokról, beleértve a kérdések megválaszolása:

* Ez használható a saját üzleti probléma megoldására?
* Felel meg az adatok adott szabványoknak vagy minták?
* Melyek azok a adatforrás rendellenességeket?
* Mik azok a lehetséges kihívásai ezeket az adatokat integrálása az alkalmazásban?

> [!NOTE]
> Dokumentáció is az eszköz ismertetik, hogyan adatok sikerült integrálni kell egy alkalmazást adhat hozzá. Lásd: [adatforrások dokumentálása](data-catalog-how-to-documentation.md).
>
>

<a name="howto"/>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Hogyan adatok profil tartalmazhat, ha egy adatforrás regisztrálása
Nem tartalmazza az adatforrás egy profilt. Amikor regisztrál egy adatforrást, a **regisztrálandó objektumok** panelen válassza ki az adatforrás-regisztráló eszköz, **adatok profillal együtt**.

![](media/data-catalog-data-profile/data-catalog-register-profile.png)

Adatforrások regisztrálása kapcsolatos további információkért lásd: [adatforrások regisztrálása](data-catalog-how-to-register.md) és [Ismerkedés az Azure Data Catalog](data-catalog-get-started.md).

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Szűrést az adategységek, amely tartalmazza az adat-profilok
Adategységeket, amely tartalmazza az adatok profil, megadhat `has:tableDataProfiles` vagy `has:columnsDataProfiles` rendelkezésre álló a keresési feltételeket.

> [!NOTE]
> Kiválasztása **adatok profillal együtt** az adatforrás frissítésregisztráló eszköz tartalmazza a tábla és a oszlopszintű profillal kapcsolatos információk. Azonban a Data Catalog API lehetővé teszi, hogy lehet regisztrálni a profil kacsolódjanak csak egy készletét adategységeket.
>
>

## <a name="viewing-data-profile-information"></a>Adatok profil információk megtekintése
Miután megtalálta a megfelelő adatforrás profillal, megtekintheti az adatok profil részleteit. Az adatok profil megtekintéséhez jelöljön ki egy adategységet, majd válassza **adatok profil** a Data Catalog-portál ablakban.

![](media/data-catalog-data-profile/data-catalog-view.png)

Az adatok profil **Azure Data Catalog** jeleníti meg a tábla és oszlop profil információkat, így:

### <a name="object-data-profile"></a>Objektum adatok profil
* A sorok számát
* A táblázat mérete
* Az objektum utolsó frissítésekor

### <a name="column-data-profile"></a>Oszlop adatok profil
* Adattípus oszlop
* A distinct érték
* NULL értéket tartalmazó sorok száma
* Minimum, maximum, átlagos és -értékek szórásának

## <a name="summary"></a>Összefoglalás
Profilkészítési adatokat biztosít a statisztikák és a regisztrált adategységeket segítségével meghatározhatja, hogy az adatok üzleti problémák megoldására való megfelelőségével kapcsolatos információk. Ellátása megjegyzésekkel és adatforrások dokumentálása, valamint adatokat profilok adhat a felhasználóknak bemutatják, az adatokat.

## <a name="see-also"></a>Lásd még:
* [Adatforrások regisztrálása](data-catalog-how-to-register.md)
* [Ismerkedés az Azure Data Catalog szolgáltatással](data-catalog-get-started.md)
