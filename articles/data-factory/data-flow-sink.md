---
title: Az Azure Data Factory-folyamat fogadó adatátalakítás leképezése
description: Az Azure Data Factory-folyamat fogadó adatátalakítás leképezése
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: a39fa0949276b7e86c7fdd0d0861492a9a0b723e
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438632"
---
# <a name="mapping-data-flow-sink-transformation"></a>Hozzárendelés fogadó folyamat átalakítását

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Fogadó-beállítások](media/data-flow/sink1.png "fogadó 1")

Az Adatátalakítási folyamat befejezése után az átalakított adatok is fogadó célkiszolgáló adatkészletekbe. A fogadó átalakítási választhatja ki, amely a cél kimeneti adatok használni kívánt adatkészlet-definícióban. Előfordulhat, hogy annyi fogadó átalakítást, az adatfolyam igényel.

Egy fiókra a bejövő adatok módosítása és a számla séma eltéréseket az általános gyakorlat, hogy a kimeneti adatokat fogadó egy mappába a kimeneti adatkészletben definiált séma nélkül. Meg is emellett a fiók az összes oszlop változáshoz a forrásokban "Engedélyezése séma eltéréseket" a forrásnál, és a automatikus hozzárendelés kijelölésével a fogadó található összes mezőhöz.

Ha szeretné, felülírja, hozzáfűzése, vagy az adatok az adatfolyam sikertelen, ha az Elhelyezés adatkészletre.

Választhatja "automatikus hozzárendelés" fogadó minden bejövő mezőt is. Ha szeretné, hogy a cél fogadó kívánt mezők kiválasztása, vagy ha szeretné módosítani a célhely a mezők nevében, válassza a "automatikus"hozzárendelés "Off", és a kimeneti mezők leképezése a leképezési lapon kattintson:

![Fogadó-beállítások](media/data-flow/sink2.png "fogadó 2")

## <a name="output-to-one-file"></a>Egy fájl kimeneti
Az Azure Storage Blob vagy a Data Lake fogadó típusait az átalakított adatokat fog kimeneti mappába. A Spark használt particionálási séma alapján particionált kimeneti adatfájlokat hoz létre a fogadó átalakítása. Beállíthatja a particionálási sémát az "Optimalizálás" fülre kattintva. Ha szeretné, hogy a kimeneti egyesíthet egyetlen fájl ADF, kattintson a "Egypartíciós" gombra.

![Fogadó-beállítások](media/data-flow/opt001.png "fogadó-beállítások")

## <a name="field-mapping"></a>Mezőleképezés

A fogadó átalakítást leképezés lapján a bejövő (bal oldal) oszlopok leképezheti a célhelyre (a jobb oldalon). Adatfolyam-gyűjteményre, fogadó-fájlokhoz, amikor ADF mindig írni új fájlokat egy mappában. Ha leképez egy adatbázis-adatkészletet, dönthet úgy, vagy létrehozhat egy új táblát a séma ("felülírása" mentési szabályzat beállítása), vagy új sor beszúrása egy meglévő táblát, és a mezők leképezése a meglévő sémák.

Válassza ki a hozzárendelési táblában használhatja a hivatkozás több oszlop egyetlen kattintással, több oszlop csatolásának megszüntetése vagy több sort leképezése oszlop nevével megegyező névre.

Ha szeretné mindig a bejövő mezők halmaza is, és megfeleltet az a cél,-van, állítsa be a "Schema eltéréseket engedélyezése" beállítást.

![Mezőt leképező](media/data-flow/multi1.png "többféle lehetőség")

Ha szeretne alaphelyzetbe állítani az oszlop-hozzárendelések, nyomja le az "Újramegfeleltetése" gombra a hozzárendelések visszaállítása.

![Fogadó-beállítások](media/data-flow/sink1.png "egy fogadó")

![Fogadó-beállítások](media/data-flow/sink2.png "fogadók")

* Lehetővé teszi a séma eltéréseket, és a séma érvényesítése, mostantól elérhetők fogadó. Ez lehetővé teszi, hogy teljes mértékben fogadja el a rugalmas sémadefiníciók (séma eltéréseket) vagy a fogadó sikertelen lesz, ha a séma módosul (séma érvényesítése) az ADF utasíthatja.

* Törölje a mappát. Az ADF csonkolja a fogadó mappa tartalmának írása a cél-fájlokat a célmappában előtt.

## <a name="file-name-options"></a>Fájlnév beállításai

   * Alapértelmezett: Fájlok elnevezésére rész alapértelmezett értékei szerinti Spark engedélyezése
   * Pattern: Adja meg a kimeneti fájlokat egy mintát. Például létrehozza a "hitelek [n]" loans1.csv, loans2.csv,...
   * Partíciónként: Írjon be egy fájlnevet partíciónként
   * Oszlop adatként: Egy oszlop értékét állítsa a kimeneti fájl

> [!NOTE]
> Fájlműveletek csak akkor futnak, amikor az adatok folyamat végrehajtása tevékenység nem a Data Flow hibakeresési módban futtatja

## <a name="database-options"></a>Adatbázis-beállítások

* Lehetővé teszi az insert, update, delete, upserts. Alapértelmezés szerint a rendszer lehetővé tegyük. Ha szeretné, frissítés, upsert, vagy törölje a sorokat, először hozzá kell adnia egy alter sor átalakítást címke sorokra e adott műveletek. "Insert engedélyezése" kikapcsolásával leáll az ADF új sorok beszúrását a forrásból.
* TRUNCATE table (eltávolítja az összes sor a céloldali tábla az adatokat a folyamat befejezése előtt)
* Hozza létre újból táblában (az adatok a folyamat befejezése előtt dobja el és létrehozására a céltábla végez)
* Köteg mérete nagy mennyiségű adat terhelés. Adjon meg egy számot és gyűjtőbe írási adattömbökbe
* Előkészítés engedélyezése: Ez utasítja az ADF a polybase szolgáltatást akkor használja, az Azure Data warehouse-ba, a fogadó-adatkészlet betöltése közben

> [!NOTE]
> Az adatfolyam hozhat létre egy új tábla definícióját a céladatbázis adatkészlet beállítása a fogadó átalakítást, amely rendelkezik egy új tábla nevét az ADF teheti fel. Az alábbi a táblázat neve a "Szerkesztés" gombra az SQL-adatkészletet, és adja meg egy új tábla nevét. Ezt követően a fogadó átalakításában kapcsolja be a "Séma eltéréseket engedélyezése". Seth None értékre a "Séma importálása" beállítást.

![Átalakítás schéma zdroje](media/data-flow/dataset2.png "SQL-séma")

![Fogadó SQL-beállítások](media/data-flow/alter-row2.png "SQL-beállítások")

> [!NOTE]
> Frissítése vagy törlése az adatbázis fogadó sorok, be kell állítani a kulcsoszlop. Ezzel a módszerrel Alter sor meg tudja határozni a DML a egyedi sort.

## <a name="next-steps"></a>További lépések

Most, hogy létrehozta az adatfolyamot, adjon hozzá egy [tevékenységet a folyamat végrehajtása az adatfolyam](concepts-data-flow-overview.md).
