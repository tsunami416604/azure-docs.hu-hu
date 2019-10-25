---
title: Kivetítések használata a Knowledge Store-ban (előzetes verzió)
titleSuffix: Azure Cognitive Search
description: A teljes szöveges kereséstől eltérő helyzetekben mentse és alakítsa ki a dúsított adatait a mesterséges intelligencia-dúsítási folyamatból.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8e6c285e8917c4224e2007c565d5ac5447b20853
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790001"
---
# <a name="working-with-projections-in-a-knowledge-store-in-azure-cognitive-search"></a>Kivetítések használata az Azure-beli Knowledge Store-ban Cognitive Search

> [!Note]
> A Knowledge áruház előzetes verzióban érhető el, és nem éles használatra készült. A [REST API 2019-05-06-es verziójának előzetes verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nincs .NET SDK-támogatás.
>

Az Azure Cognitive Search lehetővé teszi a tartalom-bővítést a beépített kognitív képességek és egyéni képességek révén az indexelés részeként. A bővítések struktúrát adhatnak a dokumentumokhoz, és hatékonyabbá tehetik a keresést. Sok esetben a dúsított dokumentumok a kereséstől eltérő forgatókönyvek esetén hasznosak, például a Knowledge Mining esetében.

A kivetítések, a [Knowledge Store](knowledge-store-concept-intro.md)egy összetevője, a kibővített dokumentumok nézetei, amelyek a fizikai tárterületre menthetők a tudás-bányászati célokra. A kivetítés lehetővé teszi, hogy az adatai egy olyan alakzatba kerüljenek, amely igazodik az igényeihez, és megőrzi a kapcsolatokat, hogy az eszközök, például a Power BI további erőfeszítés nélkül is beolvassák az adatait.

A vetítés táblázatos lehet, az Azure Table Storage-ban sorokban és oszlopokban tárolt adatokat, illetve az Azure Blob Storage-ban tárolt JSON-objektumokat. Több kivetítést is megadhat az adataihoz, mivel azok gazdagítva vannak. Több kivetítés is hasznos lehet, ha ugyanazokat az adatszerkezeteket szeretné eltérő módon használni az egyes használati esetekben.

A Tudásbázis háromféle típusú kivetítést támogat:

+ **Táblák**: a táblázatos kivetítések lehetővé teszik a sematikus-alakzat vagy-leképezés megadását a táblázatos tárolóban.

+ **Objektumok**: Ha az adatai és a bővítések JSON-ábrázolására van szüksége, az objektum-vetítések blobként lesznek mentve.

+ **Fájlok**: Ha mentenie kell a dokumentumokból kinyert képeket, a fájl-kivetítések lehetővé teszik a normalizált rendszerképek mentését.

Ha meg szeretné tekinteni a kontextusban definiált kivetítéseket, tekintse át az [Ismerkedés a Knowledge Store szolgáltatással](knowledge-store-howto.md)című témakört.

## <a name="projection-groups"></a>Kivetítési csoportok

Bizonyos esetekben különböző alakzatokban kell kibővíteni a dúsított adatait, hogy azok megfeleljenek a különböző célkitűzéseknek. A Tudásbázis segítségével több kivetítési csoportot is meghatározhat. A kivetítési csoportok az alábbi fő jellemzőkkel rendelkeznek a kölcsönös kizárólagosság és a kapcsolódó jelleg tekintetében.

### <a name="mutual-exclusivity"></a>Kölcsönös kizárólagosság

Az egyetlen csoportba tartozó összes tartalom független a többi kivetítési csoportba tervezett adatokat.
Ez a függetlenség azt feltételezi, hogy ugyanazokat az adatszerkezeteket kell megismételni, amelyeket az egyes leképezési csoportokban még meg is ismétlik.

### <a name="relatedness"></a>Rokonság

A kivetítési csoportok mostantól lehetővé teszik a dokumentumok kivetítését a leképezési típusok között, miközben megőrzi a különböző leképezési típusok közötti kapcsolatokat. Az egyetlen kivetítési csoporton belül megjelenő összes tartalom megőrzi az adatokat a leképezési típusok közötti kapcsolaton belül. A táblákon belül a kapcsolatok egy generált kulcson alapulnak, és minden alárendelt csomópont megőrzi a szülő csomópontra mutató hivatkozást. A különböző típusok (táblák, objektumok és fájlok) között a kapcsolatok megmaradnak, ha egyetlen csomópontot terveznek a különböző típusok között. Vegyünk például egy olyan forgatókönyvet, amelyben van egy kép és szöveg tartalmú dokumentum. A szöveget táblázatokra vagy objektumokra, valamint azokra a fájlokra is felhasználhatja, amelyekben a táblák vagy objektumok a fájl URL-címét tartalmazó tulajdonsággal rendelkeznek.

## <a name="input-shaping"></a>Bevitel kialakítása

Az adatai megfelelő formában vagy struktúrában való beolvasása kulcsfontosságú a hatékony használathoz, legyen az a tábla vagy az objektum. Az adatelemzési és-használati lehetőségek alapján úgy alakíthatja ki és alakíthatja át az adatait, hogy az a készségkészlet belüli **formáló** képességként elérhetővé válik.  

A kivetítések könnyebben definiálhatók, ha olyan objektum szerepel a dúsítási fában, amely megfelel a leképezés sémájának. A frissített [formáló képességgel](cognitive-search-skill-shaper.md) egy objektumot hozhat létre a dúsítási fa különböző csomópontjaiból, és egy új csomópont alatt megadhatja őket. A **formáló** képesség lehetővé teszi, hogy beágyazott objektumokkal rendelkező összetett típusokat határozzon meg.

Ha egy olyan új alakzat van definiálva, amely tartalmazza a kivetítéshez szükséges összes elemet, ezt az alakzatot már használhatja a kivetítések forrásaként, vagy egy másik képességbe bemenetként is.

## <a name="projection-slicing"></a>Leképezések szeletelése

A kivetítési csoport definiálásakor a dúsítási fa egyetlen csomópontja több kapcsolódó táblába vagy objektumba is feldarabolható. Ha olyan forrás elérési úttal rendelkező leképezést ad hozzá, amely egy meglévő leképezés gyermeke, akkor a gyermek csomópont ki lesz darabolva a szülő csomópontból, és az új, még kapcsolódó táblába vagy objektumba kerül. Ez a módszer lehetővé teszi, hogy egyetlen csomópontot határozzon meg egy olyan alakzatban, amely az összes kivetítés forrása lehet.

## <a name="table-projections"></a>Táblázatos előrejelzések

Mivel megkönnyíti az importálást, javasoljuk, hogy a Power BIekkel való adatfeltáráshoz táblázatos előrejelzéseket ajánlson. Emellett a tábla-kivetítések lehetővé teszik a tábla kapcsolatai közötti különbségek módosítását. 

Az indexben egyetlen dokumentumot is létrehozhat több táblázatba, és megőrizheti a kapcsolatokat. Több táblázatra való kivetítéskor a teljes alakzat az egyes táblákba kerül, kivéve, ha egy alárendelt csomópont egy másik tábla forrása egy adott csoporton belül.

### <a name="defining-a-table-projection"></a>Tábla kivetítésének meghatározása

Ha a készségkészlet `knowledgeStore` elemében lévő táblázat kivetítését határozza meg, először egy csomópontot rendel a dúsítási fában a tábla forrásához. Ez a csomópont általában egy olyan **formáló** képesség kimenete, amelyet a szaktudás listájához adott hozzá, hogy egy adott alakzatot hozzon létre a táblázatokban való projekthez. A projekthez kiválasztott csomópont több táblázatba is feldarabolható a projektbe. A táblák definíciója a projekthez használni kívánt táblák listáját tartalmazza.

Minden táblázathoz három tulajdonság szükséges:

+ Táblanév: a tábla neve az Azure Storage-ban.

+ generatedKeyName: a kulcs oszlopának neve, amely egyedileg azonosítja ezt a sort.

+ Forrás: a dúsítási fában lévő csomópont, amelyből a dúsítást beadja. Ez a csomópont általában egy formáló kimenete, de a képességek bármelyikének kimenete lehet.

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
          "objects": [ ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

Ahogy az ebben a példában is látható, a legfontosabb kifejezések és entitások különböző táblákba vannak modellezve, és az egyes sorokhoz tartozó szülőre (MainTable) mutató hivatkozást tartalmaznak.

Az alábbi ábra a [Knowledge Store használatbavételének első lépéseit](knowledge-store-howto.md)ismertető esettanulmányt mutatja be. Abban az esetben, ha egy esetnek több véleménye is van, és az egyes véleményeket a benne található entitások azonosításával gazdagítják, az itt látható módon modellezheti az előrejelzéseket.

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
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

Az objektumok leképezésének létrehozásához néhány objektum-specifikus attribútumra van szükség:

+ storageContainer: az a tároló, ahová a rendszer menti az objektumokat
+ Forrás: a kivetítés gyökeréhez tartozó dúsítási fa csomópontjának elérési útja
+ kulcs: egy elérési út, amely a tárolni kívánt objektum egyedi kulcsát jelöli. A rendszer felhasználja a blob nevének létrehozására a tárolóban.

## <a name="file-projection"></a>Fájl kivetítése

A fájl-kivetítések hasonlóak az objektum-kivetítésekhez, és csak a `normalized_images` gyűjteményben működnek. Az objektumok kivetítéséhez hasonlóan a fájl-kivetítések a blob tárolóban lesznek mentve a dokumentum-azonosító Base64 kódolású értékének mappa előtaggal. A fájl-kivetítések nem oszthatják meg ugyanazt a tárolót, mint az objektum-kivetítéseket, és egy másik tárolóba kell őket betervezni.

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
          "objects": [ ]
        },
        {
            "files": [
                 {
                  "storageContainer": "ReviewImages",
                  "source": "/document/normalized_images/*"
                }
            ]
        }
      ]
    }
}
```

## <a name="projection-lifecycle"></a>Leképezési életciklus

Az előrejelzések olyan életciklussal rendelkeznek, amely az adatforrás adatforrásához van kötve. Ahogy az adatai frissülnek és újraindexelve vannak, a rendszer frissíti a kivetítéseket a kivetítéseket biztosító bővítések eredményeivel, és az adatforrásban lévő összes adattal összhangban van. A kivetítések öröklik az indexhez konfigurált törlési szabályzatot. A kivetítések nem törlődnek, ha az indexelő vagy a keresési szolgáltatás törlődik.

## <a name="using-projections"></a>Kivetítések használata

Az indexelő futtatása után a kivetítésen keresztül megadott tárolókban vagy táblákban is elolvashatja a tervezett adatértékeket.

Az elemzéshez a Power BI felderítése olyan egyszerű, mint az Azure Table Storage beállítása adatforrásként. A-ban található kapcsolatokkal könnyedén hozhat létre vizualizációkat az adataihoz.

Ha a dúsított adatok adatelemzési folyamatokban való használatát szeretné használni, akkor a [blobokból származó adatok egy Panda DataFrame tölthetők](../machine-learning/team-data-science-process/explore-data-blob.md)be.

Végül, ha a Knowledge Store-ból kell exportálnia az adatait, Azure Data Factory rendelkezik összekötővel az adatok exportálásához és az Ön által választott adatbázisban való tárolásához. 

## <a name="next-steps"></a>Következő lépések

A következő lépésként hozza létre az első Knowledge Store-t mintaadatok és utasítások használatával.

> [!div class="nextstepaction"]
> [Tudásbázis létrehozása](knowledge-store-howto.md).
