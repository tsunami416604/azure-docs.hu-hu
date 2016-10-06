<properties
    pageTitle="Adatok importálása az Azure Search szolgáltatásba az Azure portál indexelőinek használatával | Microsoft Azure | Üzemeltetett felhőalapú keresési szolgáltatás"
    description="Az Azure portálon az Azure Search Adatok importálása varázsló segítségével bejárhatja az Azure virtuális gépeken található Azure Blob Storage, Table Storage, SQL Database, és SQL Server alkalmazásokban tárolt adatokat."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""
    tags="Azure Portal"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="heidist"/>


# Adatok importálása az Azure Search szolgáltatásba a portál használatával

Az Azure portál Azure Search irányítópultján található **Adatok importálása** varázsló segítségével az adatok betölthetők egy indexbe. 

  ![A parancssáv Adatok importálása eleme][1]

Belső használatra a varázsló konfigurál és meghív egy *indexelőt*, és automatizálja az indexelési folyamat több lépését: 

- Csatlakozás az aktuális Azure-előfizetésben található külső adatbázishoz
- Indexséma automatikus létrehozása a forrás adatszerkezet alapján
- Dokumentumok létrehozása az adatforrásból beolvasott sorhalmaz alapján
- A dokumentumok feltöltése a keresési szolgáltatásban lévő indexbe

A DocumentDB adatbázisból vett mintaadatokkal kipróbálhatja ezt a munkafolyamatot. Az utasításokért tekintse meg az [Ismerkedés az Azure Search szolgáltatással az Azure portálon](search-get-started-portal.md) részt.

## Az Adatok importálása varázsló által támogatott adatforrások

Az Adatok importálása varázsló az alábbi adatforrások használatát támogatja: 

- Azure SQL Database
- Az SQL Server relációs adatai egy Azure virtuális gépen
- Azure DocumentDB
- Azure Blob Storage (előzetes verzióban)
- Azure Table Storage (előzetes verzióban)

Egybesimított adatkészlet a kötelező bemenet. Az importálás kizárólag egyedi táblából, adatbázisnézetből és egyenértékű adatszerkezetből végezhető el. Az adatszerkezetet a varázsló futtatása előtt létre kell hoznia.

Ne feledje, hogy néhány indexelő még mindig előzetes verzióban van, ami azt jelenti, hogy az indexelő definíciója az API előzetes verziójából származik. A további információkat és a hivatkozásokat [Az indexelő áttekintése](search-indexer-overview.md) című rész tartalmazza.

## Csatlakozás az adatokhoz

1. Jelentkezzen be a [Azure portálra](https://portal.azure.com), és nyissa meg a szolgáltatási irányítópultját. Az ugrósávon a **Szolgáltatások keresése** elemre kattintva megjelenítheti az aktuális előfizetésben meglévő szolgáltatásokat. 

2. Az Adatok importálása panel csúsztatott megnyitásához kattintson a parancssávon található **Adatok importálása** elemre.  

3. Az indexelő által használt adatforrás-definíció megadásához kattintson a **Csatlakozás az adatokhoz** lehetőségre. Előfizetésen belüli adatforrások esetében a varázsló általában észleli és elolvassa a kapcsolatadatokat, és minimalizálja az általános konfigurációs követelményeket.

| | |
|--------|------------|
|**Meglévő adatforrás** | Ha a Search-szolgáltatásban már meg vannak adva indexelők, másik importálási folyamathoz kiválaszthat egy meglévő adatforrás-definíciót.|
|**Azure SQL Database** | A szolgáltatás neve, az olvasási engedéllyel rendelkező adatbázis-felhasználó hitelesítő adatai, valamint az adatbázis neve megadható vagy az oldalon, vagy az ADO.NET kapcsolati karakterlánc használatával. A tulajdonságok megtekintéséhez vagy testreszabásához válassza ki a kapcsolati karakterlánc lehetőséget. <br/><br/>Az oldalon meg kell adni a sorhalmazt biztosító táblát vagy nézetet. Ez a lehetőség a sikeres csatlakozás után jelenik meg: egy legördülő listából választhatja ki a kívánt elemet.|
|**Azure virtuális gépen futó SQL Server** | Adjon meg egy teljesen minősített szolgáltatásnevet, felhasználóazonosítót és jelszót, illetve egy adatbázist kapcsolati karakterláncként. Az adatforrás használatához a helyi tárolóban rendelkeznie kell egy korábban telepített tanúsítvánnyal, amely titkosítja a kapcsolatot. <br/><br/>Az oldalon meg kell adni a sorhalmazt biztosító táblát vagy nézetet. Ez a lehetőség a sikeres csatlakozás után jelenik meg: egy legördülő listából választhatja ki a kívánt elemet.
|**DocumentDB** |A követelmények a következőket tartalmazzák: a fiók, az adatbázis és a gyűjtemény. Az index tartalmazza a gyűjteményben szereplő összes dokumentumot. Definiálhat egy lekérdezést a sorkészlet egybesimításához vagy szűréséhez, illetve a módosított dokumentumok észleléséhez a későbbi adatfrissítési műveletek elvégzése érdekében.|
|**Azure Blob Storage** | A követelmények a következőket tartalmazzák: a tárolási fiók és egy tároló. Szükség esetén, ha csoportosítási célból a blob-nevek egy virtuális elnevezési konvenciót követnek, akkor a név virtuáliskönyvtár-részét a tárolóban található mappa neveként is megadhatja. A további információkat [A Blob Storage (előzetes verzió) indexelése](search-howto-indexing-azure-blob-storage.md) című rész tartalmazza. |
|**Azure Table Storage** | A követelmények a következőket tartalmazzák: a tárolási fiók és egy táblanév. Szükség esetén megadhat egy lekérdezést a táblák részhalmazának beolvasásához. A további információkat [A Table Storage (előzetes verzió) indexelése](search-howto-indexing-azure-tables.md) című rész tartalmazza. |

## Célindex testreszabása

Általában az adatkészletből kikövetkeztetett előzetes index. A séma elkészítéséhez mezőket adhat hozzá, szerkeszthet vagy törölhet. Továbbá mezőszinten attribútumokat állíthat be az index későbbi keresési viselkedésének meghatározása érdekében.

1. A **Célindex testreszabása** lapon adja meg a nevet és egy **kulcsot**, amelyet az egyes dokumentumok egyedi azonosításához kell használni. A kulcsnak karakterláncnak kell lennie. Ha a mezőértékek szóközöket vagy kötőjeleket tartalmaznak, ügyeljen arra, hogy az **Adatok importálása** lapon meg kell adnia a speciális beállításokat ezeknek a karaktereknek az érvényességét ellenőrző művelet letiltásához.

2. Tekintse át és módosítsa a fennmaradó mezőket. A mező neve és típusa általában már ki van töltve. Az adattípus módosítható.

3. Állítsa be az egyes mezőkhöz tartozó indexattribútumokat:

 - A lekérdezhető attribútum a keresési eredmények között visszaadja a mező értékét.
 - A szűrhető attribútum hivatkozhatóvá teszi az adott mezőt a szűrőkifejezésekben.
 - A rendezhető attribútum segítségével az adott mezőt használhatjuk egy rendezés alapjaként.
 - Az értékkorlátozó attribútum értékkorlátozott navigációt tesz lehetővé az adott mező esetében.
 - A kereshető attribútum lehetővé teszi a teljes szöveges keresést.
  
4. Nyelvi elemző mezőszinten történő megadásához kattintson az **Elemző** lapra. Ekkor csak a nyelvi elemzőket adhatja meg. Egyéni elemző vagy nem nyelvi elemző, például kulcsszó, minta és hasonló elemek használata esetén szükség van a kódra.

 - Kattintson a **Kereshető** elemre, hogy teljes szöveges keresést jelöljön ki a mezőhöz, és engedélyezze a Elemző legördülő listáját.
 - Válassza ki a kívánt elemzőt. Részletes információk: [Több nyelven elérhető dokumentumok indexének létrehozása](search-language-support.md).

5. A kijelölt mezőkre vonatkozó gépelés közbeni lekérdezési javaslatok engedélyezéséhez kattintson a **Javaslatok** elemre.


## Adatok importálása

1. Az **Adatok importálása** mezőben adja meg az indexelő nevét. Ne felejtse el, hogy az Adatok importálása varázsló terméke egy indexelő. Később, ha szeretné megtekinteni vagy módosítani, a portálról kiválaszthatja, és nem kell újból lefuttatnia a varázslót. 

2. Adja meg az ütemezést annak a regionális időzónának az alapján, amelyben a szolgáltatás létrejött.

3. Adja meg azokra a küszöbértékekre vonatkozó speciális beállításokat, amelyeknél az indexelés akkor is folytatódik, ha a dokumentumot elvetik. Ezenkívül azt is megadhatja, hogy a **kulcs**mezők tartalmazhatnak-e szóközöket és perjeleket.  

## Meglévő indexelő szerkesztése

A szolgáltatás irányítópultján kattintson duplán az Indexelő csempére az előfizetéshez létrehozott összes indexelő listájának kicsúsztatásához. Az indexelők futtatásához, szerkesztéséhez és törléséhez kattintson rájuk duplán. Lecserélheti az indexet egy másik létező indexre, módosíthatja az adatforrást, és beállíthatja az indexelés során érvényes hibaküszöböket.

## Meglévő index szerkesztése

Az Azure Search szolgáltatásban az index strukturális frissítéséhez az index újjáépítésére van szükség, ami az index törléséből, újbóli létrehozásából és az adatok újbóli betöltéséből áll. A strukturális frissítések közé tartozik az adattípusok módosítása és a mezők átnevezése vagy törlése.

Az újjáépítést nem igénylő szerkesztési műveletek közé tartozik az új mezők hozzáadása, illetve a pontozási profilok, a javaslatok vagy a nyelvelemzők módosítása. További információ: [Update Index](https://msdn.microsoft.com/library/azure/dn800964.aspx) (Index frissítése).

## Következő lépés

Az alábbi hivatkozások további információkat nyújtanak az indexelőkről:

- [Az Azure SQL Database indexelése](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [A DocumentDB indexelése](../documentdb/documentdb-search-indexer.md)
- [A Blob Storage (előzetes verzió) indexelése](search-howto-indexing-azure-blob-storage.md)
- [A Table Storage (előzetes verzió) indexelése](search-howto-indexing-azure-tables.md)



<!--Image references-->
[1]: ./media/search-import-data-portal/search-import-data-command.png




<!--HONumber=Sep16_HO4-->


