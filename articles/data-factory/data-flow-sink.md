---
title: Állítsa be az Azure Data Factory az adatfolyam-leképezés funkció a fogadó átalakítás
description: Ismerje meg, hogyan állítható be a leképezés adatfolyam egy fogadó átalakítási műveletet.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 4341cbb0e24330d535f5211c088f0068eab33af7
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596260"
---
# <a name="sink-transformation-for-a-data-flow"></a>Az adatfolyam átalakításában szerepel a fogadó

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Miután átalakítja az adatokat a folyamat, akkor az adatok cél adatkészletekbe is fogadó. A fogadó átalakításában szerepel a válassza ki a cél kimeneti adatokat egy adatkészlet-definícióban. Akkor is, számos fogadó átalakítások, az adatfolyam igényel.

Figyelembe venni a séma eltéréseket és változások a bejövő adatokat a kimeneti adatokat fogadó egy mappába a kimeneti adatkészletben definiált séma nélkül. Akkor is is fiók oszlop módosítások a forrásokban kiválasztásával **séma eltéréseket engedélyezése** a forrás. Ezután a gyűjtő automatikus hozzárendelés az összes mezőt.

![A fogadó lapon, valamint automatikus térkép lehetőséget](media/data-flow/sink1.png "fogadó 1")

A fogadó-az összes bejövő mezők, kapcsolja be a **automatikus térkép**. Válassza ki a mezőket a cél fogadó, illetve módosítsa a célhely a mezők nevében, kapcsolja ki a **automatikus térkép**. Nyissa meg a **leképezési** fülre, és a kimeneti mezők leképezése.

![A leképezés lapon](media/data-flow/sink2.png "fogadó 2")

## <a name="output"></a>Kimenet 
Az Azure Blob storage vagy a Data Lake Storage fogadó típusa a kimeneti az átalakított adatokat egy mappába. Spark, amely a fogadó átalakítási particionálási séma alapján particionált kimeneti adatfájlokat hoz létre. 

Beállíthatja, hogy a particionálási sémát a **optimalizálása** fülre. Ha azt szeretné, hogy a Data Factory a kimeneti egyesíthet egyetlen fájlt, jelölje be **egyetlen partíció**.

![Az Optimalizálás lap beállításai](media/data-flow/opt001.png "fogadó-beállítások")

## <a name="field-mapping"></a>Mezőleképezés

A a **leképezési** lapon, a fogadó átalakítást leképezheti a bejövő oszlopokat a bal oldali a jobb oldalon a célhelyre. Adatfolyam-gyűjteményre, fogadó-fájlokhoz, ha a Data Factory mindig írni új fájlokat egy mappát. Ha leképez egy adatbázis-adatkészletet, létrehozhat egy új táblát, amely ebben a sémában beállításával **házirend mentése** való **felülírás**. Vagy új sor beszúrása egy már meglévő tábla, és rendelje hozzá a mezőket a meglévő sémához. 

![A leképezés lapon](media/data-flow/sink2.png "fogadók")

A hozzárendelési táblában is többszörös kijelölés több oszlop hivatkozásra, több oszlop csatolásának megszüntetése vagy több sort leképezése oszlop nevével megegyező névre.

Mindig leképezhető mezők bejövő halmaza egy cél és rugalmas sémadefiníciók, teljes körűen elfogadásához válassza **séma eltéréseket engedélyezése**.

![A leképezés lapon az adatkészletben lévő oszlopok leképezett mezők](media/data-flow/multi1.png "többféle lehetőség")

Válassza ki az oszlop-hozzárendelések alaphelyzetbe állításához **tulajdonságkeresést**.

![A fogadó lap](media/data-flow/sink1.png "egy fogadó")

Válassza ki **ellenőrzése séma** a fogadó sikertelen lesz, ha a séma módosul.

Válassza ki **törölje a mappát** csonkolni a fogadó mappa tartalmának írása a cél-fájlokat a célmappában előtt.

## <a name="file-name-options"></a>Fájlnév beállításai

Állítsa be a fájl elnevezése: 

   * **Alapértelmezett**: Spark engedélyezése neve fájlok rész alapértékeken alapul.
   * **A minta**: Adja meg a kimeneti fájlokat egy mintát. Ha például **hitelek [n]** fog létrehozni, loans1.csv loans2.csv és így tovább.
   * **Partíciónként**: Adjon meg egy fájlnevet partíciónként.
   * **Oszlop adatként**: A kimeneti fájl beállítása egy oszlop értékét.
   * **Egyetlen fájlba való**: Ezzel a beállítással ADF fájlnak egyetlen fog össze a particionált kimeneti fájlok. Ez a beállítás használatához az adatkészlet kell feloldhatónak lennie a mappa nevét. Emellett Felhívjuk a figyelmét arra, hogy a merge művelet valószínűleg sikertelen lehet a csomópont mérete alapján.

> [!NOTE]
> Fájl a műveletek indítása csak akkor, ha az adatok folyamat végrehajtása tevékenység futtatja. Data Flow hibakeresési módban nem indítása.

## <a name="database-options"></a>Adatbázis-beállítások

Válassza ki az adatbázis-beállítások:

* **Frissítésének módja**: Alapértelmezés szerint a rendszer lehetővé tegyük. Egyértelmű **engedélyezése insert** Ha meg szeretné szüntetni az új sorok beszúrását a forrásból. Szeretné frissíteni, upsert, vagy törölje a sorokat, először hozzá egy alter-sor átalakítást címke sorokra a csatolási műveleteket. 
* **Hozza létre újból táblában**: Dobja el, vagy a céloldali tábla létrehozása előtt az adatfolyam befejeződik.
* **A csonkolás tábla**: Távolítsa el az összes sor a céloldali tábla előtt az adatfolyam befejeződik.
* **Kötegméret**: Adjon meg egy számot és gyűjtőbe írási adattömböket. Használja ezt a beállítást nagy mennyiségű adat terhelés. 
* **Előkészítés engedélyezése**: Polybase szolgáltatást akkor használja, ha az Azure Data Warehouse, a fogadó-adatkészlet.

![A beállítások lapon, a fogadó SQL-lehetőségeket bemutató](media/data-flow/alter-row2.png "SQL-beállítások")

> [!NOTE]
> Az adatfolyam a Data Factory új tábla definíciójának létrehozása a céladatbázis irányíthatók. A tábla definíciójának létrehozásához be egy adatkészlet a fogadó átalakítást, amely rendelkezik egy új tábla neve. Válassza ki az SQL-adatkészletet, a táblázat neve alatt **szerkesztése** , és adja meg egy új tábla nevét. A fogadó átalakításában szerepel a kapcsolja be **séma eltéréseket engedélyezése**. Állítsa be **séma importálása** való **None**.

![SQL-adatkészlet beállítások: hol kell módosítani a tábla nevét](media/data-flow/dataset2.png "SQL-séma")

> [!NOTE]
> Frissítésekor és az adatbázis fogadó a sorok törlése, be kell a kulcsoszlop. Ez a beállítás lehetővé teszi, hogy az alter-sor átalakítás meghatározásához az egyedi soron, az adatátviteli könyvtár (DML).

## <a name="next-steps"></a>További lépések

Most, hogy létrehozta az adatfolyamot, adjon hozzá egy [adatfolyam tevékenységet a folyamat](concepts-data-flow-overview.md).
