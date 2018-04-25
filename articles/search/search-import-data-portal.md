---
title: Adatok importálása az Azure Search szolgáltatásba a portálon | Microsoft Docs
description: Az Azure Portalon az Azure Search Adatok importálása varázslójával bejárhatja az Azure-beli virtuális gépeken található NoSQL Azure Cosmos DB-ben, Blob Storage-ban, Table Storage-ban, SQL Database-ben és SQL Serverben tárolt Azure-adatokat.
author: HeidiSteen
manager: cgronlun
tags: Azure Portal
ms.service: search
ms.topic: quickstart
ms.date: 05/01/2017
ms.author: heidist
ms.openlocfilehash: b651ccaa59e40b9d8d1c682fc8c266bf95b62a2e
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="import-data-to-azure-search-using-the-portal"></a>Adatok importálása az Azure Search szolgáltatásba a portál használatával
Az Azure portál Azure Search irányítópultján található **Adatok importálása** varázsló segítségével az adatok betölthetők egy indexbe. 

  ![A parancssáv Adatok importálása eleme][1]

Belső használatra a varázsló konfigurál és meghív egy *indexelőt*, és automatizálja az indexelési folyamat több lépését: 

* Csatlakozás az ugyanabban az Azure-előfizetésben található külső adatforráshoz
* Módosítható indexséma létrehozása a forrás-adatszerkezet alapján
* JSON-dokumentumok betöltése egy indexbe az adatforrásból beolvasott sorhalmaz használatával

Az Azure Cosmos DB-adatbázisból vett mintaadatokkal kipróbálhatja ezt a munkafolyamatot. Az utasításokért tekintse meg az [Ismerkedés az Azure Search szolgáltatással az Azure portálon](search-get-started-portal.md) részt.

> [!NOTE]
> Az Azure Cosmos DB irányítópultjáról elindíthatja az **Adatok importálása** varázslót, amellyel egyszerűbbé válik az adatforrás indexelése. A kezdéshez lépjen a bal oldali navigációs menüben a **Gyűjtemények** > **Azure Search hozzáadása** elemre.

## <a name="data-sources-supported-by-the-import-data-wizard"></a>Az Adatok importálása varázsló által támogatott adatforrások
Az Adatok importálása varázsló az alábbi adatforrások használatát támogatja: 

* Azure SQL Database
* Az SQL Server relációs adatai egy Azure virtuális gépen
* Azure Cosmos DB
* Azure Blob Storage
* Azure Table Storage

Egybesimított adatkészlet a kötelező bemenet. Az importálás kizárólag egyedi táblából, adatbázisnézetből és egyenértékű adatszerkezetből végezhető el. Az adatszerkezetet a varázsló futtatása előtt létre kell hoznia.

## <a name="connect-to-your-data"></a>Csatlakozás az adatokhoz
1. Jelentkezzen be a [Azure Portalra](https://portal.azure.com), és nyissa meg a szolgáltatás irányítópultját. A gyorselérési sávon a **Minden szolgáltatás** elemre kattintva kereshet az aktuális előfizetésben meglévő „keresési szolgáltatásokat”. 
2. Az Adatok importálása panel csúsztatott megnyitásához kattintson a parancssávon található **Adatok importálása** elemre.  
3. Az indexelő által használt adatforrás-definíció megadásához kattintson a **Csatlakozás az adatokhoz** lehetőségre. Előfizetésen belüli adatforrások esetében a varázsló általában észleli és elolvassa a kapcsolatadatokat, és minimalizálja az általános konfigurációs követelményeket.

|  |  |
| --- | --- |
| **Meglévő adatforrás** |Ha a Search-szolgáltatásban már meg vannak adva indexelők, másik importálási folyamathoz kiválaszthat egy meglévő adatforrás-definíciót. |
| **Azure SQL Database** |A szolgáltatás neve, az olvasási engedéllyel rendelkező adatbázis-felhasználó hitelesítő adatai, valamint az adatbázis neve megadható vagy az oldalon, vagy az ADO.NET kapcsolati karakterlánc használatával. A tulajdonságok megtekintéséhez vagy testreszabásához válassza ki a kapcsolati karakterlánc lehetőséget. <br/><br/>Az oldalon meg kell adni a sorhalmazt biztosító táblát vagy nézetet. Ez a lehetőség a sikeres csatlakozás után jelenik meg: egy legördülő listából választhatja ki a kívánt elemet. |
| **Azure virtuális gépen futó SQL Server** |Adjon meg egy teljesen minősített szolgáltatásnevet, felhasználóazonosítót és jelszót, illetve egy adatbázist kapcsolati karakterláncként. Az adatforrás használatához a helyi tárolóban rendelkeznie kell egy korábban telepített tanúsítvánnyal, amely titkosítja a kapcsolatot. Részletes útmutatásért lásd az [SQL virtuális gép az Azure Search-höz történő csatlakoztatását](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) ismertető témakört. <br/><br/>Az oldalon meg kell adni a sorhalmazt biztosító táblát vagy nézetet. Ez a lehetőség a sikeres csatlakozás után jelenik meg: egy legördülő listából választhatja ki a kívánt elemet. |
| **Azure Cosmos DB** |A követelmények a következőket tartalmazzák: a fiók, az adatbázis és a gyűjtemény. Az index tartalmazza a gyűjteményben szereplő összes dokumentumot. Definiálhat egy lekérdezést a sorkészlet egybesimításához vagy szűréséhez, illetve a módosított dokumentumok észleléséhez a későbbi adatfrissítési műveletek elvégzése érdekében. |
| **Azure Blob Storage** |A követelmények a következőket tartalmazzák: a tárolási fiók és egy tároló. Szükség esetén, ha csoportosítási célból a blob-nevek egy virtuális elnevezési konvenciót követnek, akkor a név virtuáliskönyvtár-részét a tárolóban található mappa neveként is megadhatja. A további információkat [a Blob Storage indexelését](search-howto-indexing-azure-blob-storage.md) ismertető témakör tartalmazza. |
| **Azure Table Storage** |A követelmények a következőket tartalmazzák: a tárolási fiók és egy táblanév. Szükség esetén megadhat egy lekérdezést a táblák részhalmazának beolvasásához. A további információkat [a Table Storage indexelését](search-howto-indexing-azure-tables.md) ismertető témakör tartalmazza. |

## <a name="customize-target-index"></a>Célindex testreszabása
Általában az adatkészletből kikövetkeztetett előzetes index. A séma elkészítéséhez mezőket adhat hozzá, szerkeszthet vagy törölhet. Továbbá mezőszinten attribútumokat állíthat be az index későbbi keresési viselkedésének meghatározása érdekében.

1. A **Célindex testreszabása** lapon adja meg a nevet és egy **kulcsot**, amelyet az egyes dokumentumok egyedi azonosításához kell használni. A kulcsnak karakterláncnak kell lennie. Ha a mezőértékek szóközöket vagy kötőjeleket tartalmaznak, ügyeljen arra, hogy az **Adatok importálása** lapon meg kell adnia a speciális beállításokat ezeknek a karaktereknek az érvényességét ellenőrző művelet letiltásához.
2. Tekintse át és módosítsa a fennmaradó mezőket. A mező neve és típusa általában már ki van töltve. Az adattípus az index létrehozásának pillanatáig módosítható. Ha ezt követően szeretné módosítani, újra kell építeni az indexet.
3. Állítsa be az egyes mezőkhöz tartozó indexattribútumokat:
   
   * A lekérdezhető attribútum a keresési eredmények között visszaadja a mező értékét.
   * A szűrhető attribútum hivatkozhatóvá teszi az adott mezőt a szűrőkifejezésekben.
   * A rendezhető attribútum segítségével az adott mezőt használhatjuk egy rendezés alapjaként.
   * Az értékkorlátozó attribútum értékkorlátozott navigációt tesz lehetővé az adott mező esetében.
   * A kereshető attribútum lehetővé teszi a teljes szöveges keresést.
4. Nyelvi elemző mezőszinten történő megadásához kattintson az **Elemző** lapra. Ekkor csak a nyelvi elemzőket adhatja meg. Egyéni elemző vagy nem nyelvi elemző, például kulcsszó, minta és hasonló elemek használata esetén szükség van a kódra.
   
   * Kattintson a **Kereshető** elemre, hogy teljes szöveges keresést jelöljön ki a mezőhöz, és engedélyezze a Elemző legördülő listáját.
   * Válassza ki a kívánt elemzőt. Részletes információk: [Több nyelven elérhető dokumentumok indexének létrehozása](search-language-support.md).
5. A kijelölt mezőkre vonatkozó gépelés közbeni lekérdezési javaslatok engedélyezéséhez kattintson a **Javaslatok** elemre.

## <a name="import-your-data"></a>Adatok importálása
1. Az **Adatok importálása** mezőben adja meg az indexelő nevét. Ne felejtse el, hogy az Adatok importálása varázsló terméke egy indexelő. Később, ha szeretné megtekinteni vagy módosítani, a portálról kiválaszthatja, és nem kell újból lefuttatnia a varázslót. 
2. Adja meg az ütemezést annak a regionális időzónának az alapján, amelyben a szolgáltatás létrejött.
3. Adja meg azokra a küszöbértékekre vonatkozó speciális beállításokat, amelyeknél az indexelés akkor is folytatódik, ha a dokumentumot elvetik. Ezenkívül azt is megadhatja, hogy a **kulcs**mezők tartalmazhatnak-e szóközöket és perjeleket.  
4. Az index létrehozásához és az adatok importálásához kattintson az **OK** gombra.

Az indexelés állapotát figyelemmel követheti a portálon. A dokumentumok betöltésével a meghatározott index dokumentumszámlálója növekszik. Néha beletelik néhány percbe, mire a portál oldala tükrözi a legfrissebb adatokat.

Miután az összes dokumentum betöltődött, az index azonnal lekérdezhető.

## <a name="query-an-index-using-search-explorer"></a>Index lekérdezése a Keresési ablak használatával

A portál tartalmaz egy **Keresési ablakot**, amelynek segítségével kód írása nélkül kérdezheti le az indexeket. A [Keresési ablak](search-explorer.md) bármely indexen használható.

A keresési funkció alapértelmezett beállításokon alapul, mint az [egyszerű szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) és az alapértelmezett [searchMode lekérdezési paraméter(https://docs.microsoft.com/rest/api/searchservice/search-documents). 

A rendszer az eredményeket JSON-formátumban nagy részletességgel adja vissza, így a teljes dokumentum vizsgálható.

## <a name="edit-an-existing-indexer"></a>Meglévő indexelő szerkesztése
Ahogy azt már említettük, az Adatok importálása varázsló létrehoz egy **indexelőt**, amely a portálon önálló szerkezetként módosítható.

A szolgáltatás irányítópultján kattintson duplán az Indexelő csempére az előfizetéshez létrehozott összes indexelő listájának kicsúsztatásához. Az indexelők futtatásához, szerkesztéséhez és törléséhez kattintson rájuk duplán. Lecserélheti az indexet egy másik létező indexre, módosíthatja az adatforrást, és beállíthatja az indexelés során érvényes hibaküszöböket.

## <a name="edit-an-existing-index"></a>Meglévő index szerkesztése
A varázsló emellett létrehozott egy **indexet** is. Az Azure Search szolgáltatásban az index strukturális frissítéséhez az index újjáépítésére van szükség. Az újjáépítés az index törlését, a kívánt módosításokat tartalmazó átalakított séma mentén való újbóli létrehozását, majd az adatok újbóli betöltését jelenti. A strukturális frissítések közé tartozik az adattípusok módosítása és a mezők átnevezése vagy törlése.

Az újjáépítést nem igénylő szerkesztési műveletek közé tartozik az új mezők hozzáadása, illetve a pontozási profilok, a javaslatok vagy a nyelvelemzők módosítása. További információ: [Update Index](https://msdn.microsoft.com/library/azure/dn800964.aspx) (Index frissítése).


## <a name="next-steps"></a>További lépések
Az alábbi hivatkozások további információkat nyújtanak az indexelőkről:

* [Az Azure SQL Database indexelése](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Az Azure Cosmos DB indexelése](search-howto-index-cosmosdb.md)
* [A Blob Storage indexelése](search-howto-indexing-azure-blob-storage.md)
* [A Table Storage indexelése](search-howto-indexing-azure-tables.md)

<!--Image references-->
[1]: ./media/search-import-data-portal/search-import-data-command.png

