---
title: "Az Azure Search összetett adattípusú modellek hogyan |} Microsoft Docs"
description: "A beágyazott vagy hierarchikus adatstruktúrák modellezhető az Azure Search-index egybesimított sorhalmaz és gyűjtemények adattípus használatával."
services: search
documentationcenter: 
author: LiamCa
manager: pablocas
editor: 
tags: complex data types; compound data types; aggregate data types
ms.assetid: e4bf86b4-497a-4179-b09f-c1b56c3c0bb2
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: liamca
ms.openlocfilehash: d7a7400fe7470439dfa957f1ddb463e0a7f1a271
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Hogyan modell összetett adattípusú az Azure Search
Az Azure Search-index néha feltöltésére használt külső adatkészletek hierarchikus vagy beágyazott alépítményeit, amely nem felosztása szépen táblázatos sorhalmazt tartalmaz. Példa ilyen struktúrák előfordulhat, hogy több helyről és telefonszámok tartalmazza az egy ügyfél több színek és méretek egyetlen termékváltozat, több szerzők egy könyv, és így tovább. A modellezési feltételeit, ezen szerkezetek néven megjelenhet *összetett adattípusú*, *összetett adattípusú*, *összetett adattípusok*, vagy *összesített adattípusok*, csak hogy néhányat említsünk.

Összetett adattípusú nem natív módon támogatja az Azure Search, de bevált megoldás tartalmaz egy kétlépéses folyamat egybesimítását szerkezetét, és használata a **gyűjtemény** adattípus meglévő kölcsönökre a belső struktúrában. A cikkben leírt eljárást követve lehetővé teszi a tartalom keres, jellemzőalapú, és szűrhetők.

## <a name="example-of-a-complex-data-structure"></a>Összetett adatszerkezet – példa
Általában a szóban forgó adatok JSON- vagy XML-dokumentumot, vagy egy NoSQL-tároló, például az Azure Cosmos DB elem található. Szerkezete a kihívás ered, több gyermek elemeket kell keresni és szűrt rendelkezik.  A bemutató a megoldás kiindulási pontként hajtsa végre a következő JSON-dokumentum, amely számos olyan példaként ügyfelek:

~~~~~
[
  {
    "id": "1",
    "name": "John Smith",
    "company": "Adventureworks",
    "locations": [
      {
        "id": "1",
        "description": "Adventureworks Headquarters"
      },
      {
        "id": "2",
        "description": "Home Office"
      }
    ]
  }, 
  {
    "id": "2",
    "name": "Jen Campbell",
    "company": "Northwind",
    "locations": [
      {
        "id": "3",
        "description": "Northwind Headquarter"
      },
      {
        "id": "4",
        "description": "Home Office"
      }
    ]
}]
~~~~~

Amíg a mezőket "id" nevű, "name" és "Vállalati" csak egyszerűen képezhető le egy az egyhez típusú mezői belül az Azure Search-index, a "helyek" mező a helyek, hogy mindkét egy csoportja hely azonosítók, valamint a hely leírása tömböt tartalmaz. Fényében, hogy az Azure Search nem rendelkezik olyan adattípusú, amely támogatja ezt, igazolnia kell a következő modellre: Ez az Azure Search különböző módokon. 

> [!NOTE]
> Ezzel a módszerrel is blogbejegyzés ismerteti által Kirk Evans [Azure Cosmos DB indexelése az Azure Search](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/), amely mutatja a technika nevű, "az adatok egybesimítását", amely lehetővé teszi, akkor egy egy nevű mező `locationsID` és `locationsDescription` amelyek [gyűjtemények](https://msdn.microsoft.com/library/azure/dn798938.aspx) (vagy karakterláncok).   
> 
> 

## <a name="part-1-flatten-the-array-into-individual-fields"></a>1. lépés: A tömb egyes mezőkbe Egybesimítására
Ez az adatkészlet tervezhetők Azure Search-index, hozzon létre a beágyazott tartóelemeket egyes mezőket: `locationsID` és `locationsDescription` adatok típussal rendelkező [gyűjtemények](https://msdn.microsoft.com/library/azure/dn798938.aspx) (vagy karakterláncok). A mezők be kellene index "1" vagy "2" érték a `locationsID` a John Smith és az értékeket a "3" & "4" mezőben a `locationsID` Ilona Campbell mezőt.  

Az adatok Azure Search belül néz ki: 

![mintaadatokat, 2 sor](./media/search-howto-complex-data-types/sample-data.png)

## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>2. lépés: Az index definícióját gyűjtemény mező felvétele
A sémát indexeli az a mező definíciók hasonlóan néznének ki ebben a példában.

~~~~
var index = new Index()
{
    Name = indexName,
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("name", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("company", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("locationsId", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("locationsDescription", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true }
    }
};
~~~~

## <a name="validate-search-behaviors-and-optionally-extend-the-index"></a>Keresési viselkedések érvényesítése és kiterjeszti az index
Ha létrehozta az indexet, és az adatok betöltése, most tesztelheti a megoldás a dataset keresési lekérdezés végrehajtásának ellenőrzése. Minden egyes **gyűjtemény** mező lehet **kereshető**, **szűrhető** és **kategorizálható**. Érdemes, például a lekérdezések futtatása:

* Az Adventureworks központban dolgozó összes személyek keresése.
* Megszámlálása egy otthoni Office dolgozó személyek számát.  
* Annak a személynek egy otthoni irodában dolgozó bemutatják, milyen többi iroda működnek együtt az egyes helyeken a személyek számát.  

Ha ezzel a technikával egymástól esik esetén végre kell hajtani egy keresést, hogy mind a helyazonosító, valamint a hely leírása. Példa:

* Minden személyek keresése, ahol egy otthoni Office rendelkeznek, és egy helyet azonosító, a 4.  

Ha Emlékezzen vissza az eredeti tartalom ehhez hasonló keresést végrehajtani:

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

Azonban most, hogy külön mezőkbe azt választotta el az adatokat, tudunk afelől, hogy ha nincs lehetőség a Kezdőlap Office Ilona Campbell vonatkozik a `locationsID 3` vagy `locationsID 4`.  

Ebben az esetben kezelésére, ad meg, amely egyesíti az összes adat egyetlen gyűjtemény indexe másik mezőt.  A példa kedvéért nevezzük fog ebben a mezőben `locationsCombined` , és azt fogja a tartalmat egy `||` Bár választhat, amely úgy gondolja, hogy bármely elválasztó karakter a tartalom egyedi készletének lenne. Példa: 

![mintaadatokat, 2 sor elválasztóval](./media/search-howto-complex-data-types/sample-data-2.png)

Ennek segítségével `locationsCombined` mezőben azt most már képes még több lekérdezések, többek között:

* Egy szám irodában egy"otthoni" helyen lévő azonosítója "4" dolgozó személyek megjelenítése.  
* Egy otthoni irodában dolgozó személyek keresése helyen lévő "4" azonosítójú. 

## <a name="limitations"></a>Korlátozások
Ez a módszer akkor hasznos, ha több forgatókönyvet, de nincs minden esetben alkalmazható.  Példa:

1. Ha Ön az összetett adattípusú nem rendelkezik a statikus mezők halmaza alapján, és nem lehetett azon lehetséges típusait hozzárendelése egy mező. 
2. A beágyazott objektumok frissítése néhány extra munkát annak meghatározására, hogy mit kell frissíteni az Azure Search-index szükséges

## <a name="sample-code"></a>Mintakód
Látható egy példa egy összetett JSON-adatkészlet index Azure Search szolgáltatásba történő, és képes lekérdezések száma ehhez az adatkészlethez ezzel [GitHub-tárház](https://github.com/liamca/AzureSearchComplexTypes).

## <a name="next-step"></a>Következő lépés
[Natív támogatást az összetett adattípusú szavazzon](https://feedback.azure.com/forums/263029-azure-search) az Azure Search UserVoice lapon, és adjon meg semmilyen további bemeneti szeretné, hogy a szolgáltatás megvalósítási kapcsolatban fontolja meg. Is érhető el nekem közvetlenül a Twitteren: @liamca.

