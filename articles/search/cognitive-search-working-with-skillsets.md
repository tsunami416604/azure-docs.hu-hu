---
title: Skillset fogalmak és munkafolyamat
titleSuffix: Azure Cognitive Search
description: Skillsets van hol vagy szerző egy AI dúsítás folyamat -ban Azure Cognitive Kutatás. Ismerje meg a fontos fogalmakat és részleteket skillset összetételét.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8b45840215092281c7fbc8d499e26b095b374dd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191026"
---
# <a name="skillset-concepts-and-composition-in-azure-cognitive-search"></a>Skillset-fogalmak és kompozíciók az Azure Cognitive Search-ben

Ez a cikk azoknak a fejlesztőknek szól, akiknek mélyebb ismeretekre van szükségük a dúsítási folyamat működéséről, és feltételezi, hogy rendelkezik az AI-bővítési folyamat fogalmi ismeretekkel. Ha ön új ez fogalom, elkezd -val:
+ [AI gazdagítása az Azure Cognitive Search-ben](cognitive-search-concept-intro.md)
+ [Tudástároló (előzetes verzió)](knowledge-store-concept-intro.md)

## <a name="specify-the-skillset"></a>Adja meg a Skillset
A skillset egy újrafelhasználható erőforrás az Azure Cognitive Search, amely meghatározza a kognitív képességek elemzéséhez használt, átalakítása és a szöveg vagy kép tartalom indexelés során használt kognitív képességek gyűjteménye. A skillset létrehozása lehetővé teszi, hogy szöveg- és képbővítéseket csatoljon az adatbetöltési fázisban, új információk és struktúrák kinyerése és létrehozása a nyers tartalomból.

A skillset három tulajdonsággal rendelkezik:

+   ```skills```, a képességek rendezetlen gyűjteménye, amelyhez a platform az egyes szakértelemhez szükséges bemenetek alapján határozza meg a végrehajtás sorrendjét
+   ```cognitiveServices```, a kognitív szolgáltatások kulcsa szükséges számlázása a kognitív képességek meghívott
+   ```knowledgeStore```, az a tárfiók, ahol a bővített dokumentumok kivetítésre kerülnek



Skillsets szerzője JSON. A [kifejezésnyelv](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional)használatával bonyolult készségeket hozhat létre a hurkolással és [az elágazással.](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional) A kifejezés nyelve a [JSON-mutató](https://tools.ietf.org/html/rfc6901) elérési útjának néhány módosításával azonosítja a dúsítási fa csomópontjait. A ```"/"``` a fa egy szinttel ```"*"``` alacsonyabb szintjén halad át, és a környezetben minden operátorként működik. Ezeket a fogalmakat a legjobban egy példával lehet leírni. A koncepciók és képességek bemutatására végigsétálunk a [szálloda értékelései között.](knowledge-store-connect-powerbi.md) A skillset megtekintéséhez, miután követte az importálási adatok munkafolyamat, akkor kell használni a REST API-ügyfél [a skillset beszerzése.](https://docs.microsoft.com/rest/api/searchservice/get-skillset)

### <a name="enrichment-tree"></a>Dúsító fa

Ha el szeretné képzelni, hogy egy készségkészlet hogyan gazdagítja fokozatosan a dokumentumot, kezdjük azzal, hogy hogyan néz ki a dokumentum, mielőtt bármilyen gazdagodás következne be. A dokumentumfeltörés kimenete az adatforrástól és a kiválasztott elemzési módtól függ. Ez annak a dokumentumnak az állapota is, amelyből a [mezőleképezések](search-indexer-field-mappings.md) tartalmat tudnak forrásolni, amikor adatokat adnak hozzá a keresési indexhez.
![Tudástároló a folyamatábrán](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Tudástároló a folyamatábrán")

Ha egy dokumentum a dúsítási folyamat, akkor jelenik meg, mint egy fa a tartalom és a kapcsolódó dúsítások. Ez a fa példányos, mint a kimenet idokumentum repedés. A dúsítási fa formátumlehetővé teszi, hogy a dúsítási folyamat metaadatokat csatoljon még primitív adattípusokhoz is, nem érvényes JSON-objektum, de érvényes JSON formátumba vetíthető. Az alábbi táblázat a dúsítási folyamatba belépő dokumentum állapotát mutatja be:

|Adatforrás\Elemzési mód|Alapértelmezett|JSON, JSON Vonalak & CSV|
|---|---|---|
|Blob Storage|/dokumentum/tartalom<br>/dokumentum/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|N/A |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|N/A|

 A képességek végrehajtása, új csomópontokat adnak hozzá a dúsító fa. Ezek az új csomópontok ezután az alsóbb rétegbeli képességek bemeneteként használhatók, kivetítve a tudástárolóba, vagy indexmezőkleképezéseként. A dúsítások nem módosíthatók: a létrehozás után a csomópontok nem szerkeszthetők. Ahogy a skillsets egyre összetettebb, így lesz a dúsító fa, de nem minden csomópont a dúsító fa kell tenni, hogy az index vagy a tudás tároló. 

Szelektíven csak az index vagy a tudástároló dúsítási alkészletét tarthatja meg.
A dokumentum további részében azt feltételezzük, hogy [szállodai értékelésekkel](https://docs.microsoft.com/azure/search/knowledge-store-connect-powerbi)dolgozunk , de ugyanazok a fogalmak vonatkoznak az összes többi adatforrásból származó dokumentumok gazdagítására.

### <a name="context"></a>Környezet
Minden szakértelemhez kontextus szükséges. A környezet határozza meg:
+   A szakértelem végrehajtásának száma a kiválasztott csomópontok alapján. A típusgyűjtemény környezeti értékeinek ```/*``` hozzáadása a végén a szakértelem meghívását eredményezi a gyűjtemény minden egyes példánya esetén. 
+   Ahol a dúsítási fa a szakértelem kimenetek egészül ki. A kimenetek mindig a környezeti csomópont gyermekeiként kerülnek a fához. 
+   A bemenetek alakja. Többszintű gyűjtemények esetén a szülőgyűjtemény környezetének beállítása hatással lesz a szakértelem bemenetének alakjára. Ha például van egy dúsító fával az országok listájával, mindegyik az irányítószámok listáját tartalmazó állapotlistával gazdagítva.

|Környezet|Input (Bemenet)|A bemenet alakja|Szakértelem meghívása|
|---|---|---|---|
|```/document/countries/*``` |```/document/countries/*/states/*/zipcodes/*``` |Az ország összes irányítószámának listája |Országonként egyszer |
|```/document/countries/*/states/*``` |```/document/countries/*/states/*/zipcodes/*``` |Az irányítószámok listája az államban | Országonként és államonként egyszer|

### <a name="sourcecontext"></a>Forráskörnyezet

`sourceContext` Az csak szakértelem-bemenetekben és [előrejelzésekben használatos](knowledge-store-projection-overview.md). Többszintű, beágyazott objektumok építésére szolgál. Előfordulhat, hogy létre kell hoznia egy új objektumot, hogy vagy adja át egy szakértelem vagy projekt bemeneteként a tudástárba. Mivel a dúsítási csomópontok nem lehetnek érvényes JSON-objektumok a dúsítási fában, és a fa csomópontjára való hivatkozás csak a csomópont létrehozásakor adja vissza a csomópont állapotát, a dúsítások szakértelem-bemenetként vagy kivetítésként használva egy jól formázott JSON-objektum létrehozásához. A `sourceContext` lehetővé teszi egy hierarchikus, névtelen típusú objektum létrehozásához, amely több készséget igényelne, ha csak a környezetet használja. A `sourceContext` használat a következő szakaszban látható. Tekintse meg a szakértelem kimenet, amely létrehozott egy dúsítási annak megállapítására, hogy érvényes JSON-objektum, és nem egy primitív típusú.

### <a name="projections"></a>Leképezések

A vetítés az a folyamat, amelynek során kiválasztja a tudástárolóban mentendő csomópontokat a dúsítási fából. A vetítések a dokumentum egyéni alakzatai (tartalom és dúsítások), amelyek tábla- vagy objektumvetületként is kiadhatók. Ha többet szeretne tudni az előrejelzésekkel való munkáról, olvassa el [az Előrejelzések kelései](knowledge-store-projection-overview.md).

![Mezőleképezési beállítások](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Mezőleképezési beállítások a dúsítási folyamathoz")

A fenti diagram a dúsítási folyamat helye alapján ismerteti a kijelölt választót.

## <a name="generate-enriched-data"></a>Bővített adatok létrehozása 

Most lépjen át a szálloda értékelés skillset, akkor kövesse a [bemutató,](knowledge-store-connect-powerbi.md) hogy megteremtse a skillset vagy [tekintse meg](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/samples/skillset.json) a skillset. Meg fogjuk nézni:

* hogyan fejlődik a dúsító fa az egyes készségek végrehajtásával 
* hogyan működik a környezet és a bemenetek annak meghatározásához, hogy egy szakértelem hányszor hajtvégre 
* a szövegkörnyezetalapján a bemenet alakja. 

Mivel az indexelő hez a tagolt szövegelemzési módot használjuk, a dúsítási folyamaton belüli dokumentum egyetlen sort jelent a CSV-fájlban.

### <a name="skill-1-split-skill"></a>skill #1: split készség 

![dúsítási fa a dokumentum feltörése után](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Dúsítási fa a dokumentum feltörése után és a szakértelem végrehajtása előtt")

A ( a) szakértelemivel ez ```"/document/reviews_text"``` `reviews_text`a képesség egyszer végre fog hajtani a számára. A szakértelem kimenet egy `reviews_text` lista, ahol a van osztva 5000 karakter szegmensek. A felosztási szakértelem kimenete neve `pages` és hozzáadása a dúsítási fához. A `targetName` szolgáltatás lehetővé teszi egy szakértelem-kimenet átnevezését, mielőtt hozzáadna a dúsítási fához.

A dúsítási fa most már egy új csomópont keretében a szakértelem. Ez a csomópont minden szakértelem, vetítés vagy kimeneti mező leképezése számára elérhető.


Az összes dúsítás gyökércsomópontja . `"/document"` Blob indexelők, a `"/document"` csomópont lesz gyermek csomópontok `"/document/content"` és. `"/document/normalized_images"` A CSV-adatokkal végzett munka során, ahogy ebben a példában `"/document"`is, az oszlopnevek az alatta lévő csomópontokra lesznek leképezve. A szakértelem által a csomóponthoz hozzáadott dúsítások eléréséhez a dúsítás teljes elérési útja szükséges. Ha például a ```pages``` csomópont szövegét egy másik szakértelem beviteleként szeretné használni, meg ```"/document/reviews_text/pages/*"```kell adnia azt .
 
 ![dúsító fa után készség #1](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "Dúsítási fa a szakértelem #1 végrehajtása után")

### <a name="skill-2-language-detection"></a>Készség #2 nyelv felismerése
 Míg a nyelvfelismerési szakértelem a skillsetben meghatározott harmadik (készség#3) szakértelem, ez a következő végrehajtandó szakértelem. Mivel nem blokkolja a bemenetek, akkor hajtsa végre párhuzamosan az előző szakértelem. Az azt megelőző felosztási szakértelemhez hasonlóan a nyelvfelismerési szakértelem is egyszer meghívásra kerül minden dokumentumhoz. A dúsító fa most már egy új csomópont a nyelv.
 ![dúsító fa után készség #2](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Dúsítási fa a #2 végrehajtása után")
 
 ### <a name="skill-3-key-phrases-skill"></a>Skill #3: Key kifejezések skill 

Mivel ```/document/reviews_text/pages/*``` a kontextus a legfontosabb kifejezések szakértelem meghívása egyszer a `pages` gyűjtemény minden elem. A szakértelem kimenete egy csomópont lesz a társított oldalelem alatt. 

 Most már képesnek kell lennie arra, hogy nézd meg a többi készségek a skillset és elképzelni, hogy a fa a dúsítások továbbra is növekedni fog a végrehajtása minden készség. Egyes képességek, például az egyesítési szakértelem és a formázó-szakértelem is hoznak létre új csomópontokat, de csak meglévő csomópontokból származó adatokat használnak, és nem hoznak létre nettó új gazdagítást.

![dúsító fa után minden készségek](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Dúsító fa után minden készségek")

A fenti fa összekötőinek színei azt jelzik, hogy a dúsításokat különböző képességek hozták létre, és a csomópontokat egyenként kell kezelni, és nem lesznek részei a szülőcsomópont kiválasztásakor visszaadott objektumnak.

## <a name="save-enrichments-in-a-knowledge-store"></a>Gazdagítások mentése tudástárolóban 

Skillsets is meghatározza a tudás tároló, ahol a bővített dokumentumok vetíthető késedelmi táblák vagy objektumok. Ha a bővített adatokat a tudástárolóba szeretné menteni, meg kell határoznia a bővített dokumentum vetületeinek készletét. Ha többet szeretne megtudni a tudástárolóról, olvassa el a [tudástároló áttekintését](knowledge-store-concept-intro.md)

### <a name="slicing-projections"></a>Szeletelési vetületek

Táblázatvetítési csoport definiálásakor a dúsítási fa egyetlen csomópontja több kapcsolódó táblára szeletelhető. Ha olyan táblát ad hozzá, amelynek forráselérési útja egy meglévő táblavetítés gyermeke, az eredményül kapott gyermekcsomópont nem lesz a meglévő táblavetítés gyermeke, hanem az új, kapcsolódó táblába lesz vetítve. Ez a szeletelési technika lehetővé teszi, hogy egyetlen csomópontot definiáljon egy formázó-szakértelemben, amely az összes táblavetés forrása lehet. 

### <a name="shaping-projections"></a>Vetítések alakítása

A vetület kétféleképpen határozható meg. Használhat egy formázó-képességet egy új csomópont létrehozásához, amely az összes kivetített dúsítás gyökércsomópontja. Ezután a kivetítésekben csak a formázó képesség kimenetére hivatkozik. A vetítési definíción belül is kialakíthat egy vetületet.

A formázó megközelítés bőbeszédűbb, mint a szövegközi formázás, de biztosítja, hogy a dúsító fa összes mutációja a képességekben legyen, és hogy a kimenet olyan objektum legyen, amely újra felhasználható. A szövegközi formázás lehetővé teszi a szükséges alakzat létrehozását, de névtelen objektum, és csak azon vetítés számára érhető el, amelyhez definiálva van. A megközelítések együttesen vagy külön-külön is használhatók. A portálmunkafolyamatban létrehozott skillset mindkettőt tartalmazza. A táblavetések hez formázó-készséget használ, de a szövegközi formázást is használja a kulcskifejezések táblázatának kivetítéséhez.

A példa kiterjesztéséhez eltávolíthatja a szövegközi formázást, és formázó-képességgel új csomópontot hozhat létre a kulcskifejezésekhez. Ha három táblára vetített alakzatot `hotelReviewsDocument`szeretne `hotelReviewsPages`létrehozni, nevezetesen a , és `hotelReviewsKeyPhrases`a két lehetőséget a következő szakaszok ismertetik.


#### <a name="shaper-skill-and-projection"></a>Formázó készség és vetítés 

> [!Note]
> A dokumentumtábla néhány oszlopát rövidséggel törölték ebből a példából.
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

A `tableprojection` fenti `outputs` szakaszban definiált csomóponttal most már használhatjuk a szeletelési funkciót a `tableprojection` csomópont egyes részeinek különböző táblákba történő kivetítéséhez:

> [!Note]
> Ez csak a tudástároló konfigurációjában lévő vetítés egy részlete.
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

#### <a name="inline-shaping-projections"></a>Inline formáló vetületek

A szövegközi formázási megközelítés nem igényel formázókészséget, mivel az előrejelzésekhez szükséges összes alakzat akkor jön létre, amikor szükség van rájuk. Ha ugyanazokat az adatokat szeretné kivetíteni, mint az előző példában, a szövegközi vetítési lehetőség a következőképpen néz ki:

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
  
Mindkét megközelítés egyik észrevétele az, `"Keyphrases"` hogy az `"sourceContext"`értékek hogyan vetítődnek előre a használatával. A `"Keyphrases"` karakterláncok gyűjteményét tartalmazó csomópont maga is az oldal szövegének gyermeke. Mivel azonban a kivetítések hez JSON-objektum szükséges, `"sourceContext"` és az oldal primitív (karakterlánc), a program a kulcsmondatot egy elnevezett tulajdonsággal rendelkező objektumba csomagolja. Ez a technika lehetővé teszi, hogy még a primitívek is egymástól függetlenül legyenek kivetítve.

## <a name="next-steps"></a>További lépések

Következő lépésként hozza létre az első skillset kognitív készségekkel.

> [!div class="nextstepaction"]
> [Hozza létre az első skillset](cognitive-search-defining-skillset.md).
