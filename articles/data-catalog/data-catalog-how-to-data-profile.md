---
title: Adatforrások adatforrásainak használata a Azure Data Catalogban
description: 'Útmutató: a táblázatos és az oszlop szintű adatprofilok bevonása az adatforrások Azure Data Catalogban való regisztrálásakor, valamint az adatprofilok használata az adatforrások megértéséhez.'
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 4223d310bc747a518abaeadbbb467aa44871578f
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882516"
---
# <a name="data-profile-data-sources"></a>Adatforrások adatprofiljának készítése

## <a name="introduction"></a>Bevezetés

A **Microsoft Azure Data Catalog** egy teljes körűen felügyelt felhőalapú szolgáltatás, amely a vállalati adatforrások regisztrálási és felderítési rendszereként szolgál. Ez azt jelenti, hogy **Azure Data Catalog** az a célja, hogy segítsen az embereknek felderíteni, megérteni és használni az adatforrásokat, és hogy segítse a szervezeteket abban, hogy a meglévő adatokból több értéket kapjanak. Ha egy adatforrás regisztrálva van **Azure Data Catalog**, a metaadatokat a szolgáltatás átmásolja és indexeli, de a történet nem fejeződik be.

**Azure Data Catalog** adatprofilkészítési funkciója megvizsgálja a katalógusban található támogatott adatforrásokból származó adatokat, és statisztikai adatokat és információkat gyűjt az adatokról. Könnyedén belefoglalhatja az adategységek profilját. Adategység regisztrálása esetén válassza az **Adatprofil** belefoglalása lehetőséget az adatforrás-regisztrációs eszközben.

## <a name="what-is-data-profiling"></a>Mi az az adatprofilkészítés?

Az adatok profilkészítése megvizsgálja az adatforrásban regisztrált adatokat, és statisztikai adatokat és információkat gyűjt az adatokról. Az adatforrás-felderítés során ezek a statisztikák segíthetnek meghatározni az adatok alkalmasságát az üzleti problémák megoldására.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

A következő adatforrások támogatják az adatprofilkészítést:

* SQL Server (beleértve az Azure SQL DB és a Azure SQL Data Warehouse) táblázatokat és nézeteket
* Oracle-táblák és nézetek
* Teradata táblák és nézetek
* Struktúra táblái

Az adategységek regisztrálásakor a felhasználók az adatforrásokkal kapcsolatos kérdésekre válaszolnak, beleértve az alábbiakat:

* Használható az üzleti problémák megoldására?
* Megfelel az adatok bizonyos szabványoknak vagy mintázatoknak?
* Mi az adatforrás anomália?
* Mik az adatok az alkalmazásba való integrálásának lehetséges kihívásai?

> [!NOTE]
> Emellett dokumentációt is hozzáadhat egy eszközhöz, amely leírja, hogyan integrálható az alkalmazások egy alkalmazásba. Lásd: [az](data-catalog-how-to-documentation.md)adatforrások dokumentálása.
>

<a name="howto"/>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Adatprofil belefoglalása az adatforrás regisztrálásakor

Könnyedén belefoglalhatja az adatforrás profilját. Amikor regisztrál egy adatforrást, az adatforrás-regisztrációs eszköz regisztrálásához szükséges **objektumok** panelen válassza az adatprofil belefoglalása lehetőséget.

![Adatprofil befoglalása jelölőnégyzet](media/data-catalog-data-profile/data-catalog-register-profile.png)

Az adatforrások regisztrálásával kapcsolatos további tudnivalókért lásd: [](data-catalog-how-to-register.md) az adatforrások regisztrálása és [Az Azure Data Catalog első lépései](data-catalog-get-started.md).

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Olyan adategységek szűrése, amelyek adatprofilokat tartalmaznak

Ha olyan adategységeket szeretne felderíteni, amelyek adatprofilokat `has:columnsDataProfiles` tartalmaznak, a keresési kifejezéseket is megadhatja `has:tableDataProfiles` .

> [!NOTE]
> Ha az adatprofil belefoglalása lehetőséget választja az adatforrás regisztrálása eszközben, akkor a tábla és az oszlop szintű profilra vonatkozó információk is szerepelnek. A Data Catalog API azonban lehetővé teszi, hogy az adategységek csak egy profil-információval legyenek regisztrálva.
>

## <a name="viewing-data-profile-information"></a>Az adatprofil adatainak megtekintése

Miután megtalálta a megfelelő adatforrást egy profillal, megtekintheti az adatprofil részleteit. Az adatprofil megtekintéséhez válassza ki az adategységet, és válassza az adatprofil lehetőséget a Data Catalog-portál ablakban.

![Adatprofil lap](media/data-catalog-data-profile/data-catalog-view.png)

A **Azure Data Catalog** adatprofilja a táblázat és az oszlop profiljának adatait jeleníti meg, beleértve a következőket:

### <a name="object-data-profile"></a>Objektum adatprofilja

* Sorok száma
* Táblázat mérete
* Az objektum legutóbbi frissítésekor

### <a name="column-data-profile"></a>Oszlop adatprofilja

* Oszlop adattípusa
* Eltérő értékek száma
* NULL értékű sorok száma
* Az oszlopok értékeinek minimális, maximális, átlagos és szórásos szórása

## <a name="summary"></a>Összegzés

Az adatok profilkészítése statisztikai adatokat és információkat biztosít a regisztrált adategységekről, amelyek segítségével meghatározhatja az üzleti problémák megoldásához szükséges adatok megfelelőségét. A jegyzetek és az adatforrások dokumentálása mellett az adatprofilok mélyebb ismereteket biztosíthatnak a felhasználóknak az adatairól.

## <a name="see-also"></a>Lásd még:

* [Adatforrások regisztrálása](data-catalog-how-to-register.md)
* [Ismerkedés az Azure Data Catalog szolgáltatással](data-catalog-get-started.md)
