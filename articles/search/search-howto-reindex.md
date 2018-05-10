---
title: Egy Azure Search index vagy frissítés kereshető tartalom újraépítése |} Microsoft Docs
description: Új elem hozzáadása, frissítse a meglévő elemeket vagy dokumentumok vagy rebuild teljes vagy részleges növekményes indexelő frissítése az Azure Search-index elavult dokumentumok törlése.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.openlocfilehash: f38054eaf2829149a496f840366b6f2f9e03e12b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-rebuild-an-azure-search-index"></a>How Azure Search-index újraépítése

Az index-változtatások újraépítése módosítása az indexet az Azure Search szolgáltatás a fizikai kifejezése struktúrája. Ezzel ellentétben index frissítése a csak tartalom frissítés meghatározó külső adatforrásból a legutóbbi módosítások átvételéhez. A cikkben irány indexek frissítésével szerkezetileg és lényegileg hatályba.

Olvasási és írási engedélye a szolgáltatási szint az index frissítések szükségesek. Programozott módon kérjen REST vagy a .NET API-k teljes rebuild vagy növekményes indexelése tartalmat, a frissítési beállítások megadása a paraméterekkel. 

Indexek frissítések általában igény. Azonban a forrás-specifikus feltöltve indexek [indexelők](search-indexer-overview.md), beépített ütemezési is használhatja. Az ütemező dokumentum frissítési akár 15 perces bármilyen intervallum és a szükséges mintát gyakran támogatja. Frissítési gyorsabban szükséges index frissítések terjesztése manuálisan, lehet, hogy a tranzakciók, mind a külső adatforráshoz, és az Azure Search-index frissítése egyidejűleg dupla-írási keresztül.

## <a name="full-rebuilds"></a>Teljes újraépíti

Számos különböző típusú frissítések egy teljes rebuild szükség. A teljes rebuild index, mind az adatokat, és a metaadatokat, a külső adatforrásokból index elszaporításával követ törlésének hivatkozik. Programozott módon [törlése](https://docs.microsoft.com/rest/api/searchservice/delete-index), [létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index), és [töltse be újra](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) azt, hogy az index. 

Utáni újraépítése, ne feledje, hogy ha rendelkezik lett tesztelni kívánt lekérdezési mintáknak és profilok pontozási, számíthat variation a lekérdezés eredményei között, ha az alapul szolgáló tartalom módosultak.

## <a name="when-to-rebuild"></a>Mikor kell építeni

A gyakori, teljes terv újraépíti aktív fejlesztése során, amikor index sémák fluxus állapotban vannak.

| Módosítás | Építse újra állapota|
|--------------|---------------|
| Módosítsa a mezőnév adattípus, vagy a [indexattribútumok](https://docs.microsoft.com/rest/api/searchservice/create-index) | Egy mező definition általában módosítása azt eredményezi azok háromszorosa rebuild büntetést, kivételével ezek [indexattribútumok](https://docs.microsoft.com/rest/api/searchservice/create-index): lekérhető, SearchAnalyzer, SynonymMaps. Attribútumot is hozzáadhat a lekérhető SearchAnalyzer és SynonymMaps meglévő mezőhöz anélkül, hogy az index újraépítése.|
| A mező hozzáadása | Az Újraépítés szigorú követelmény. Meglévő indexelt dokumentumok az új mező null értéket kap. Egy későbbi ismételt indexelése a forrásadatok értékek cserélje le az Azure Search által hozzáadott nullák. |
| Mező törlése | Az Azure Search-index közvetlenül egy mező nem törölhető. Ehelyett az alkalmazás figyelmen kívül hagyása elkerülése érdekében használja a "törölt" mező szükséges. Fizikailag a mező meghatározását és a tartalom továbbra is az index a séma, amely kihagyja a szóban forgó mező használatával az index újraépítése addig, amíg.|

> [!Note]
> Egy rebuild is szükség, ha a rétegek vált. Ha bármikor további kapacitás mellett dönt, nem a helyben történő frissítést. Egy új szolgáltatást kell létrehozni az új kapacitás ponton, és indexek kell kialakítani, a teljesen új szolgáltatás. 

## <a name="partial-or-incremental-indexing"></a>Részleges vagy növekményes indexelő

Ha az index éles környezetben, lesz aktuális növekményes indexelő, általában a discernable szolgáltatás szolgáltatások. Részleges vagy növekményes indexelő, amely szinkronizálja egy keresési indexet meghatározó adatforrás tartalom állapotát a tartalmát csak tartalom munkaterhelés. A dokumentum felvett vagy törölt a forráshelyen vagy az indexhez törlésekor. A kódban, hívja az [hozzáadása, frissítése vagy törlése dokumentumok](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) művelet, vagy a .NET-megfelelője.

> [!Note]
> Amely a külső adatforrások bejárható indexelők használatakor a forrásrendszerek változások követése mechanizmusok növekményes indexelés használja. A [Azure Blob Storage tárolóban](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection), egy `lastModified` mezővel. A [Azure Table storage](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection), `timestamp` ugyanazt a célt szolgálja. Hasonlóképpen, mindkettő [Azure SQL Database indexelő](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) és [Azure Cosmos DB indexelő](search-howto-index-cosmosdb.md#indexing-changed-documents) mezőket a sor frissítések megjelölése rendelkezik. Az indexelő kapcsolatos további információkért lásd: [indexelő áttekintése](search-indexer-overview.md).

## <a name="scale-out-indexing"></a>Kibővített indexelő

Az adatkötetek növekedhet és feldolgozási igényeinek változását, előfordulhat, hogy egyszerű újraépíti és feladatok újraindexelés olyan nem elegendő. Első lépésként érdekében megnövekedett igényekre, azt javasoljuk, hogy állítsa a [méretezés és a kapacitás](search-capacity-planning.md) a meglévő szolgáltatást keretein belül. 

Ha használhatja [indexelők](search-indexer-overview.md), további kibővített mechanizmusok elérhetővé válnak. Indexelő egy beépített ütemezési, amely lehetővé teszi ki rendszeres időközönként indexelő csomagonkénti rendelkeznek, illetve kibővíthetők feldolgozási túl a 24 órás időszak. Emellett ha adatforrás-definíciók, indexelők segítséget párhuzamossági űrlap elérése adatok particionálása és ütemezések használatával hajtható végre párhuzamosan.

### <a name="scheduled-indexing-for-large-data-sets"></a>A nagy méretű adatkészletekhez indexelő ütemezett

Ütemezés egy olyan fontos mechanizmus nagy adatkészletek és lassan futó elemzések, például a lemezkép elemzés kognitív keresési-feldolgozási folyamat feldolgozásra. Az indexelő feldolgozási működik egy 24 órás időszakban. 24 órán belül befejezése meghiúsul, feldolgozás, ha az indexelő ütemezés viselkedésmódját előnyére is működik. 

Úgy lett kialakítva beütemezve adott időközönként indexelési elindul egy feladat a következő ütemezett időközönként folytatása előtt általában befejezése. Azonban ha feldolgozási időszakán belül nem fejeződik be, a indexelője (mert idő). A nyomon ahol utolsó abbamaradtak, a rendszer megőrzéséről a feldolgozási folytatása a következő időközönként, ha ez történik. 

Gyakorlatilag az index terhelések átfedés akár több napon egy 24 órás ütemezés szerint az indexelő helyezhet el. Indexelő esetében a következő 24 órás stint folytatása, ha azt a legutóbbi ismert helyes dokumentum újraindul. Ezzel a módszerrel az indexelő napon belül végre minden feldolgozatlan dokumentumok feldolgozása több keresztül működik a módon dokumentumban Hátralék keresztül. Ezzel a módszerrel kapcsolatos további információkért lásd: [nagy adatkészletek indexelő](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Párhuzamos indexelő

Egy második rendszer stratégia indexelő párhuzamos beállítása. A nem rendszeres számításilag intenzív követelményeket, például a beolvasott dokumentumokat a kognitív keresési folyamat OCR indexelő stratégia indexelő párhuzamos lehet a megfelelő módszer, hogy adott cél. Egy kognitív keresési dúsító sorban kép elemzés és természetes nyelvű feldolgozási is hosszú ideig futniuk. Párhuzamos indexelő olyan szolgáltatás, amely nem egy időben kezeli a következő lekérdezési kérelmek az oka az lehet, hogy lassú-feldolgozási tartalom nagy törzs keresztül végzett munka kivitelezhető lehetőség. 

A párhuzamos feldolgozást végző stratégiát ezeket az elemeket:

+ Kiszámolja a forrásadatok között több tároló vagy ugyanabban a tárolóban található több virtuális mappákat. 
+ Minden egyes mini adatkészlet leképezése egy [dátum forrás](https://docs.microsoft.com/rest/api/searchservice/create-data-source), a saját párosított [indexelő](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ Kognitív keresési lásd azonos [skillset](ref-create-skillset.md) minden indexelő-definícióban.
+ Írja be a azonos cél search-index. 
+ Összes indexelő futtatásához egyidejűleg ütemezni.

> [!Note]
> Az Azure Search nem támogatja a hogy replikák vagy adott munkaterhelés konkrét partíciókat. A nagy egyidejű indexelő kockázatát túlterhelése van a rendszer a lekérdezési teljesítmény rovására. Ha rendelkezik egy tesztkörnyezetben, alkalmazzon a párhuzamos indexelő nincs először a mellékhatásokkal megértése.

### <a name="configure-parallel-indexing"></a>Párhuzamos indexelés konfigurálása

Az indexelő feldolgozási kapacitás lazán alapul egy indexelő alrendszer szolgáltatás tárolóegységekhez (SU) a keresési szolgáltatás által használt. Több egyidejű indexelők is előfordulhatnak, az Azure Search szolgáltatás alapszintű vagy Standard rétegek, hogy legalább két replika lett kiépítve. 

1. Az a [Azure-portálon](https://portal.azure.com), a search szolgáltatás irányítópultján **áttekintése** lapon ellenőrizze a **tarifacsomag** lehetővé teszi, párhuzamos indexelő megerősítéséhez. A Basic és Standard rétegek több replika kínálnak.

2. A **beállítások** > **méretezési**, [növelje a replikák](search-capacity-planning.md) párhuzamos feldolgozás: minden indexelő munkaterhelés számára egy további replikát. Hagyja bejelölve a meglévő lekérdezést kötet elegendő. Az indexelő lekérdezés munkaterhelések feláldozása nincs helyes kompromisszumot.

3. Az Azure Search indexelők érheti szinten több tároló adatok terjesztése. Ennek oka lehet az Azure SQL Database, az Azure Blob storage több tároló vagy több gyűjtemény több tábla. Adja meg mindegyik táblához vagy a tároló egy adatforrás-objektum.

4. Hozza létre, és több indexelők párhuzamos ütemezése:

   + Tegyük fel, a szolgáltatás hat replikával. Konfigurálja a hat indexelők, mindegyik egy adatforrást, amely az adathalmaz a teljes adatkészlet 6 – kétutas valószínűségét a egy-hatodik leképezve. 

   + Minden egyes indexelő mutasson ugyanazt az indexet. Kognitív keresési munkaterhelések esetén az azonos skillset minden indexelő mutasson.

   + Minden indexelő definíció, belül futásidejű végrehajtási ugyanilyen mintájú ütemezni. Például `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` ütemezés létrehozása a nyolc órás időközönként futó összes indexelő 2018-05-15.

A megadott időpontban összes indexelő végrehajtása, az adatok betöltése fokozatokká (Ha a konfigurált kognitív keresési adatcsatorna) alkalmazása és az index írása megkezdéséhez. Az Azure Search nem zárolja az index a frissítéseket. Egyidejű írási műveletek kezelik, egy adott írási első kísérlet alkalmával sikertelen ismételt próbálkozás után.

> [!Note]
> Replikák növelését, vegye figyelembe a partíciók száma növelése, ha az index mérete jelentősen megemelkedhet van vetítve. Partíció tárolhat szeletek indexelt tartalom; a további partíciók van, annál kisebb a szelet mindegyik rendelkezik tárolásához.

## <a name="see-also"></a>Lásd még

+ [Az indexelő áttekintése](search-indexer-overview.md)
+ [A portál indexelő](search-import-data-portal.md)
+ [Az Azure SQL Database indexelő](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB-indexelő](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage-indexelő](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage-indexelő](search-howto-indexing-azure-tables.md)
+ [Az Azure Search biztonsági](search-security-overview.md)