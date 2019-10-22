---
title: Kivetítések használata a Knowledge Store-ban (előzetes verzió) – Azure Search
description: A kibővített adatok mentése és formázása a mesterséges intelligencia indexelési folyamatból a kereséstől eltérő forgatókönyvekben való használatra
manager: nitinme
author: vkurpad
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: vikurpad
ms.openlocfilehash: c5fb547b18bc4014f91341070f49c4af84c01005
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "71265182"
---
# <a name="working-with-projections-in-a-knowledge-store-in-azure-search"></a>A kivetítések használata a Azure Searchban található Knowledge Store-ban

> [!Note]
> A Knowledge áruház előzetes verzióban érhető el, és nem éles használatra készült. A [REST API 2019-05-06-es verziójának előzetes verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nincs .NET SDK-támogatás.
>

Azure Search lehetővé teszi a tartalom-fejlesztést AI kognitív ismeretek és egyéni képességek révén az indexelés részeként. A bővítések struktúrát adhatnak a dokumentumokhoz, és hatékonyabbá tehetik a keresést. Sok esetben a dúsított dokumentumok a kereséstől eltérő forgatókönyvek esetén hasznosak, például a Knowledge Mining esetében.

A kivetítések, a [Knowledge Store](knowledge-store-concept-intro.md)egy összetevője, a kibővített dokumentumok nézetei, amelyek a fizikai tárterületre menthetők a tudás-bányászati célokra. A kivetítés lehetővé teszi, hogy az adatai egy olyan alakzatba kerüljenek, amely igazodik az igényeihez, és megőrzi a kapcsolatokat, hogy az eszközök, például a Power BI további erőfeszítés nélkül is beolvassák az adatait. 

A vetítés táblázatos lehet, az Azure Table Storage-ban sorokban és oszlopokban tárolt adatokat, illetve az Azure Blob Storage-ban tárolt JSON-objektumokat. Több kivetítést is megadhat az adataihoz, mivel azok gazdagítva vannak. Ez akkor hasznos, ha ugyanazt az adatformátumot szeretné használni az egyes használati esetekben. 

Az áruház két típusú kivetítést támogat:

+ **Táblák**: a táblázatos kivetítések lehetővé teszik egy sematikus-alakzat vagy-leképezés definiálását a táblázatos tárolóban. 

+ **Objektumok**: Ha az adatai és a bővítések JSON-ábrázolására van szüksége, az objektum-vetítések blobként lesznek mentve.

Ha meg szeretné tekinteni a kontextusban definiált kivetítéseket, tekintse át az [Ismerkedés a Knowledge Store szolgáltatással](knowledge-store-howto.md)című témakört.

## <a name="projection-groups"></a>Kivetítési csoportok

Bizonyos esetekben különböző alakzatokban kell kibővíteni a dúsított adatait, hogy azok megfeleljenek a különböző célkitűzéseknek. A Tudásbázis segítségével több kivetítési csoportot is meghatározhat. A kivetítési csoportok az alábbi fő jellemzőkkel rendelkeznek a kölcsönös kizárólagosság és a kapcsolódó jelleg tekintetében.

### <a name="mutually-exclusivity"></a>Kölcsönösen kizárólagosság

Az egyetlen csoportba tartozó összes tartalom független a többi kivetítési csoportba tervezett adatokat. Ez azt jelenti, hogy ugyanazokat az adatszerkezeteket kell megismételni, amelyeket az egyes vetületi csoportoknál még meg is ismétlik. 

A kivetítési csoportokban kikényszerített egyik korlátozás a kivetítési csoportok kölcsönös KIZÁRÓLAGOSSÁGA. Egyetlen csoporton belül csak táblázat-vagy objektum-kivetítéseket lehet meghatározni. Ha a táblákat és az objektumokat is szeretné használni, adjon meg egy leképezési csoportot a táblákhoz, és egy második leképezési csoportot az objektumok számára.

### <a name="relatedness"></a>Rokonság

Az egyetlen kivetítési csoporton belül megjelenő összes tartalom megőrzi az adatkapcsolaton belüli kapcsolatokat. A kapcsolatok egy generált kulcson alapulnak, és mindegyik alárendelt csomópont megőrzi a szülő csomópontra mutató hivatkozást. A kapcsolatok nem terjednek ki a kivetítési csoportokra, és egy kivetítési csoportban létrehozott táblák vagy objektumok nem rendelkeznek kapcsolattal más vetületi csoportokban létrehozott adatértékekhez.

## <a name="input-shaping"></a>Bevitel kialakítása
Az adatai megfelelő formában vagy struktúrában való beolvasása kulcsfontosságú a hatékony használathoz, legyen az a tábla vagy az objektum. Az adatelemzési és-használati lehetőségek alapján úgy alakíthatja ki és alakíthatja át az adatait, hogy az a készségkészlet belüli **formáló** képességként elérhetővé válik.  

A kivetítések könnyebben definiálhatók, ha olyan objektum szerepel a dúsítási fában, amely megfelel a leképezés sémájának. A frissített [formáló képességgel](cognitive-search-skill-shaper.md) egy objektumot hozhat létre a dúsítási fa különböző csomópontjaiból, és egy új csomópont alatt megadhatja őket. A **formáló** képesség lehetővé teszi, hogy beágyazott objektumokkal rendelkező összetett típusokat határozzon meg.

Ha egy olyan új alakzat van definiálva, amely tartalmazza a kivetítéshez szükséges összes elemet, ezt az alakzatot már használhatja a kivetítések forrásaként, vagy egy másik képességbe bemenetként is.

## <a name="table-projections"></a>Táblázatos előrejelzések

Mivel megkönnyíti az importálást, javasoljuk, hogy a Power BIekkel való adatfeltáráshoz táblázatos előrejelzéseket ajánlson. Emellett a tábla-kivetítések lehetővé teszik a tábla kapcsolata közötti különbség módosítását. 

Az indexben egyetlen dokumentumot is létrehozhat több táblázatba, és megőrizheti a kapcsolatokat. Több táblázatra való kivetítéskor a teljes alakzat az egyes táblákba kerül, kivéve, ha egy alárendelt csomópont egy másik tábla forrása egy adott csoporton belül.

### <a name="defining-a-table-projection"></a>Tábla kivetítésének meghatározása

Ha a készségkészlet `knowledgeStore` elemében lévő táblázat kivetítését határozza meg, először egy csomópontot rendel a dúsítási fában a tábla forrásához. Ez a csomópont általában egy olyan **formáló** képesség kimenete, amelyet a szaktudás listájához adott hozzá, hogy egy adott alakzatot hozzon létre a táblázatokban való projekthez. A projekthez kiválasztott csomópont több táblázatba is feldarabolható a projektbe. A táblák definíciója a projekthez használni kívánt táblák listáját tartalmazza. 

#### <a name="projection-slicing"></a>Leképezések szeletelése
A táblázatos kivetítési csoportok definiálásakor a dúsítási fa egyetlen csomópontja több kapcsolódó táblába is feldarabolható. Ha olyan táblát ad hozzá a forrás elérési útjához, amely egy meglévő táblázatos leképezés gyermeke, akkor a gyermek csomópont ki lesz darabolva a szülő csomópontból, és az új, még kapcsolódó táblába kerül. Ez lehetővé teszi, hogy egyetlen csomópontot határozzon meg egy olyan alakzatban, amely a tábla összes vetületének forrása lehet.

Minden táblázathoz három tulajdonság szükséges:

+ Táblanév: a tábla neve az Azure Storage-ban.

+ generatedKeyName: a kulcs oszlopának neve, amely egyedileg azonosítja ezt a sort.

+ Forrás: a dúsítási fában lévő csomópont, amelyből a dúsítást beadja. Ez általában egy formáló kimenete, de az is lehet, hogy bármelyik ismeret kimenete lehetséges.

Íme egy példa a tábla-kivetítésekre.

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
Ahogy az ebben a példában is látható, a legfontosabb kifejezések és entitások különböző táblákba vannak modellezve, és az egyes sorokhoz tartozó szülőre (MainTable) mutató hivatkozást tartalmaznak. 

A következő ábra a Caselaw gyakorlatra mutat, amely [bemutatja, hogyan kezdheti el a Knowledge Store](knowledge-store-howto.md)-t. Abban az esetben, ha egy esetnek több véleménye is van, és az egyes véleményeket a benne található entitások azonosításával gazdagítják, az itt látható módon modellezheti az előrejelzéseket.

![Entitások és kapcsolatok a táblákban](media/knowledge-store-projection-overview/TableRelationships.png "Modellezési kapcsolatok a táblázatos kivetítésekben")

## <a name="object-projections"></a>Objektum-kivetítések

Az objektum-kivetítések a dúsítási fa olyan JSON-ábrázolásai, amelyek bármely csomópontból származnak. Sok esetben ugyanaz a **shapeer** -képesség, amely létrehoz egy tábla-kivetítést egy objektum-kivetítés létrehozásához. 

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

Az objektumok leképezésének létrehozásához néhány objektum-specifikus attribútumra van szükség:

+ storageContainer: az a tároló, ahová a rendszer menti az objektumokat
+ Forrás: a kivetítés gyökeréhez tartozó dúsítási fa csomópontjának elérési útja
+ kulcs: egy elérési út, amely a tárolni kívánt objektum egyedi kulcsát jelöli. A rendszer felhasználja a blob nevének létrehozására a tárolóban.

## <a name="projection-lifecycle"></a>Leképezési életciklus

Az előrejelzések olyan életciklussal rendelkeznek, amely az adatforrás adatforrásához van kötve. Ahogy az adatai frissülnek és újra indexelnek, a rendszer frissíti a kivetítéseket a kivetítéseket biztosító bővítések eredményeivel, és az adatforrásban lévő összes adattal összhangban van. A kivetítések öröklik az indexhez konfigurált törlési szabályzatot. 

## <a name="using-projections"></a>Kivetítések használata

Az indexelő futtatása után a kivetítésen keresztül megadott tárolókban vagy táblákban is elolvashatja a tervezett adatértékeket. 

Az elemzéshez a Power BI felderítése olyan egyszerű, mint az Azure Table Storage beállítása adatforrásként. Egyszerűen létrehozhat egy vizualizációkat az adatain belül, amelyekben a kapcsolatok kihasználják a kapcsolatait.

Ha a dúsított adatok adatelemzési folyamatokban való használatát szeretné használni, akkor a [blobokból származó adatok egy Panda DataFrame tölthetők](../machine-learning/team-data-science-process/explore-data-blob.md)be.

Végül, ha a Knowledge Store-ból kell exportálnia az adatait, Azure Data Factory rendelkezik összekötővel az adatok exportálásához és az Ön által választott adatbázisban való tárolásához. 

## <a name="next-steps"></a>Következő lépések

A következő lépésként hozza létre az első Knowledge Store-t mintaadatok és utasítások használatával.

> [!div class="nextstepaction"]
> [Tudásbázis létrehozása](knowledge-store-howto.md).
