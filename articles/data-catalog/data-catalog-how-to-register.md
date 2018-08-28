---
title: Az Azure Data Catalog az adatforrások regisztrálása
description: Ez a cikk az adatforrások regisztrálása az Azure Data Catalog, beleértve a regisztrálás során kinyert metaadatokat tartalmazó mezőket emeli ki.
services: data-catalog
author: steelanddata
ms.author: maroche
ms.assetid: bab89906-186f-4d35-9ffd-61b1d903905d
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 4688b58b40df110a33f9310226db9a6412f43054
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053554"
---
# <a name="register-data-sources-in-azure-data-catalog"></a>Az Azure Data Catalog az adatforrások regisztrálása
## <a name="introduction"></a>Bevezetés
Az Azure Data Catalog egy teljes körűen felügyelt felhőszolgáltatás, amely a regisztráció és a vállalati adatforrások rendszerként. Más szóval a Data Catalog lehetővé teszi a felderítési megértésében, valamint használhatják az adatforrásokat, és segít a szervezeteknek a meglévő adatok kihasználása. Az első lépés az adatforrás a Data Catalog által felfedezhetők, hogy regisztrálja, hogy az adatforrás.

## <a name="register-data-sources"></a>Adatforrások regisztrálása
Regisztráció az a folyamat metaadatok beolvasása az adatforrásból, és az adatok másolása a Data Catalog szolgáltatásba. Az adatok helye nem változik, ahogy az sem, hogy mely rendszergazdák felügyelik az adatokat, és milyen szabályzatok érvényesek rájuk.

Egy adatforrás regisztrálása, tegye a következőket:
1. Az Azure Data Catalog-portálon a Data Catalog adatforrás-regisztráló eszköz elindításához. 
2. Jelentkezzen be a munkahelyi vagy iskolai fiókkal az Azure Active Directory hitelesítő adatokkal, használhatja a bejelentkezni a portálra.
3. Válassza ki a regisztrálni kívánt adatforrás.

További részletes információkért tekintse meg a [Ismerkedés az Azure Data Catalog](data-catalog-get-started.md) oktatóanyag.

Miután regisztrálta az adatforrás, a katalógus nyomon követi a helyét, és indexeli metaadatait. Felhasználók keresés, Tallózás, és felderíteni az adatforrásban, és a hely használatával csatlakozzon hozzá az alkalmazást vagy az általuk választott eszköz használatával.

## <a name="supported-data-sources"></a>Támogatott adatforrások
A jelenleg támogatott adatforrások listájáért lásd: [Data Catalog DSR](data-catalog-dsr.md).

## <a name="structural-metadata"></a>Szerkezeti metaadatok
Ha adatforrásként regisztrálja, a regisztrációs eszköz kinyeri a kiválasztott objektumok struktúrája kapcsolatos információkat. Ezt az információt nevezzük szerkezeti metaadatokat.

Minden objektum a szerkezeti metaadatokat, hogy a felhasználók számára az adatok használhatja ezt az információt szeretne csatlakozni az objektumot az ügyféleszközök elől az általuk választott tartalmazza az objektum tárfiókbeli helyének. Más szerkezeti metaadatokat tartalmazza az objektum neve és típusa, és attribútum vagy az oszlop neve és az adatok írja.

## <a name="descriptive-metadata"></a>A leíró metaadatok
Az adatforrás-regisztráló eszköz mellett az alapvető szerkezeti metaadatokat, amelyek ki kell olvasni az adatforrásból, kinyeri a leíró metaadatok. Az SQL Server Analysis Services és az SQL Server Reporting Services a metaadatok a leírás tulajdonságai, ezek a szolgáltatások által elérhetővé tett származik. Az SQL Server használatával a ms megadott értékek\_kiterjesztett tulajdonság leírása ki kell olvasni. Oracle Database, az adatforrás-regisztrációs eszköz kinyeri a megjegyzések oszlop az összes\_lapon\_MEGJEGYZÉSEKET nézet.

Mellett a leíró metaadatok, ki kell olvasni az adatforrásból felhasználókat adhat meg a leíró metaadatok az adatforrás-regisztráló eszköz használatával. A címkék a felhasználók hozzáadhatnak, és azonosítani tudja a regisztrálandó objektumok szakértői. A leíró metaadatok a Data Catalog szolgáltatást, és az szerkezeti metaadatokat másolja.

## <a name="include-previews"></a>Tartalmazza az előzetes verziók
Alapértelmezés szerint csak metaadatokat adatforrások kinyert, és másolja a Data Catalog szolgáltatást, de egy adatforrás gyakran könnyebbé vált a benne található adatok mintát is megtekintheti ismertetése.

A Data Catalog adatforrás-regisztrációs eszköze segítségével megadhatja az adatok pillanatképes előnézetet mindegyik táblát és nézetet, amely regisztrálva van. Ha előzetes verziók tartalmazzák a regisztráció során, a regisztrációs eszköz legfeljebb 20 bejegyzést tartalmazhat az egyes tábla és nézet is tartalmaz. A pillanatkép majd át lesznek másolva a katalógust a szerkezeti és leíró metaadatok együtt.

> [!NOTE]
> Előfordulhat, hogy nagy számú oszlopot a széles táblák 20-nál kevesebb rekordokat tartalmazza az előzetes verzióban érhető el.
>
>

## <a name="include-data-profiles"></a>Adatok profilok belefoglalása
Ugyanúgy, mint például előzetes verziók is kontextusba értékes felhasználók számára, akik keresse meg a Data Catalog az adatforrásokat, beleértve egy adatprofil még könnyebbé teheti a felderített az adatforrásokkal kapcsolatos tudnivalók.

A Data Catalog adatforrás-regisztrációs eszköze segítségével megadhatja egy adatprofil minden táblát és nézetet, amely regisztrálva van. Ha egy adatprofil belefoglalása a regisztráció során, a frissítésregisztráló eszköz adatokra vonatkozó összesített statisztikák tartalmaz, mindegyik tábla és nézet, többek között:

* Sorok és az adatok az objektum méretének száma.
* Az adatok és az objektum-sémát a legutóbbi frissítés dátuma.
* Null-rekordok és -oszlopok különböző értékek száma.
* Oszlopok minimális, maximális, átlagos és a szórást értékeit.

A statisztikai majd kerülnek át a katalógust a szerkezeti és leíró metaadatok együtt.

> [!NOTE]
> Szöveg és a dátum oszlop nem tartalmaznak átlagos vagy a szórás statisztikák az adat-profilt.
>
>

## <a name="update-registrations"></a>Regisztrációk frissítése
Adatforrás regisztrálása lehetővé teszi a Data Catalog észlelhető a metaadatok és a regisztrálás során kinyert választható előzetes verzió használata esetén. A katalógusban (például ha egy objektum sémája megváltozott, eredetileg kizárt táblák tartalmaznia kell, vagy frissíti az adatokat, amely tartalmazza az előzetes verziók) frissíteni kell az adatforrást, ha az adatforrás-regisztráló eszköz újból futtatható.

Egy már regisztrált adatforrás regisztrálása merge "upsert" műveletet hajt végre: meglévő objektumok frissülnek, és új objektumokat hoz létre. A Data Catalog-portálon keresztül a felhasználó által megadott összes metaadat megmaradnak.

## <a name="summary"></a>Összegzés
Mert szerkezeti és leíró metaadatokat egy adatforrásból a katalógus szolgáltatás másolja, a Data Catalog az adatforrás regisztrálása, könnyebben megtalálhatóvá és értelmezhetővé. Miután regisztrálta az adatforrás, jegyzettel láthatja el, kezelheti és Fedezze fel, a Data Catalog-portál használatával.

## <a name="next-steps"></a>További lépések
Az adatforrások nyilvántartására vonatkozó további információkért tekintse meg a [Ismerkedés az Azure Data Catalog](data-catalog-get-started.md) oktatóanyag.
