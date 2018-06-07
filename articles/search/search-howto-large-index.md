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
ms.openlocfilehash: cb99096c1217fca1527b17946dc12390ddf3f62c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655221"
---
# <a name="how-to-scale-out-indexing-in-azure-seearch"></a>Útmutató az Azure Seearch indexelő kibővített

Az adatkötetek nő, illetve feldolgozási igényeinek változását, előfordulhat, hogy egyszerű [újraépíti és feladatok újraindexelés](search-howto-reindex.md) nincsenek-e elegendő. 

Első lépésként érdekében megnövekedett igényekre, azt javasoljuk, hogy állítsa a [méretezés és a kapacitás](search-capacity-planning.md) a meglévő szolgáltatást keretein belül. 

A második lépés, ha használható [indexelők](search-indexer-overview.md), méretezhető indexelő mechanizmusok hozzáadja. Indexelő egy beépített ütemezési, amely lehetővé teszi ki rendszeres időközönként indexelő csomagonkénti rendelkeznek, illetve kibővíthetők feldolgozási túl a 24 órás időszak. Emellett ha adatforrás-definíciók, indexelők segítséget párhuzamossági űrlap elérése adatok particionálása és ütemezések használatával hajtható végre párhuzamosan.

### <a name="scheduled-indexing-for-large-data-sets"></a>A nagy méretű adatkészletekhez indexelő ütemezett

Ütemezés egy olyan fontos mechanizmus nagy adatkészletek és lassan futó elemzések, például a lemezkép elemzés kognitív keresési-feldolgozási folyamat feldolgozásra. Az indexelő feldolgozási működik egy 24 órás időszakban. 24 órán belül befejezése meghiúsul, feldolgozás, ha az indexelő ütemezés viselkedésmódját előnyére is működik. 

Úgy lett kialakítva beütemezve adott időközönként indexelési elindul egy feladat a következő ütemezett időközönként folytatása előtt általában befejezése. Azonban ha feldolgozási időszakán belül nem fejeződik be, a indexelője (mert idő). A nyomon ahol utolsó abbamaradtak, a rendszer megőrzéséről a feldolgozási folytatása a következő időközönként, ha ez történik. 

Gyakorlatilag az index terhelések átfedés akár több napon egy 24 órás ütemezés szerint az indexelő helyezhet el. Indexelő esetében a következő 24 órás stint folytatása, ha azt a legutóbbi ismert helyes dokumentum újraindul. Ezzel a módszerrel az indexelő napon belül végre minden feldolgozatlan dokumentumok feldolgozása több keresztül működik a módon dokumentumban Hátralék keresztül. Ezzel a módszerrel kapcsolatos további információkért lásd: [nagy adatkészletek indexelő](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)

<a name="parallel-indexing"></a>

## <a name="parallel-indexing"></a>Párhuzamos indexelő

Egy második rendszer stratégia indexelő párhuzamos beállítása. A nem rendszeres számításilag intenzív követelményeket, például a beolvasott dokumentumokat a kognitív keresési folyamat OCR indexelő stratégia indexelő párhuzamos lehet a megfelelő módszer, hogy adott cél. Egy kognitív keresési dúsító sorban kép elemzés és természetes nyelvű feldolgozási is hosszú ideig futniuk. Párhuzamos indexelő olyan szolgáltatás, amely nem egy időben kezeli a következő lekérdezési kérelmek az oka az lehet, hogy lassú-feldolgozási tartalom nagy törzs keresztül végzett munka kivitelezhető lehetőség. 

A párhuzamos feldolgozást végző stratégiát ezeket az elemeket:

+ Kiszámolja a forrásadatok között több tároló vagy ugyanabban a tárolóban található több virtuális mappákat. 
+ Minden egyes mini adatkészlet leképezése egy [dátum forrás](https://docs.microsoft.com/rest/api/searchservice/create-data-source), a saját párosított [indexelő](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ Kognitív keresési lásd azonos [skillset](https://docs.microsoft.com/rest/api/searchservice/create-skillset) minden indexelő-definícióban.
+ Írja be a azonos cél search-index. 
+ Összes indexelő futtatásához egyidejűleg ütemezni.

> [!Note]
> Az Azure Search nem támogatja a hogy replikák vagy adott munkaterhelés konkrét partíciókat. A nagy egyidejű indexelő kockázatát túlterhelése van a rendszer a lekérdezési teljesítmény rovására. Ha rendelkezik egy tesztkörnyezetben, alkalmazzon a párhuzamos indexelő nincs először a mellékhatásokkal megértése.

## <a name="configure-parallel-indexing"></a>Párhuzamos indexelés konfigurálása

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