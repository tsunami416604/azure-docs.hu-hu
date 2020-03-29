---
title: Adatprofil-készítő adatforrások használata az Azure Data Catalogban
description: Útmutató cikk, amely kiemeli, hogyan lehet tábla- és oszlopszintű adatprofilokat felvenni az adatforrások Azure Data Catalogban történő regisztrálásakor, és hogyan használhatja az adatprofilokat az adatforrások megértéséhez.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 04ac6c2bf0137289221a4ae6ed58d5a71ad21739
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68950217"
---
# <a name="how-to-data-profile-data-sources-in-azure-data-catalog"></a>Adatprofil-adatforrások az Azure Data Catalogban

## <a name="introduction"></a>Bevezetés

**A Microsoft Azure Data Catalog** egy teljes körűen felügyelt felhőszolgáltatás, amely a vállalati adatforrások regisztrációs és felderítési rendszereként szolgál. Más szóval az **Azure Data Catalog** arról szól, hogy segítsen az embereknek az adatforrások felderítésében, megértésében és használatában, és segítse a szervezeteket abban, hogy nagyobb értéket kapjanak a meglévő adataikból. Ha egy adatforrás regisztrálva van az **Azure Data Catalog-ban,** a rendszer másolja és indexeli a metaadatait a szolgáltatás, de a történet nem ér véget.

Az Azure Data **Catalog** **adatprofilozási** funkciója megvizsgálja a katalógusban szereplő támogatott adatforrásokból származó adatokat, és statisztikákat és információkat gyűjt az adatokról. Az adatelemek profilját egyszerűen felveheti. Amikor regisztrál egy adategységet, válassza az **Adatprofil felvétele lehetőséget** az adatforrás-regisztrációs eszközben.

## <a name="what-is-data-profiling"></a>Mi az adatprofilkészítés?

Az adatprofilkészítés megvizsgálja a regisztrálandó adatforrásban lévő adatokat, és statisztikákat és információkat gyűjt az adatokról. Az adatforrás-felderítés során ezek a statisztikák segíthetnek meghatározni, hogy az adatok alkalmasak-e az üzleti probléma megoldására.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

A következő adatforrások támogatják az adatprofilkészítést:

* SQL Server (beleértve az Azure SQL DB-t és az Azure SQL Data Warehouse-t) táblák és nézetek
* Oracle táblázatok és nézetek
* Teradata-táblázatok és -nézetek
* Hive-táblák

Az adateszközök regisztrálásakor az adatprofilok belefoglalása segít a felhasználóknak megválaszolni az adatforrásokkal kapcsolatos kérdéseket, többek között az:

* Lehet használni, hogy megoldja az üzleti probléma?
* Megfelelnek-e az adatok bizonyos szabványoknak vagy mintáknak?
* Milyen anomáliák az adatforrás?
* Milyen lehetséges kihívások at integrálja ezeket az adatokat az alkalmazásomba?

> [!NOTE]
> Egy eszközhöz dokumentációt is hozzáadhat, amely leírja, hogyan integrálhatók az adatok egy alkalmazásba. Lásd: [Adatforrások dokumentálása](data-catalog-how-to-documentation.md).
>

<a name="howto"/>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Adatprofil felvétele adatforrás regisztrálásakor

Az adatforrás profiljának egyszerű. Adatforrás regisztrálásakor az adatforrás-regisztrációs eszköz **Rögzítendő objektumok** panelén válassza az **Adatprofil felvétele**lehetőséget.

![Adatprofillal együtt jelölőnégyzet](media/data-catalog-data-profile/data-catalog-register-profile.png)

Az adatforrások regisztrálásáról az [Adatforrások regisztrálása](data-catalog-how-to-register.md) és [az Azure Data Catalog – első lépések](data-catalog-get-started.md)ebben a témakörben olvashat bővebben.

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Adatprofilokat tartalmazó adateszközök szűrése

Az adatprofilt tartalmazó adatelemek felderítéséhez `has:tableDataProfiles` `has:columnsDataProfiles` felveheti a keresési kifejezések egyikét, vagy egy keresési kifejezésként.

> [!NOTE]
> Az Adatforrás-regisztrációs eszköz **Adatprofil felvétele** jelölőnégyzetbe foglalása tábla- és oszlopszintű profiladatokat is tartalmaz. A Data Catalog API azonban lehetővé teszi az adateszközök regisztrálását, és csak egy profiladat-készlet et tartalmaz.
>

## <a name="viewing-data-profile-information"></a>Adatprofil adatainak megtekintése

Miután megtalálta a megfelelő adatforrást egy profillal, megtekintheti az adatprofil részleteit. Az adatprofil megtekintéséhez jelöljön ki egy adateszközt, és válassza az **Adatprofil** lehetőséget a Data Catalog portál ablakában.

![Adatprofil lap](media/data-catalog-data-profile/data-catalog-view.png)

Az Azure **Data Catalog** adatprofilja a táblázat- és oszlopprofil-adatokat jeleníti meg, többek között a következőket:

### <a name="object-data-profile"></a>Objektumadat-profil

* Sorok száma
* Táblázat mérete
* Az objektum utolsó frissítésének időpontja

### <a name="column-data-profile"></a>Oszlop adatprofilja

* Oszlop adattípusa
* Különböző értékek száma
* NULL értékkel rendelkező sorok száma
* Az oszlopértékek minimális, maximális, átlagos és szórása

## <a name="summary"></a>Összefoglalás

Az adatprofilkészítés statisztikákat és információkat nyújt a regisztrált adateszközökről, hogy segítsen meghatározni, hogy az adatok alkalmasak-e az üzleti problémák megoldására. Az adatforrások jegyzetelése és dokumentálása mellett az adatprofilok segítségével a felhasználók jobban megérthetik az adatokat.

## <a name="see-also"></a>Lásd még:

* [Adatforrások regisztrálása](data-catalog-how-to-register.md)
* [Ismerkedés az Azure Data Catalog szolgáltatással](data-catalog-get-started.md)
