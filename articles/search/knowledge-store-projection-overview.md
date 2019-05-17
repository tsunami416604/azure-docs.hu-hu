---
title: A Tudásbázis-tárolóban (előzetes verzió) – Azure Search leképezések használata
description: Mentse adatait és olyanná alakíthatja képi elemekben gazdag keresési eltérő híradásoknál AI-indexelési folyamatból
manager: eladz
author: vkurpad
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: vikurpad
ms.custom: seomay2019
ms.openlocfilehash: f1c7278909557dc92f86c5dfc1f190fddf33f607
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540806"
---
# <a name="working-with-projections-in-a-knowledge-store-in-azure-search"></a>A Tudásbázis-tárolóban az Azure Search leképezések használata

> [!Note]
> Tudásbázis store előzetes állapotban van, nem éles használatra szánt. A [REST API verzióját 2019-05-06-Preview](search-api-preview.md) ezt a szolgáltatást biztosít. Rendszer jelenleg nem .NET SDK-t támogatja.
>

Az Azure Search lehetővé teszi, hogy a tartalom Adatbővítés AI kognitív képességeket és indexelő részeként egyéni képesség révén. Végrehajtott információbeolvasás struktúra ad hozzá a dokumentumok és a keresés hatékonyabbá tétele. Sok esetben a jelentéstétellel dokumentumok hasznosak eltérő keresési, például a Tudásbázis adatbányászati forgatókönyvekhez.

Leképezések, részét képező [Tudásbázis store](knowledge-store-concept-intro.md), képi elemekben gazdag azt jelzi, hogy a fizikai tároló Tudásbázis adatbányászati célból menthetők nézet. Egy leképezési "kivetítését" az adatok egy formázásához, amely megfelel az igényeinek, megőrizve a kapcsolatokat, hogy az eszközök, mint például a Power BI tudja olvasni az adatokat, nincs további erőfeszítéssel teszi lehetővé. 

Leképezések a sorok és oszlopok az Azure Table storage-ban tárolt adatok, vagy az Azure Blob storage szolgáltatásban tárolt JSON-objektumok táblázatos, lehet. Meghatározhatja az adatok több leképezések, bővített folyamatban van. Ez akkor hasznos, ha azt szeretné, hogy az egyes használati esetek eltérően alakú ugyanazokat az adatokat. 

A Tudásbázis áruház kétféle típusú leképezések támogatja:

+ **Táblák**: Legjobb jelenik meg a sorok és oszlopok adatok esetén tábla leképezések lehetővé teszik egy sematikus alakzat- és leképezéspéldányok meghatározásához a Table storage-ban. 

+ **Objektumok**: Az adatok és a végrehajtott információbeolvasás JSON-reprezentációja van szüksége, objektum leképezések blobként menti.

A környezetben definiált leképezések megtekintéséhez végighaladhat [hogyan Tudásbázis store használatának első lépései](knowledge-store-howto.md)

## <a name="projection-groups"></a>Leképezés csoportok

Bizonyos esetekben szüksége lesz a bővített adatok különböző alakzatok különböző célok eléréséhez a projekthez. A Tudásbázis tároló lehetővé teszi, hogy meghatározzák a több leképezések. Leképezés csoportnak van a következő főbb jellemzőit kölcsönös kizárólagosság és rokonsági fok.

### <a name="mutually-exclusivity"></a>Kizárólagosság kölcsönösen

Előre jelzett költségről rendezhet egy csoportba az összes tartalom független a többi leképezési csoportokba előre jelzett költségről adatok. Ez azt jelenti, hogy ugyanazok az adatok másképpen lesznek formázva, de ismétlődő minden leképezése csoport is. 

Egy leképezése csoportok érvényesül pedig kivetítés csoporttal leképezéstípusait, kölcsönös kizárólagosságot az. Csak akkor definiálhat, vagy a tábla leképezések, vagy az objektum leképezések egyetlen csoporton belül. Ha azt szeretné, táblák és objektumok is, egy leképezési csoport a táblák és a egy második leképezése csoport objektumok megadása.

### <a name="relatedness"></a>Rokonsági fok

Előre jelzett költségről egyetlen leképezése csoportban lévő összes tartalmat megőrzi az adatok között kapcsolatok. Kapcsolatok egy létrehozott kulcs alapulnak, és minden gyermek csomópont megőrzi a szülőcsomópont hivatkozást. Kapcsolatok nem leképezése csoportra is kiterjednek, és táblák vagy egy leképezési csoportban létrehozott objektumok rendelkezik létrehozott többi leképezési csoportokban lévő adatok nem áll kapcsolatban.

## <a name="input-shaping"></a>Adjon meg megfelelő átalakítását
Az adatok beolvasása a megfelelő alakzat vagy struktúra hatékony kulcs használata, táblák és objektumok legyen. Képesség kulcsfontosságú néven jelenik meg arra, hogy formázásához vagy struktúra alapján hogyan azt tervezi, hogy eléri és használja azt a **Shaper** szakértelem készségeitől belül.  

Leképezések használata egyszerűbb, határozza meg, ha egy objektum, amely megfelel a sémát a leképezés Adatbővítés fájában. A frissített [Shaper szakértelem](cognitive-search-skill-shaper.md) lehetővé teszi, hogy a compose-felderítési bővítést fa csomópontjai objektumot, és a egy új csomópont alatt szülő őket. A **Shaper** szakértelem lehetővé teszi, hogy a beágyazott objektumok komplex típust határoznak meg.

Ha egy új alakzat definiálva kell meg a projekt összes elemét tartalmazó, mostantól használhatja az alakzatot a leképezések forrásaként, vagy egy másik szakértelem bemeneteként.

## <a name="table-projections"></a>Tábla kivetítések

Mert így egyszerűbb importálása, javasoljuk, hogy az adatok feltárására a Power BI tábla-leképezések. Ezenkívül a tábla leképezések lehetővé teheti, a változtatja meg a számosságot közötti kapcsolat. 

Kivetítheti egyetlen dokumentum az indexben lévő több táblára felosztva megőrzi a kapcsolatokat. Több tábla Segédben a teljes alakzat lesz lehetséges a projekció minden táblához, ha egy alárendelt csomópont nem ugyanazon a csoporton belül egy másik tábla forrása.

### <a name="defining-a-table-projection"></a>Egy tábla leképezése meghatározása

Egy tábla leképezése belül meghatározásakor a `knowledgeStore` elem, a képességek alkalmazási lehetőségét, indítsa el a felderítési bővítést fa csomópontjait a tábla forrás leképezésével. Ebben a csomópontban általában kimenete egy **Shaper** szakértelem táblákba projektre kell adott alakzat előállításához képességek a listához hozzáadott. A csomópont úgy dönt, hogy a projekt is lehet szeletelt projekthez több táblára felosztva. A táblák definíciója, hogy a projekt kívánt táblák listáját. 

Minden tábla három tulajdonságok szükségesek:

+ Táblanév: Az Azure Storage-ban a tábla neve.

+ generatedKeyName: Az oszlop nevét, a kulcs, amely egyedileg azonosítja ezt a sort.

+ Forrás: A csomópont a Adatbővítés fából, a végrehajtott információbeolvasás forrásául. Ez általában egy shaper kimenetét, de a kimenet a képességek bármelyikének lehet.

Íme egy példa tábla leképezések.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
      
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [
            { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
          ]
        },
        {
          "objects": [
            
          ]
        }
      ]
    }
}
```
Ahogyan az ebben a példában, a kulcskifejezéseket és entitások különböző táblákba modellezése eltér, és tartalmaz egy hivatkozást a szülő (MainTable) minden egyes sorára. 

Az alábbi ábrán eszköztáblára mutató hivatkozás a Caselaw gyakorlására [hogyan kezdheti el a Tudásbázis store](knowledge-store-howto.md). Egy forgatókönyv, ahol egy esetet rendelkezik több véleményeket, és minden véleményt van bővített benne foglalt entitások azonosítása a leképezések sikerült modellje, itt látható módon.

![Entitások és kapcsolatok táblákban](media/knowledge-store-projection-overview/TableRelationships.png "tábla leképezések a kapcsolatok modellezését")

## <a name="object-projections"></a>Objektum-leképezések

Objektum leképezések jelképezik JSON Adatbővítés fa, amely képes bármely olyan csomópontról beolvasva. Sok esetben azonos **Shaper** szakértelem, amely létrehoz egy tábla leképezése egy objektum leképezése létrehozásához használható. 

```json
{
 
    "name": "your-skillset",
    "skills": [
      …your skills
      
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [
            {
              "storageContainer": "Reviews", 
              "format": "json", 
              "source": "/document/Review", 
              "key": "/document/Review/Id" 
            }
          ]
        }
      ]
    }
}
```

Egy objektum leképezése létrehozásához szükséges néhány objektumhoz kapcsolódó attribútumok:

+ storageContainer: A tárolóban, ahol az objektumok menti a rendszer
+ Forrás: A csomópont a Adatbővítés fa, amely a leképezés a gyökér elérési útja
+ Kulcs: Egy elérési útja, amely egy egyedi kulcsot kell tárolni az objektumot jelöli. A blob nevét, a tároló létrehozásához használandó.

## <a name="projection-lifecycle"></a>Leképezés életciklusa

A leképezések rendelkeznek egy életciklussal, amely a forrásadatokat az adatforrás van kötve. Az adatok frissítése és ismételt indexelt, mivel a leképezések a végrehajtott információbeolvasás annak biztosítása, a leképezések idővel konzisztenssé váljanak az adatok az adatforrásból származó eredményei frissülnek. A leképezések öröklik a már konfigurálta az index törlésre vonatkozó házirendet. 

## <a name="using-projections"></a>Leképezések használata

Az indexelő futtatása után a tervezett adatokat leképezések keresztül a megadott táblák vagy tárolók olvashat. 

Analytics feltárása a Power bi-ban rendkívül egyszerű csak az Azure Table storage adatforrásként. Vizualizációk egy készlete nagyon egyszerűen hozhat létre, az adatokon belül a kapcsolatok optimalizálásával.

Azt is megteheti, ha a bővített adatok használata az adatok adatelemzési van szüksége, próbálja meg [az adatok betöltése az blobok az Pandas DataFrame](../machine-learning/team-data-science-process/explore-data-blob.md).

Végül exportálja az adatokat a Tudásbázis áruházból kell, ha az Azure Data Factory összekötővel rendelkezik, amelyek exportálja az adatokat, és megnyitja azt az adatbázist a választott. 

## <a name="next-steps"></a>További lépések

Következő lépésként hozzon létre az első Tudásbázis store mintaadatokat és utasításokat.

> [!div class="nextstepaction"]
> [A Tudásbázis-tároló létrehozása](knowledge-store-howto.md).