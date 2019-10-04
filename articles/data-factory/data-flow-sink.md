---
title: Egy fogadó átalakítás beállítása a Azure Data Factory leképezési adatáramlási funkciójában
description: Megtudhatja, hogyan állíthat be egy fogadó-átalakítást a leképezési adatfolyamban.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 24ad0f2e917420c327577851cabc9e5bdbad2825
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515663"
---
# <a name="sink-transformation-for-a-data-flow"></a>Adatfolyam-transzformáció

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Az adatfolyamatok átalakítását követően az adatokat egy célként megadott adatkészletbe lehet elosztani. A fogadó átalakításban válassza ki a cél kimeneti adatokat tartalmazó adatkészlet-definíciót. Az adatáramláshoz annyi fogadó átalakításra van szükség.

A sémák eltolódásának és a bejövő adatváltozások változásainak a megadásához a kimeneti adatkészletben definiált séma nélküli mappába kell Elsüllyedni a kimeneti adatokat. A forrásokban lévő oszlopok változásait is figyelembe veheti, ha kiválasztja a **séma-eltolódás engedélyezése** lehetőséget a forrásban. Ezután a fogadó összes mezőjét AutoMapa.

A fogadó ![lapon megjelenő beállítások, beleértve az automatikus leképezés lehetőséget is](media/data-flow/sink1.png "1") . fogadó

Az összes bejövő mező elfogadásához kapcsolja be az **automatikus leképezést**. A célhelyre befogadó mezők kiválasztásához, illetve a célhelyen lévő mezők nevének módosításához kapcsolja ki az **automatikus leképezést**. Ezután nyissa meg a **leképezés** lapot a kimeneti mezők leképezéséhez.

![Beállítások a leképezés lapon](media/data-flow/sink2.png "2") . fogadó

## <a name="output"></a>Output 
Az Azure Blob Storage-hoz vagy a Data Lake Storage fogadó típushoz az átalakított adatokat egy mappába írja. A Spark particionált kimeneti adatfájlokat hoz létre a fogadó átalakítás által használt particionálási séma alapján. 

Az **optimalizálás** lapon állíthatja be a particionálási sémát. Ha azt szeretné, Data Factory hogy egyetlen fájlba egyesítse a kimenetet, válassza az **egyetlen partíció**lehetőséget.

![Beállítások az optimalizálás lapon] fogadó (media/data-flow/opt001.png "beállításai")

## <a name="field-mapping"></a>Mező-hozzárendelés
A fogadó átalakítás **leképezés** lapján a bal oldali bejövő oszlopok a jobb oldalon lévő célhelyekre képezhetők le. Amikor adatfolyamatokat másol a fájlokhoz, Data Factory mindig új fájlokat fog írni egy mappába. Ha adatbázis-adatkészlethez rendel leképezést, akkor az adatbázis-táblázat műveleti lehetőségei közül választhatja a Beszúrás, a frissítés, a upsert vagy a Törlés lehetőséget.

![A leképezés lap](media/data-flow/sink2.png "Mosogatók")

A leképezési táblában több oszlop összekapcsolására, több oszlop leválasztására vagy több sor azonos nevű oszlopra való hozzárendelésére van lehetőség.

Ha a mezők bejövő készletét mindig egy célként szeretné leképezni, és a rugalmas sémákat teljes mértékben el szeretné fogadni, válassza a **séma drift engedélyezése**lehetőséget.

![A leképezés lap, amely az adatkészlet oszlopaihoz rendelt mezőket mutatja](media/data-flow/multi1.png "több lehetőség")

Az oszlop-hozzárendelések alaphelyzetbe állításához válassza az **újbóli leképezés**lehetőséget.

![A fogadó lap] Fogadó (media/data-flow/sink1.png "egy")

Válassza a **séma ellenőrzése** lehetőséget, ha a séma megváltozásakor a fogadó meghibásodik.

Válassza **a mappa törlése** elemet a fogadó mappa tartalmának kivágásához, mielőtt a célmappába beírja a célfájl tartalmát.

## <a name="rule-based-mapping"></a>Szabály alapú leképezés
Az automatikus leképezés kikapcsolásakor lehetősége van az oszlop alapú leképezés (rögzített leképezés) vagy a szabályokon alapuló leképezés hozzáadására is. A szabályon alapuló leképezés lehetővé teszi, hogy a kifejezéseket minta egyeztetéssel írja. 

![Szabály alapú leképezés](media/data-flow/rules4.png "Szabály alapú leképezés")

Ha a szabály alapú leképezést választja, akkor a rendszer az ADF-et arra utasítja, hogy értékelje a megfelelő kifejezést a bejövő mintázat szabályainak megfelelően, és adja meg a kimenő mezők nevét. A mezők és a szabályokon alapuló leképezések tetszőleges kombinációját felveheti. A mezőneveket a forrástól érkező bejövő metaadatok alapján, az ADF futásidőben generálja a rendszer. A generált mezők neve a hibakeresés során és az adatelőnézet panelen tekinthető meg.

A minta egyeztetésével kapcsolatos részletek az [oszlop mintájának dokumentációjában](concepts-data-flow-column-pattern.md)találhatók.

## <a name="file-name-options"></a>Fájlnév beállításai

Fájl elnevezésének beállítása: 

   * **Alapértelmezett**: Fájlok nevének engedélyezése a Spark számára a részek alapértelmezett értékei alapján.
   * **Minta**: Adja meg a kimeneti fájlok mintáját. A **hitelek [n]** például a loans1. csv, a loans2. csv és így tovább fog létrejönni.
   * **/Partíció**: Adja meg a partíciók egy fájlnevét.
   * **Oszlopbeli adatként**: Állítsa a kimeneti fájlt egy oszlop értékére.
   * **Kimenet egyetlen fájlba**: Ezzel a beállítással az ADF egyetlen elnevezett fájlba fogja egyesíteni a particionált kimeneti fájlokat. Ha ezt a beállítást szeretné használni, az adatkészlet fel kell oldania a mappa nevét. Azt is vegye figyelembe, hogy az egyesítési művelet valószínűleg meghiúsulhat a csomópont méretétől függően.

> [!NOTE]
> A fájl műveletei csak akkor indulnak el, ha futtatja az adatfolyam végrehajtása tevékenységet. Nem indulnak el adatfolyam-hibakeresési módban.

## <a name="database-options"></a>Adatbázis-beállítások

Adatbázis-beállítások kiválasztása:

![A beállítások lap, amely az SQL-fogadó beállításait mutatja](media/data-flow/alter-row2.png "SQL-beállítások")

* **Frissítési módszer**: Az alapértelmezett érték a beszúrások engedélyezése. Ha le szeretné állítani a forrásból az új sorok beszúrását, törölje a **Beszúrás engedélyezése** jelölőnégyzet jelölését. A sorok frissítéséhez, upsert vagy törléséhez először adjon hozzá egy Alter-Row transzformációt a műveletekhez tartozó sorok címkézéséhez. 
* **Tábla újbóli létrehozása**: Dobja el vagy hozza létre a cél táblát az adatfolyam befejeződése előtt.
* **Táblázat csonkítása**: Az adatfolyamat befejeződése előtt távolítsa el a céltábla összes sorát.
* **Köteg mérete**: Adja meg azt a számot, amelyet a rendszer a tömbökbe ír. Ezt a lehetőséget nagy adatterhelések esetén használja. 
* **Előkészítés engedélyezése**: Ha az Azure-adattárházat a fogadó adatkészletként tölti be, használja a Base-t.
* **SQL-szkriptek előtti és utáni parancsfájlok**: Adja meg azokat a többsoros SQL-parancsfájlokat, amelyeket a rendszer a (pre-Processing) és a (post-Processing) adatainak a fogadó adatbázisba való beírása előtt végrehajt.

![SQL-feldolgozási parancsfájlok előzetes és utáni feldolgozása](media/data-flow/prepost1.png "SQL-feldolgozási parancsfájlok")

> [!NOTE]
> Az adatforgalomban Data Factory, hogy új tábla-definíciót hozzon létre a céladatbázis használatával. A tábla definíciójának létrehozásához állítson be egy adatkészletet a fogadó átalakításban, amely új táblanév néven szerepel. Az SQL-adatkészletben a tábla neve alatt válassza a **Szerkesztés** lehetőséget, és adjon meg egy új táblanév nevet. Ezután a fogadó átalakításban kapcsolja be a **séma-eltolódás engedélyezése lehetőséget**. Az **importálási séma** beállítása **none**értékre.

![SQL-adatkészlet beállításai, amely a tábla nevének szerkesztésének helyét mutatja](media/data-flow/dataset2.png "SQL-séma")

> [!NOTE]
> Az adatbázis-fogadóban lévő sorok frissítésekor vagy törlésekor a kulcs oszlopot kell megadnia. Ez a beállítás lehetővé teszi, hogy az Alter-Row transzformáció meghatározza az adatátviteli függvénytár (DML) egyedi sorát.

## <a name="next-steps"></a>További lépések
Most, hogy létrehozta az adatfolyamatot, adjon hozzá egy [adatfolyam-tevékenységet a folyamathoz](concepts-data-flow-overview.md).
