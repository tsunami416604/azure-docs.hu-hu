---
title: Készségkészlet-fogalmak és-munkafolyamatok
titleSuffix: Azure Cognitive Search
description: A szakértelmével egy mesterséges intelligenciát létrehozó folyamat készítése az Azure Cognitive Searchban. Ismerje meg a készségkészlet-összeállítással kapcsolatos fontos fogalmakat és részleteket.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: acf7305a46e9fc3d19f96f88cf2e9ab5eacddd7c
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113644"
---
# <a name="skillset-concepts-and-composition-in-azure-cognitive-search"></a>Készségkészlet-fogalmak és-összeállítás az Azure Cognitive Search

Ez a cikk azoknak a fejlesztőknek szól, akiknek alaposabban meg kell ismerniük, hogyan működik a dúsítási folyamat, és feltételezi, hogy az AI-bővítési folyamat fogalmi ismerete van. Ha új ez a fogalom, kezdje a következővel:
+ [AI-gazdagítás az Azure Cognitive Search](cognitive-search-concept-intro.md)
+ [Knowledge Store (előzetes verzió)](knowledge-store-concept-intro.md)

## <a name="specify-the-skillset"></a>A Készségkészlet meghatározása
A készségkészlet az Azure Cognitive Search újrafelhasználható erőforrása, amely olyan kognitív képességek gyűjteményét határozza meg, amelyek a szöveg-és képtartalom elemzéséhez, átalakításához és bővítéséhez használatosak az indexelés során. A készségkészlet létrehozása lehetővé teszi szöveg-és képgazdagítás csatolását az adatfeldolgozási fázisban, új információk és struktúrák kinyerése és létrehozása a nyers tartalomból.

A készségkészlet három tulajdonsága van:

+   ```skills```, a képességek rendezetlen gyűjteménye, amelyhez a platform meghatározza a végrehajtás sorrendjét az egyes képességekhez szükséges bemenetek alapján
+   ```cognitiveServices```, a meghívott kognitív képességek számlázásához szükséges kognitív szolgáltatások kulcsa
+   ```knowledgeStore```azt a Storage-fiókot, ahol a dúsított dokumentumokat tervezik



A szakértelmével JSON-ban vannak létrehozva. A [kifejezés nyelve](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional)segítségével összetett szakértelmével hozhat létre a hurok és az [elágazás](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional) használatával. A kifejezés nyelve a [JSON-mutató](https://tools.ietf.org/html/rfc6901) elérési útjának jelölését használja, és néhány módosítással azonosítja a csomópontokat a dúsítási fában. A ```"/"``` a fában alacsonyabb szintre halad, és a ```"*"``` a környezet minden operátora számára működik. Ezeket a fogalmakat a legjobban egy példával ismertetjük. Az egyes fogalmak és képességek szemléltetésére a [Hotel Reviews minta](knowledge-store-connect-powerbi.md) készségkészlet mutatunk. Ha a készségkészlet az Adatimportálási munkafolyamat követése után szeretné megtekinteni, REST API-ügyfelet kell használnia [a készségkészlet lekéréséhez](https://docs.microsoft.com/rest/api/searchservice/get-skillset).

### <a name="enrichment-tree"></a>Dúsítási fa

Annak megtervezéséhez, hogy egy készségkészlet fokozatosan gazdagítsa a dokumentumot, kezdjük azzal, amit a dokumentum úgy néz ki, hogy bármilyen alkoholtartalom-növelést lehessen. A dokumentum repedésének kimenete függ az adatforrástól és a kiválasztott elemzési módból. Ez annak a dokumentumnak a állapota is, amelyet a [mező-hozzárendelések](search-indexer-field-mappings.md) az adatok keresési indexbe való felvételekor a tartalom forrásaként használhatnak.
![Knowledge Store a folyamat ábráján](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Knowledge-tároló a folyamat ábráján ")

Ha egy dokumentum a dúsítási folyamatban van, akkor a rendszer a tartalom és a hozzájuk kapcsolódó dúsítások fájáként jeleníti meg. Ezt a fát a dokumentum repedésének kimenete hozza létre. A dúsítási fa formátuma lehetővé teszi, hogy a dúsítási folyamat a metaadatokat még a primitív adattípusokhoz csatolja, nem egy érvényes JSON-objektum, de érvényes JSON formátumba is beilleszthető. A következő táblázat a dúsítási folyamatba bekerülő dokumentum állapotát mutatja be:

|AdatSource\Parsing mód|Alapértelmezett|JSON, JSON-sorok & CSV|
|---|---|---|
|Blob Storage|/document/content<br>/Document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|N/A |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|N/A|

 A képességek végrehajtásával új csomópontokat vesznek fel a dúsítási fában. Ezek az új csomópontok ezután az alárendelt képességekhez bemenetként, a Knowledge Store-ban való kivetítéssel, vagy az index mezőihez való leképezéssel használhatók. A dúsítások nem változtathatók meg, a csomópontok nem szerkeszthetők. Mivel a szakértelmével összetettebbek, így a dúsítási fában marad, de a dúsítási fában nem minden csomópontnak kell azt az indexbe vagy a tudásbázisba tenni. A dúsítások csak egy részhalmazát különítheti el az indexbe vagy a Knowledge Store-ba.

A dúsítások csak egy részhalmazát különítheti el az indexbe vagy a Knowledge Store-ba.
A dokumentum további részében feltételezzük, hogy a [Hotel Reviews példával](https://docs.microsoft.com/azure/search/knowledge-store-connect-powerbi)dolgozunk, de ugyanezek a fogalmak a dokumentumok más adatforrásokból való bővítésére is érvényesek.

### <a name="context"></a>Környezet
Minden egyes szaktudáshoz környezet szükséges. A környezet meghatározza A következőket:
+   A képzettség végrehajtásának száma a kiválasztott csomópontok alapján. A gyűjtemény típusú környezeti értékek esetében a befejezéshez ```/*``` hozzáadásával a program a gyűjtemény minden példánya esetében egyszer meghívja a képességet. 
+   Ahol a dúsítási fában a rendszer felveszi a képzettségi kimeneteket. A rendszer mindig hozzáadja a kimeneteket a fában a környezeti csomópont gyermekeiként. 
+   A bemenetek alakja. Többszintű gyűjtemények esetén a szülő-gyűjtemény kontextusának beállítása hatással lesz a szakértelem bemenetének alakzatára. Ha például van egy alkoholtartalom-növelési fája az országok listájával, a rendszer minden olyan állapotot tartalmaz, amely tartalmazza a ZipCodes tartalmazó listát.

|Környezet|Input (Bemenet)|Bemenet alakja|Szaktudás meghívása|
|---|---|---|---|
|```/document/countries/*``` |```/document/countries/*/states/*/zipcodes/*``` |Az ország összes ZipCodes listája |Országonként egyszer |
|```/document/countries/*/states/*``` |```/document/countries/*/states/*/zipcodes/*``` |Az állapotban lévő ZipCodes listája | Az ország és az állapot kombinációja után|

### <a name="sourcecontext"></a>SourceContext

A `sourceContext` csak a [formáló ismeretekben](cognitive-search-skill-shaper.md) és a [kivetítésekben](knowledge-store-projection-overview.md)használatos. Többszintű, beágyazott objektumok létrehozásához használatos. A `sourceContext` lehetővé teszi egy hierarchikus, névtelen típusú objektum összeállítását, amely több ismeretet igényelne, ha csak a környezetet használta. A `sourceContext` használata a következő szakaszban látható.

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

### <a name="skill-1-split-skill"></a>Skill #1: megosztási képesség 

![a dúsítás fája a dokumentum repedése után](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "A dúsítási fa a dokumentum repedése és a szaktudás végrehajtása előtt")

A ```"/document/reviews_text"```szaktudási kontextusával ezt a képességet egyszer kell végrehajtani a `reviews_text`. A szakértelem kimenete egy lista, amelyen a `reviews_text` 5000 karakterből álló szegmensbe van felosztva. A felosztott képességből származó kimenet neve `pages`, és a rendszer hozzáadja a dúsítási fában. A `targetName` funkció lehetővé teszi a képzettségi kimenet átnevezését a dúsítási fában való felvétel előtt.

A dúsítási fában már van egy új csomópont, amely a szakértelem kontextusában van elhelyezve. Ez a csomópont bármilyen képzettségi, leképezési vagy kimeneti mezők leképezéséhez elérhető.


Az összes dúsítás legfelső szintű csomópontja `"/document"`. A blob-indexelő használatakor a `"/document"` csomópont `"/document/content"` és `"/document/normalized_images"`alárendelt csomópontokkal fog rendelkezni. A CSV-adatkapcsolatok használatakor, ahogy az ebben a példában is látható, az oszlopnevek a `"/document"`alatti csomópontokra lesznek leképezve. A csomópont által egy adott képességhez hozzáadott alkoholtartalom-növeléshez a dúsítás teljes elérési útjára van szükség. Ha például azt szeretné, hogy a ```pages``` csomópont szövege bemenetként egy másik szakértelmet adjon meg, akkor ```"/document/reviews_text/pages/*"```ként kell megadnia.
 
 ![a bővítési fa a szaktudás után #1](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "A szaktudás #1 végrehajtása után a dúsítási fa")

### <a name="skill-2-language-detection"></a>A szaktudás #2 a nyelvfelismerés
 Míg a nyelvi észlelési képesség a készségkészlet definiált harmadik (skill #3) képesség, a következő végrehajtandó képesség. Mivel nem blokkolja semmilyen bemenetet, az előzővel párhuzamosan fog megjelenni. Az előtte lévő felosztási képességhez hasonlóan a nyelvi észlelési képességet is egyszer kell meghívja az egyes dokumentumokhoz. A dúsítási fában már van egy új csomópont a nyelvhez.
 ![a bővítési fa a szaktudás után #2](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Ena szaktudás #2 végrehajtása utáni fastruktúra ")
 
 ### <a name="skill-3-key-phrases-skill"></a>Skill #3: a kulcsfontosságú kifejezések ismerete 

```/document/reviews_text/pages/*``` a legfontosabb kifejezések ismeretének kontextusát egyszer kell meghívni a `pages` gyűjtemény minden eleméhez. A skill kimenete a társított oldal elem alatti csomópont lesz. 

 Most már képesnek kell lennie arra, hogy megtekintse a készségkészlet képességeit, és láthatóvá tegye, hogy a dúsítások fája hogyan fog növekedni az egyes képességek végrehajtásával. Bizonyos készségek, például az egyesítési képesség és a formáló képesség, új csomópontokat is létrehozhatnak, de csak a meglévő csomópontok adatait használják, és nem hozhatók létre az új net-bővítések.

![a dúsítás fája az összes ismeret után](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "A dúsítás fája az összes ismeret után")

A fenti fában látható összekötők színei azt jelzik, hogy a dúsítások különböző képességekkel lettek létrehozva, és a csomópontokat külön kell kezelni, és nem lesznek a fölérendelt csomópont kiválasztásakor visszaadott objektum részei.

## <a name="save-enrichments-in-a-knowledge-store"></a>Bővítések mentése a Knowledge Store-ban 

A szakértelmével olyan tudásbázist is definiálhat, amelyben a dúsított dokumentumok táblázatként vagy objektumként is kiállíthatók. A dúsított információk a Tudásbázisban való mentéséhez definiálni kell a dúsított dokumentum kivetítéseit. További információ a Knowledge Store-ról: a [Knowledge Store áttekintése](knowledge-store-concept-intro.md)

### <a name="slicing-projections"></a>Kivetítések szeletelése

A táblázatos kivetítési csoportok definiálásakor a dúsítási fa egyetlen csomópontja több kapcsolódó táblába is feldarabolható. Ha olyan táblát ad hozzá a forrás elérési útjához, amely egy meglévő tábla kivetítésének gyermeke, akkor az eredményül kapott alárendelt csomópont nem lesz a meglévő táblázatos leképezés gyermeke, hanem az új, kapcsolódó, táblázatba kerül. Ez a szeletelési módszer lehetővé teszi, hogy egyetlen csomópontot határozzon meg egy olyan alakzatban, amely a tábla összes vetületének forrása lehet. 

### <a name="shaping-projections"></a>Kivetítések kialakítása

A kivetítést kétféleképpen lehet definiálni. Egy formázó képességgel létrehozhat egy új csomópontot, amely a kivetítéshez használt összes dúsítás legfelső szintű csomópontja. Ezután az előrejelzések szerint csak az alakzatra vonatkozó szakértelem kimenetére hivatkozhat. A vetítési definícióban is beágyazhat egy kivetítési formát.

A formáló megközelítés részletesebb, mint a beágyazott alakítás, azonban biztosítja, hogy a dúsítási fa összes mutációja a szaktudásban legyen, és hogy a kimenet egy olyan objektum, amely újra felhasználható. A beágyazott kialakítás lehetővé teszi, hogy létrehozza a szükséges alakzatot, de egy névtelen objektum, és csak azokhoz a vetítésekhez érhető el, amelyekhez definiálva van. A módszerek együtt vagy külön is használhatók. A portál munkafolyamatban létrehozott készségkészlet is tartalmaz. A táblázatos kivetítésekhez egy formálói képességet használ, de a legfontosabb kifejezések táblázatának bevetítéséhez is a beágyazott alakítást használja.

A példa kiterjesztéséhez dönthet úgy, hogy eltávolítja a beágyazott formázást, és egy formáló képességgel létrehoz egy új csomópontot a legfontosabb kifejezésekhez. A következő szakaszokban a két lehetőség közül választhat, hogy egy három táblázatból álló alakzatot hozzon létre: `hotelReviewsDocument`, `hotelReviewsPages`és `hotelReviewsKeyPhrases`.


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

A fenti `outputs` szakaszban definiált `tableprojection`-csomóponttal mostantól a szeletelés funkciót is használhatja a `tableprojection` csomópont különböző táblákba való kivetítéséhez:

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
  
A módszerek közül az egyik megfigyelés, hogy az `"Keyphrases"` értékeit hogyan tervezték a `"sourceContext"`. A karakterláncok gyűjteményét tartalmazó `"Keyphrases"` csomópont maga az oldal szövegének gyermeke. Mivel azonban a vetítésekhez JSON-objektumra van szükség, és az oldal egy primitív (string), a `"sourceContext"` a kulcs kifejezésének egy nevesített tulajdonsággal rendelkező objektumba való becsomagolására szolgál. Ez a módszer lehetővé teszi, hogy a primitívek egymástól függetlenül legyenek kitervezve.

## <a name="next-steps"></a>További lépések

A következő lépésként hozza létre az első készségkészlet kognitív ismeretekkel.

> [!div class="nextstepaction"]
> [Hozza létre az első készségkészlet](cognitive-search-defining-skillset.md).
