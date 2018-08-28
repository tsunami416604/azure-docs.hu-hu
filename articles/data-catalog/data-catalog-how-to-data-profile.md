---
title: Az Azure Data Catalog az adatforrásokat profilkészítési adatok használata
description: Útmutató a cikk kiemelése, tábla és oszlop szintű adatok profilok belefoglalása, amikor az Azure Data Catalog az adatforrások regisztrálása, és hogyan az adatforrásokkal kapcsolatos tudnivalók a data-profilok használatával.
services: data-catalog
author: spelluru
ms.author: spelluru
ms.assetid: 94a8274b-5c9c-4962-a4b1-2fed38a3d919
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 2f628367e2e5b0e6a4481a6212ff201b11a7105a
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053797"
---
# <a name="data-profile-data-sources"></a>Adatforrások adatprofiljának készítése
## <a name="introduction"></a>Bevezetés
**A Microsoft Azure Data Catalog** egy teljes körűen felügyelt felhőszolgáltatás, amely egy regisztrációs és felderítőrendszert biztosít a vállalati adatforrások. Más szóval **Azure Data Catalog** lényege személyek felderítése megértésében, valamint használhatják az adatforrásokat, és segíti a szervezetek számára, hogy a meglévő adatok kihasználása. Ha egy adatforrás regisztrálva **Azure Data Catalog**, másolt és a szolgáltatás indexeli metaadatait, de a történetet ott nem végződhet.

A **profilkészítési adatok** funkcióját **Azure Data Catalog** megvizsgálja a katalógusban található támogatott adatforrások származó adatok és statisztikák és adatok adatait gyűjti. Könnyedék tartalmazza az adatvagyon egy profilt. Amikor regisztrál egy adategységet, válassza ki a **Adatprofil belefoglalása** az adatforrás-regisztráló eszköz található.

## <a name="what-is-data-profiling"></a>Mit jelent a profilkészítési adatokat
Adatok profilkészítés megvizsgálja az adatforrás regisztrálása folyamatban lévő adatok és statisztikák és adatok adatait gyűjti. Adatforrás-felderítés során a statisztikai segítségével meghatározhatja az adatok az üzleti probléma megoldására megfelelőségét.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

A következő adatforrások támogatják a profilkészítési adatokat:

* Az SQL Server (például Azure SQL Database és az Azure SQL Data Warehouse) táblák és nézetek
* Oracle-táblák és nézetek
* Teradata-táblák és nézetek
* Hive-táblákban

Adategységek regisztrálása segít a felhasználóknak például adatok-profilok válaszoljon a kérdésekre, adatforrások, például:

* Ez használható saját üzleti probléma megoldására?
* Felel meg az adatok adott szabványok és minták?
* Mik az adatforrás az anomáliákat?
* Mik azok a lehetséges kihívásokat, ezek az adatok integrálása saját alkalmazásba?

> [!NOTE]
> Dokumentáció is az adategység ismertetik, hogyan adatok sikerült integrálható egy alkalmazást adhat hozzá. Lásd: [adatforrások dokumentálása](data-catalog-how-to-documentation.md).
>
>

<a name="howto"/>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Egy adatprofil belefoglalása, amikor egy adatforrás regisztrálása
Könnyedék tartalmazza az adatforrás egy profilt. Amikor regisztrál egy adatforráshoz, az a **regisztrálandó objektumok** panelen válassza az adatforrás-regisztráló eszköz, **Adatprofil belefoglalása**.

![](media/data-catalog-data-profile/data-catalog-register-profile.png)

Adatforrások regisztrálása kapcsolatos további tudnivalókért lásd: [adatforrások regisztrálása](data-catalog-how-to-register.md) és [Ismerkedés az Azure Data Catalog](data-catalog-get-started.md).

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Az adategységek, beleértve az adatok profilok szűrése
Adategységeket, amely egy adatprofil belefoglalása, hozzáadhatja a `has:tableDataProfiles` vagy `has:columnsDataProfiles` egyik a keresési kifejezéseit.

> [!NOTE]
> Kiválasztásával **Adatprofil belefoglalása** az adatforrás frissítésregisztráló eszköz tartoznak a tábla és oszlopszintű profil adatait. Azonban a Data Catalog API lehetővé teszi, hogy csak egy készletét tartalmazza profiladatok regisztrálni adategységeket.
>
>

## <a name="viewing-data-profile-information"></a>Adatok Profiladatok megtekintése
Miután megtalálta a megfelelő adatforrás egy profilt, megtekintheti az adatok profil adatai. Az adatok profil megtekintéséhez kattintson egy adategységet, és válassza **Adatprofil** a Data Catalog portál ablakban.

![](media/data-catalog-data-profile/data-catalog-view.png)

Az adatok profil **Azure Data Catalog** jeleníti meg a tábla és oszlop profil adatok, többek között:

### <a name="object-data-profile"></a>Objektum adatprofil
* Sorok száma
* Szolgáltatástáblázat mérete
* Az objektum utolsó frissítésekor

### <a name="column-data-profile"></a>Oszlopprofil adatok
* Oszlop adattípusa
* Eltérő értékek száma
* NULL értéket tartalmazó sorok száma
* Minimum, maximum, átlagos és -értékek szórásának

## <a name="summary"></a>Összegzés
Profilkészítési adatok statisztikák és a regisztrált adategységeket a alkalmasságát az üzleti problémák adatok meghatározásához kapcsolatos információkat biztosít. Jegyzetkészítés és adatforrások címkézésével, valamint adatok profilok adhat a felhasználóknak jobban megértheti az adatokat.

## <a name="see-also"></a>Lásd még:
* [Adatforrások regisztrálása](data-catalog-how-to-register.md)
* [Ismerkedés az Azure Data Catalog szolgáltatással](data-catalog-get-started.md)
