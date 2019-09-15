---
title: A szakértelmével használata – Azure Search
description: A szakértelmével egy mesterséges intelligencia-bővítési folyamatot hoz létre a kognitív keresésekben, néhány fogalom megismerése és a szakértelmével-munka lehetővé teszi egyszerű vagy összetett szakértelmével létrehozását
manager: eladz
author: vkurpad
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: vikurpad
ms.openlocfilehash: df2c469ef825863f958c8c263253c7d1c6f6c732
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71004409"
---
# <a name="working-with-skillsets"></a>A szakértelmével használata
Ez a cikk azoknak a fejlesztőknek szól, akiknek alaposabban meg kell ismerniük, hogyan működik a dúsítási folyamat, és feltételezi, hogy Ön rendelkezik a kognitív keresési folyamat fogalmi megismerésével. Ha a kognitív keresés újdonsága, kezdje a következővel:
+ [Mi a "kognitív keresés" a Azure Searchban?](cognitive-search-concept-intro.md)
+ [Mi az a Knowledge Store a Azure Searchban?](knowledge-store-concept-intro.md)

## <a name="specify-the-skillset"></a>A Készségkészlet meghatározása
A készségkészlet egy újrafelhasználható erőforrás a Azure Searchban, amely olyan kognitív képességek gyűjteményét határozza meg, amelyek a szöveg-és képtartalom elemzéséhez, átalakításához és bővítéséhez használatosak az indexelés során. A készségkészlet létrehozása lehetővé teszi szöveg-és képgazdagítás csatolását az adatfeldolgozási fázisban, új információk és struktúrák kinyerése és létrehozása a nyers tartalomból.

A készségkészlet három tulajdonsága van:

+   ```skills```, a képességek rendezetlen gyűjteménye, amelyhez a platform meghatározza a végrehajtás sorrendjét az egyes képességekhez szükséges bemenetek alapján
+   ```cognitiveServices```, a kognitív szolgáltatásoknak a meghívott kognitív képességek számlázásához szükséges kulcsa
+   ```knowledgeStore```, azt a Storage-fiókot, ahol a dúsított dokumentumokat kitervezik



A szakértelmével JSON-ban vannak létrehozva. A [kifejezés nyelve](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional)segítségével összetett szakértelmével hozhat létre a hurok és az [elágazás](https://docs.microsoft.com/en-us/azure/search/cognitive-search-skill-conditional) használatával. A kifejezés nyelve a [JSON-mutató](https://tools.ietf.org/html/rfc6901) elérési útjának jelölését használja, és néhány módosítással azonosítja a csomópontokat a dúsítási fában. A ```"/"``` egy szinttel lejjebb halad a fában, ```"*"``` és a-minden operátorként működik a környezetben. Ezeket a fogalmakat a legjobban egy példával ismertetjük. Az egyes fogalmak és képességek szemléltetésére a [Hotel Reviews minta](knowledge-store-connect-powerbi.md) készségkészlet mutatunk. Ha a készségkészlet az Adatimportálási munkafolyamat követése után szeretné megtekinteni, REST API-ügyfelet kell használnia [a készségkészlet lekéréséhez](https://docs.microsoft.com/en-us/rest/api/searchservice/get-skillset).

### <a name="enrichment-tree"></a>Dúsítási fa

Annak megtervezéséhez, hogy egy készségkészlet fokozatosan gazdagítsa a dokumentumot, kezdjük azzal, amit a dokumentum úgy néz ki, hogy bármilyen alkoholtartalom-növelést lehessen. A dokumentum repedésének kimenete függ az adatforrástól és a kiválasztott elemzési módból. Ez annak a dokumentumnak a állapota is, amelyet a [mező-hozzárendelések](search-indexer-field-mappings.md) az adatok keresési indexbe való felvételekor a tartalom forrásaként használhatnak.
![Knowledge Store a folyamat ábráján](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Knowledge Store a folyamat ábráján")

Ha egy dokumentum a dúsítási folyamatban van, akkor a rendszer a tartalom és a hozzájuk kapcsolódó dúsítások fájáként jeleníti meg. Ezt a fát a dokumentum repedésének kimenete hozza létre. A dúsítási fa formátuma lehetővé teszi, hogy a dúsítási folyamat a metaadatokat még a primitív adattípusokhoz csatolja, nem egy érvényes JSON-objektum, de érvényes JSON formátumba is beilleszthető. A következő táblázat a dúsítási folyamatba bekerülő dokumentum állapotát mutatja be:

|AdatSource\Parsing mód|Alapértelmezett|JSON, JSON-sorok & CSV|
|---|---|---|
|Blobtároló|/document/content<br>/document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|– |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|–|

 A képességek végrehajtásával új csomópontokat vesznek fel a dúsítási fában. Ezek az új csomópontok ezután az alárendelt képességekhez bemenetként, a Knowledge Store-ban való kivetítéssel, vagy az index mezőihez való leképezéssel használhatók. A dúsítások nem változtathatók meg, a csomópontok nem szerkeszthetők. Mivel a szakértelmével összetettebbek, így a dúsítási fában marad, de a dúsítási fában nem minden csomópontnak kell azt az indexbe vagy a tudásbázisba tenni. A dúsítások csak egy részhalmazát különítheti el az indexbe vagy a Knowledge Store-ba.

A dúsítások csak egy részhalmazát különítheti el az indexbe vagy a Knowledge Store-ba.
A dokumentum további részében feltételezzük, hogy a [Hotel Reviews példával](https://docs.microsoft.com/en-us/azure/search/knowledge-store-connect-powerbi)dolgozunk, de ugyanezek a fogalmak a dokumentumok más adatforrásokból való bővítésére is érvényesek.

### <a name="context"></a>Környezet
Minden egyes szaktudáshoz környezet szükséges. A környezet meghatározza A következőket:
+   A képzettség végrehajtásának száma a kiválasztott csomópontok alapján. A gyűjtemény típusú környezeti értékek esetében a végén a ```/*``` hozzáadás után a rendszer egyszer meghívja a képességet a gyűjtemény minden példánya számára. 
+   Ahol a dúsítási fában a rendszer felveszi a képzettségi kimeneteket. A rendszer mindig hozzáadja a kimeneteket a fában a környezeti csomópont gyermekeiként. 
+   A bemenetek alakja. Többszintű gyűjtemények esetén a szülő-gyűjtemény kontextusának beállítása hatással lesz a szakértelem bemenetének alakzatára. Ha például van egy alkoholtartalom-növelési fája az országok listájával, a rendszer minden olyan állapotot tartalmaz, amely tartalmazza a ZipCodes tartalmazó listát.

|Környezet|Input (Bemenet)|Bemenet alakja|Szaktudás meghívása|
|---|---|---|---|
|```/document/countries/*``` |```/document/countries/*/states/*/zipcodes/*``` |Az ország összes ZipCodes listája |Országonként egyszer |
|```/document/countries/*/states/*``` |```/document/countries/*/states/*/zipcodes/*``` |Az állapotban lévő ZipCodes listája | Az ország és az állapot kombinációja után|

### <a name="sourcecontext"></a>SourceContext

A `sourceContext` csak a [formáló ismeretekben](cognitive-search-skill-shaper.md) és a [kivetítésekben](knowledge-store-projection-overview.md)használatos. Többszintű, beágyazott objektumok létrehozásához használatos. A `sourceContext` lehetővé teszi egy hierarchikus, névtelen típusú objektum létrehozását, amelyhez több ismeretre lenne szükség, ha csak a kontextust használta. A `sourceContext` használata a következő szakaszban látható.

### <a name="projections"></a>Leképezések

A kivetítési folyamat kijelöli a csomópontokat a dúsítási fában, hogy a rendszer mentse a Tudásbázisban. A kivetítések a dokumentum (tartalom és dúsítások) egyéni alakzatai, amelyek tábla-vagy objektum-kivetítésként is kiállíthatók. További információ a kivetítések használatáról: [a kivetítések használata](knowledge-store-projection-overview.md).

![Mező-hozzárendelési beállítások](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Mező-hozzárendelési beállítások a dúsítási folyamathoz")

A fenti diagram azt a választót írja le, amely a dúsítási folyamaton alapul.

## <a name="generate-enriched-data"></a>Dúsított adathalmazok előállítása 

Most ugorjon át a Hotel Reviews készségkészlet, és kövesse az [oktatóanyagot](knowledge-store-connect-powerbi.md) a készségkészlet létrehozásához, vagy [tekintse](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/samples/skillset.json) meg a készségkészlet. A következőt fogjuk megtekinteni:

* a dúsítási fa fejlődése az egyes képességek végrehajtásával 
* a környezet és a bemenetek működése annak megállapítása érdekében, hogy a szaktudás hányszor fut 
* a bemenet alakja a kontextuson alapul. 

Mivel a tagolt szöveges elemzési módot használjuk az indexelő számára, a dúsítási folyamaton belüli dokumentumok a CSV-fájl egyetlen sorát jelölik.

### <a name="skill-1-split-skill"></a>Szakértelem #1: Megosztási képesség 

a ![dúsítás fája a dokumentum repedése után](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "A dúsítási fa a dokumentum repedése és a szaktudás végrehajtása előtt")

A gyakorlati környezetében ```"/document/reviews_text"```ez a képesség egyszer `reviews_text`lesz végrehajtva. A szakértelem kimenete egy lista, amelyben `reviews_text` a rendszer 5000 karakterből álló szegmensbe kerül. A felosztási képesség kimenete neve `pages` és hozzáadása a dúsítási fában történik. A `targetName` funkció lehetővé teszi a képzettségi kimenet átnevezését a dúsítási fában való felvétel előtt.

A dúsítási fában már van egy új csomópont, amely a szakértelem kontextusában van elhelyezve. Ez a csomópont bármilyen képzettségi, leképezési vagy kimeneti mezők leképezéséhez elérhető.


Az összes dúsítás `"/document"`legfelső szintű csomópontja. A blob-indexelő használatakor `"/document"` a csomópont a `"/document/content"` és `"/document/normalized_images"`a alárendelt csomópontjaival fog rendelkezni. A CSV-adatkapcsolatok használatakor, ahogy ebben a példában vagyunk, az oszlopnevek az alatta `"/document"`lévő csomópontokra lesznek leképezve. A csomópont által egy adott képességhez hozzáadott alkoholtartalom-növeléshez a dúsítás teljes elérési útjára van szükség. Ha például a ```pages``` csomópont szövegét bemenetként kívánja használni egy másik képességhez, meg kell adnia ```"/document/reviews_text/pages/*"```a következőt:.
 
 a bővítési ![fa a szaktudás után #1] A (media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "szaktudás #1 végrehajtása után a dúsítási fa")

### <a name="skill-2-language-detection"></a>A szaktudás #2 a nyelvfelismerés
 Míg a nyelvi észlelési képesség a készségkészlet definiált harmadik (skill #3) képesség, a következő végrehajtandó képesség. Mivel nem blokkolja semmilyen bemenetet, az előzővel párhuzamosan fog megjelenni. Az előtte lévő felosztási képességhez hasonlóan a nyelvi észlelési képességet is egyszer kell meghívja az egyes dokumentumokhoz. A dúsítási fában már van egy új csomópont a nyelvhez.
 a bővítési ![fa a szaktudás után #2] A (media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "szaktudás #2 végrehajtása után a dúsítási fa")
 
 ### <a name="skill-3-key-phrases-skill"></a>Szakértelem #3: A legfontosabb kifejezések ismerete 

A legfontosabb kifejezések ismeretének kontextusa ```/document/reviews_text/pages/*``` csak egyszer lesz meghívva a `pages` gyűjtemény minden elemére. A skill kimenete a társított oldal elem alatti csomópont lesz. 

 Most már képesnek kell lennie arra, hogy megtekintse a készségkészlet képességeit, és láthatóvá tegye, hogy a dúsítások fája hogyan fog növekedni az egyes képességek végrehajtásával. Bizonyos készségek, például az egyesítési képesség és a formáló képesség, új csomópontokat is létrehozhatnak, de csak a meglévő csomópontok adatait használják, és nem hozhatók létre az új net-bővítések.

![a dúsítás fája az összes ismeret után](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "A dúsítás fája az összes ismeret után")

A fenti fában látható összekötők színei azt jelzik, hogy a dúsítások különböző képességekkel lettek létrehozva, és a csomópontokat külön kell kezelni, és nem lesznek a fölérendelt csomópont kiválasztásakor visszaadott objektum részei.

## <a name="save-enrichments-in-a-knowledge-store"></a>Bővítések mentése a Knowledge Store-ban 

A szakértelmével olyan tudásbázist is definiálhat, amelyben a dúsított dokumentumok táblázatként vagy objektumként is kiállíthatók. A dúsított információk a Tudásbázisban való mentéséhez definiálni kell a dúsított dokumentum kivetítéseit. További információ a Knowledge Áruházról: [Mi az a Knowledge Store a Azure Searchban?](knowledge-store-concept-intro.md)

### <a name="slicing-projections"></a>Kivetítések szeletelése

A táblázatos kivetítési csoportok definiálásakor a dúsítási fa egyetlen csomópontja több kapcsolódó táblába is feldarabolható. Ha olyan táblát ad hozzá a forrás elérési útjához, amely egy meglévő tábla kivetítésének gyermeke, akkor az eredményül kapott alárendelt csomópont nem lesz a meglévő táblázatos leképezés gyermeke, hanem az új, kapcsolódó, táblázatba kerül. Ez a szeletelési módszer lehetővé teszi, hogy egyetlen csomópontot határozzon meg egy olyan alakzatban, amely a tábla összes vetületének forrása lehet. 

### <a name="shaping-projections"></a>Kivetítések kialakítása

A kivetítést kétféleképpen lehet definiálni. Egy formázó képességgel létrehozhat egy új csomópontot, amely a kivetítéshez használt összes dúsítás legfelső szintű csomópontja. Ezután az előrejelzések szerint csak az alakzatra vonatkozó szakértelem kimenetére hivatkozhat. A vetítési definícióban is beágyazhat egy kivetítési formát.

A formáló megközelítés részletesebb, mint a beágyazott alakítás, azonban biztosítja, hogy a dúsítási fa összes mutációja a szaktudásban legyen, és hogy a kimenet egy olyan objektum, amely újra felhasználható. A beágyazott kialakítás lehetővé teszi, hogy létrehozza a szükséges alakzatot, de egy névtelen objektum, és csak azokhoz a vetítésekhez érhető el, amelyekhez definiálva van. A módszerek együtt vagy külön is használhatók. A portál munkafolyamatban létrehozott készségkészlet is tartalmaz. A táblázatos kivetítésekhez egy formálói képességet használ, de a legfontosabb kifejezések táblázatának bevetítéséhez is a beágyazott alakítást használja.

A példa kiterjesztéséhez dönthet úgy, hogy eltávolítja a beágyazott formázást, és egy formáló képességgel létrehoz egy új csomópontot a legfontosabb kifejezésekhez. A következő szakaszokban a két lehetőség közül választhat, hogy három `hotelReviewsDocument`táblázatba `hotelReviewsPages`( `hotelReviewsKeyPhrases`például,, és) álló alakzatot hozzon létre.


#### <a name="shaper-skill-and-projection"></a>Formáló képesség és kivetítés 

> [!Note]
> A dokumentum táblázat néhány oszlopa el lett távolítva ebből a példából a rövidség kedvéért.
>
```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
                        }
                    ]
                }
            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "tableprojection"
        }
    ]
}
```

A fenti `outputs` szakaszban meghatározott `tableprojection` csomóponttal mostantól a szeletelési funkciót is használhatja a csomópont különböző táblákba való kivetítéséhez: `tableprojection`

> [!Note]
> Ez csak a Knowledge Store-konfigurációban található kivetítés részlete.
>
```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

#### <a name="inline-shaping-projections"></a>Kivetítések beágyazott kialakítása

A beágyazott alakítási megközelítés nem igényel formálói képességet, mivel a kivetítésekhez szükséges összes alakzat létrehozásához szükség van. Az előző példához hasonló adatprojektek kivetítéséhez a beágyazott leképezési lehetőség a következőképpen fog kinézni:

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
                    {
                        "name": "reviews_text",
                        "source": "/document/reviews_text"
                    },
                    {
                        "name": "reviews_title",
                        "source": "/document/reviews_title"
                    },
                    {
                        "name": "AzureSearch_DocumentKey",
                        "source": "/document/AzureSearch_DocumentKey"
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
                "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                "inputs": [
                    {
                        "name": "Keyphrases",
                        "source": "/document/reviews_text/pages/*/Keyphrases/*"
                    }
                ]
            }
        ]
    }
]
```
  
A megközelítések közül az egyik megfigyelés, hogy az `"Keyphrases"` értékek hogyan lesznek kiértékelve a `"sourceContext"`használatával. A `"Keyphrases"` karakterláncok gyűjteményét tartalmazó csomópont maga az oldal szövegének gyermeke. Mivel azonban a vetítésekhez JSON-objektumra van szükség, és az oldal egy primitív (karakterlánc) `"sourceContext"` , a a kulcs kifejezést egy nevesített tulajdonsággal rendelkező objektumba csomagolja. Ez a módszer lehetővé teszi, hogy a primitívek egymástól függetlenül legyenek kitervezve.

## <a name="next-steps"></a>További lépések

A következő lépésként hozza létre az első készségkészlet kognitív ismeretekkel.

> [!div class="nextstepaction"]
> [Hozza létre az első készségkészlet](cognitive-search-defining-skillset.md).
