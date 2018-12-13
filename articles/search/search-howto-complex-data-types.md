---
title: Összetett adattípusok modellezése – Azure Search hogyan
description: A beágyazott vagy hierarchikus datové struktury modellezhető az Azure Search-index egybesimított sorkészlet és gyűjtemények adattípus használatával.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2017
ms.custom: seodec2018
ms.openlocfilehash: 973623d6c4cb57518af2012bccf67c969146d23c
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53311983"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Összetett adattípusok modellezése az Azure Search hogyan
Azure Search-index feltöltéséhez néha használt külső adathalmazok, amelyek nem felosztása eligazíthatja táblázatos sorhalmaz hierarchikus vagy beágyazott alépítményeit tartalmazza. Példák ilyen struktúrák előfordulhat, hogy több helyről és telefonszámok tartalmazza az egyetlen ügyfél több színt és méretet egyetlen termékváltozat, több készítői könyvet, és így tovább. Feltételek modellező, láthatja a továbbiakban ezen szerkezetek *összetett adattípusok*, *összetett adattípusok*, *összetett adattípusok*, vagy *összesített az adattípusok*, hogy néhányat említsünk.

Összetett adattípusok nem natív módon támogatja az Azure Search szolgáltatásban, de egy jól bevált megoldást tartalmaz, az egybesimítás struktúráját, majd egy kétlépéses folyamat egy **gyűjtemény** adattípust, belső szerkezetét pótlására. A cikkben bemutatott módszert követve lehetővé teszi a tartalom keres, jellemzőalapú, és szűrhetők.

## <a name="example-of-a-complex-data-structure"></a>Összetett adatstruktúra – példa
A szóban forgó adatok általában JSON vagy XML-dokumentumot, vagy elemek, például az Azure Cosmos DB egy nosql-alapú tárolóban található. A kihívás szerkezete, nem kell keresni és szűrt több gyermekelemek jelentik.  A megoldás csoportjaként kiindulási pontként hajtsa végre a következő JSON-dokumentum, amely a partnerek mutat be példaként:

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

Bár a mezők "id" nevű, "name" és "Céges" egyszerűen leképezhetők egy az egyhez belül az Azure Search-index a mezők, a "hely" mező a helyeken, hogy mindkét egy csoportja hely azonosítóit, valamint a hely leírása egy számtömböt. Tekintve, hogy az Azure Search nincs adattípusú, amely támogatja ezt, szükségünk modell Ez az Azure Search szolgáltatásban is. 

> [!NOTE]
> Ezzel a módszerrel is blogbejegyzés ismerteti Kirk Evans által [Azure Cosmos DB indexelése az Azure Search](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/), amely jelzi, hogy olyan módszer, nevű, "az adatok egybesimítását", amely lehetővé teszi, hogy mezője `locationsID` és `locationsDescription` amelyek [gyűjtemények](https://msdn.microsoft.com/library/azure/dn798938.aspx) (vagy karakterláncok).   
> 
> 

## <a name="part-1-flatten-the-array-into-individual-fields"></a>1. rész: Simítja egybe a tömböt az egyes mezők
Azure Search-index, amely ehhez az adatkészlethez megfelelő, hozzon létre egyéni mezőket a beágyazott alépítményhez: `locationsID` és `locationsDescription` típusú adatok [gyűjtemények](https://msdn.microsoft.com/library/azure/dn798938.aspx) (vagy karakterláncok). Az ezekben a mezőkben be kellene index az "1" és a '2' értéket a `locationsID` adatmérték-mezőt Kovács János és "3" & "4". az értékeket a `locationsID` Ilona Campbell mezőjét.  

Az adatait az Azure Search kellene kinéznie: 

![mintaadatok, 2 sor](./media/search-howto-complex-data-types/sample-data.png)

## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>2. rész: Az Indexdefiníció egy gyűjtemény mező hozzáadása
Az indexsémát az Meződefiníciók ebben a példában hasonlóan néznének ki.

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

## <a name="validate-search-behaviors-and-optionally-extend-the-index"></a>Ellenőrizze a keresési viselkedések, és kiterjeszti az index
Ha létrehozta az indexet, és az adatok betöltése, most tesztelheti a megoldás a keresési lekérdezés végrehajtása az élelmiszer-ellenőrzése. Minden egyes **gyűjtemény** mező lehet **kereshető**, **szűrhető** és **kategorizálható**. Meg kell tudni például a lekérdezések futtatása:

* Az összes "Adventureworks központjában" dolgozó személyek keresése.
* Megszámlálása egy otthoni irodai dolgozó személyek számát.  
* Egy otthoni irodában dolgozó személyek milyen más iroda működnek együtt az egyes helyeken a személyek számát mutatják.  

Ha ezt a technikát későbbre esik akkor, ha kell tennie egy keresést, amely ötvözi az a hely azonosítója és is a hely leírása. Példa:

* Amelyek egy otthoni Office összes személyek keresése és a egy hely azonosítója a 4-ből.  

Ha a már ismert módon kikeresi az eredeti tartalom:

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

Azonban most, hogy rendelkezik az adatok elkülönített külön mezőkben, hogy akkor sem afelől, hogy ha a Kezdőlap Office Ilona Campbell vonatkozik a `locationsID 3` vagy `locationsID 4`.  

Ebben az esetben kezelése érdekében adja meg az index, amely egyesíti az összes adat egy gyűjteményt, amely egy másik mező.  A példánkban ez a mező fog nevezzük `locationsCombined` , és hogy a tartalmat az elkülönített egy `||` Bár választhat, amelyek úgy gondolja, hogy bármely elválasztó lenne a tartalom egyedi karakterkészlet. Példa: 

![mintaadatok, 2 sor elválasztóval](./media/search-howto-complex-data-types/sample-data-2.png)

Ez `locationsCombined` mezőben azt most már képes még több lekérdezéseket, például:

* A "Home irodában" helyen azonosítója "4" dolgozó személyek számát mutatják.  
* Keresse meg a kezdőlap irodában dolgozó személyek "4" azonosítójú helyét. 

## <a name="limitations"></a>Korlátozások
Ez a módszer akkor hasznos, ha számos forgatókönyv esetében, de nem alkalmazható, minden esetben.  Példa:

1. Ha az összetett típusban nem rendelkezik statikus a mezők halmaza, és semmilyen módon nem lehet egyetlen mező összes lehetséges típusait leképezése történt. 
2. A beágyazott objektumok frissítése szükséges elvégzését meghatározni, hogy mit kell frissíteni kell az Azure Search-index

## <a name="sample-code"></a>Mintakód
Látható egy példa JSON adatkészlet összetett index Azure Search szolgáltatásba történő, és jelenleg ez az adatkészlet-lekérdezések száma végre [GitHub-adattárat](https://github.com/liamca/AzureSearchComplexTypes).

## <a name="next-step"></a>Következő lépés
[Összetett adattípusok natív támogatását szavazzon](https://feedback.azure.com/forums/263029-azure-search) a az Azure Search UserVoice lapon, és adjon meg semmilyen további bemenetet, adja meg, hogy fontolja meg a szolgáltatás végrehajtásával kapcsolatos. Is elérhető számomra közvetlenül a Twitteren: @liamca.

