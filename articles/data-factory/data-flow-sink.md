---
title: Az Azure Data Factory-folyamat fogadó adatátalakítás leképezése
description: Az Azure Data Factory-folyamat fogadó adatátalakítás leképezése
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: dba043721c2d81b7fe2c254f62328e54bb959cdc
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729372"
---
# <a name="mapping-data-flow-sink-transformation"></a>Hozzárendelés fogadó folyamat átalakítását

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Fogadó-beállítások](media/data-flow/windows1.png "fogadó 1")

Az Adatátalakítási folyamat befejezése után az átalakított adatok is fogadó célkiszolgáló adatkészletekbe. A fogadó átalakítási választhatja ki, amely a cél kimeneti adatok használni kívánt adatkészlet-definícióban. Előfordulhat, hogy annyi fogadó átalakítást, az adatfolyam igényel.

Egy fiókra a bejövő adatok módosítása és a számla séma eltéréseket az általános gyakorlat, hogy a kimeneti adatokat fogadó egy mappába a kimeneti adatkészletben definiált séma nélkül. Meg is emellett a fiók az összes oszlop változáshoz a forrásokban "Engedélyezése séma eltéréseket" a forrásnál, és a automatikus hozzárendelés kijelölésével a fogadó található összes mezőhöz.

Ha szeretné, felülírja, hozzáfűzése, vagy az adatok az adatfolyam sikertelen, ha az Elhelyezés adatkészletre.

Választhatja "automatikus hozzárendelés" fogadó minden bejövő mezőt is. Ha szeretné, hogy a cél fogadó kívánt mezők kiválasztása, vagy ha szeretné módosítani a célhely a mezők nevében, válassza a "automatikus"hozzárendelés "Off", és a kimeneti mezők leképezése a leképezési lapon kattintson:

![Fogadó-beállítások](media/data-flow/sink2.png "fogadó 2")

## <a name="output-to-one-file"></a>Egy fájl kimeneti
Az Azure Storage Blob vagy a Data Lake fogadó típusait az átalakított adatokat fog kimeneti mappába. A Spark használt particionálási séma alapján particionált kimeneti adatfájlokat hoz létre a fogadó átalakítása. Beállíthatja a particionálási sémát az "Optimalizálás" fülre kattintva. Ha szeretné, hogy a kimeneti egyesíthet egyetlen fájl ADF, kattintson a "Egypartíciós" gombra.

![Fogadó-beállítások](media/data-flow/opt001.png "fogadó-beállítások")

### <a name="output-settings"></a>Kimeneti beállításai

Felülírással csonkolja a táblából, ha létezik, hozza létre újra, és betöltheti az adatokat. Fűzze hozzá a rendszer az új sor beszúrásához. Ha az adatkészlet-tábla neve a tábla nem létezik egyáltalán a célzott ADW, annak adatfolyam a tábla létrehozásához, majd az adatok betöltéséhez.

Ha törli az "Automatikus térkép", leképezheti a mezőket a céltábla manuálisan.

![Fogadó ADW beállítások](media/data-flow/adw2.png "adw fogadó")

#### <a name="field-mapping"></a>Mezőleképezés

A fogadó átalakítást leképezés lapján a bejövő (bal oldal) oszlopok leképezheti a célhelyre (a jobb oldalon). Adatfolyam-gyűjteményre, fogadó-fájlokhoz, amikor ADF mindig írni új fájlokat egy mappában. Ha leképez egy adatbázis-adatkészletet, dönthet úgy, vagy létrehozhat egy új táblát a séma ("felülírása" mentési szabályzat beállítása), vagy új sor beszúrása egy meglévő táblát, és a mezők leképezése a meglévő sémák.

Válassza ki a hozzárendelési táblában használhatja a hivatkozás több oszlop egyetlen kattintással, több oszlop csatolásának megszüntetése vagy több sort leképezése oszlop nevével megegyező névre.

![Mezőt leképező](media/data-flow/multi1.png "többféle lehetőség")

Ha szeretne alaphelyzetbe állítani az oszlop-hozzárendelések, nyomja le az "Újramegfeleltetése" gombra a hozzárendelések visszaállítása.

![Kapcsolatok](media/data-flow/maxcon.png "kapcsolatok")

### <a name="updates-to-sink-transformation-for-adf-v2-ga-version"></a>A fogadó átalakításában szerepel az ADF V2 általánosan elérhető verziót frissíti

![Fogadó-beállítások](media/data-flow/sink1.png "egy fogadó")

![Fogadó-beállítások](media/data-flow/sink2.png "fogadók")

* Lehetővé teszi a séma eltéréseket, és a séma érvényesítése, mostantól elérhetők fogadó. Ez lehetővé teszi, hogy teljes mértékben fogadja el a rugalmas sémadefiníciók (séma eltéréseket) vagy a fogadó sikertelen lesz, ha a séma módosul (séma érvényesítése) az ADF utasíthatja.

* Törölje a mappát. Az ADF csonkolja a fogadó mappa tartalmának írása a cél-fájlokat a célmappában előtt.

* Fájlnév beállításai

   * Alapértelmezett: Fájlok elnevezésére rész alapértelmezett értékei szerinti Spark engedélyezése
   * Pattern: Adja meg a kimeneti fájlok nevét
   * Partíciónként: Írjon be egy fájlnevet partíciónként
   * Oszlop adatként: Egy oszlop értékét állítsa a kimeneti fájl

> [!NOTE]
> Fájlműveletek csak akkor futnak, amikor az adatok folyamat végrehajtása tevékenység nem a Data Flow hibakeresési módban futtatja

Az SQL-fogadó típusú állíthatja be:

* Tábla csonkolása
* Hozza létre újból táblában (hajt végre közvetlen/létrehozása)
* Köteg mérete nagy mennyiségű adat terhelés. Adjon meg egy számot és gyűjtőbe írási adattömböket.

![Mezőleképezés](media/data-flow/sql001.png "SQL-beállítások")

## <a name="next-steps"></a>További lépések

Most, hogy létrehozta az adatfolyamot, adjon hozzá egy [tevékenységet a folyamat végrehajtása az adatfolyam](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-overview).
