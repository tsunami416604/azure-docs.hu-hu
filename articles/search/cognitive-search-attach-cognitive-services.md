---
title: Egy indexmezők – Azure Search a Cognitive Services-erőforrás csatolása
description: A Cognitive Services teljes körű előfizetéssel csatolása az Azure Search cognitive Adatbővítés folyamat utasítások.
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: bfa9bbb9816148182b79a8231f2ddb3e46433804
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2019
ms.locfileid: "54413243"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Az Azure Search-képességek alkalmazási lehetőségét a Cognitive Services-erőforrás csatolása 

AI-algoritmusokat meghajtó a [cognitive search folyamatok](cognitive-search-concept-intro.md) az egy Azure Search szolgáltatásban az indexelés művelet strukturálatlan adatok feldolgozására használható. Ezek az algoritmusok alapuló [Cognitive Services-erőforrások](https://azure.microsoft.com/services/cognitive-services/), többek között [Computer Vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/) képelemzés és optikai karakterfelismerés (OCR), és [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/)entitásfelismerés, kulcsszókeresést és más végrehajtott információbeolvasás.

Dokumentumok korlátozott számú ingyenes bővítését, vagy nagyobb és gyakoribb számítási feladatok Cognitive Services-számlázható erőforrás csatolása. Ebből a cikkből megtudhatja, hogyan társítsa a Cognitive Services-erőforrás során adatokat feldúsítani a kognitív képességek alkalmazási lehetőségét [Azure Search-indexelő](search-what-is-an-index.md).

Ha a folyamat a Cognitive Services API-k egymástól független képességek áll, továbbra is kell megadni a Cognitive Services-erőforrás. Ez igen felülbírálások a **ingyenes** , amely korlátozza, hogy naponta végrehajtott információbeolvasás kis mennyiségű erőforrást. Nem jár, hogy nem köti a Cognitive Services API-k képességek. Ezek a képességek közé tartozik: [egyéni képesség](cognitive-search-create-custom-skill-example.md), [szövegegyesítő](cognitive-search-skill-textmerger.md), [szöveg felosztó](cognitive-search-skill-textsplit.md), és [shaper](cognitive-search-skill-shaper.md).

> [!NOTE]
> 2018. December 21. Cognitive Services-erőforrás is társíthat egy Azure Search-képességek alkalmazási lehetőségét. Ez lehetővé teszi számunkra, hogy indexmezők végrehajtási számítunk fel díjat. Ezen a napon azt is már díjszabási kép kinyerése a dokumentumfeltörést fázis részeként. A dokumentumok szövegkinyerés továbbra is ingyenesen kínáljuk.
>
> [Beépített kognitív szakértelem](cognitive-search-predefined-skills.md) végrehajtás díja a [használatalapú-as-, a Cognitive Services nyissa meg az árat](https://azure.microsoft.com/pricing/details/cognitive-services), azonos értékelje, ha végrehajtotta a feladat közvetlenül. Kép kinyerése nem egy Azure Search költségekkel, jelenleg az előzetes verzió áron érhető el. További információkért lásd: a [díjszabását ismertető oldalt az Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400) vagy [számlázás módját works](search-sku-tier.md#how-billing-works).


## <a name="use-free-resources"></a>Ingyenes erőforrások használata

A korlátozott, ingyenes feldolgozási beállítás segítségével végezze el a cognitive search oktatóanyag és a rövid útmutató gyakorlatok. 

> [!Important]
> 2019. február 1-től kezdődően a **(korlátozott végrehajtott Információbeolvasás) ingyenes** lesznek korlátozva a naponta 20 dokumentumokhoz. 

1. Nyissa meg a **adatimportálás** varázsló.

   ![Adatok importálása parancs](media/search-get-started-portal/import-data-cmd2.png "adatok importálása parancs")

1. Válasszon egy adatforrást, és továbbra is **hozzáadása (nem kötelező) a kognitív keresés**. Ezzel a varázslóval részletes bemutatóért lásd: [importálása index és a lekérdezés portal-eszközök használatával](search-get-started-portal.md).

1. Bontsa ki a **csatolása a Cognitive Services** válassza **(korlátozott végrehajtott Információbeolvasás) ingyenes**.

   ![Cognitive Services csatolása szakasz kibontva](./media/cognitive-search-attach-cognitive-services/attach1.png "Cognitive Services csatolása kibontva")

A következő lépésben **végrehajtott információbeolvasás hozzáadása**. A portálon elérhető képességek ismertetését lásd: ["2. lépés: A cognitive ismeretek hozzáadása"](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) a kognitív keresés a rövid útmutatóban.

## <a name="use-billable-resources"></a>Számlázható erőforrások használata

A több mint 20 végrehajtott információbeolvasás naponta számozása számítási feladatokhoz kell csatolnia a Cognitive Services-számlázható erőforrás. 

Képességek, amelyek a Cognitive Services API-k hívása csak díjkötelesek. Nem-API-alapú képességek, például [egyéni képesség](cognitive-search-create-custom-skill-example.md), [szövegegyesítő](cognitive-search-skill-textmerger.md), [szöveg felosztó](cognitive-search-skill-textsplit.md), és [shaper](cognitive-search-skill-shaper.md) képességek nem lesznek számlázva.

1. Az a **adatimportálás** varázsló **csatolása a Cognitive Services**, válasszon egy meglévő erőforrást, vagy kattintson a **új Cognitive Services-erőforrás létrehozása**.

1. A **új Cognitive Services-erőforrás létrehozása**, egy új lapon nyílik meg, hogy az erőforrást is létrehozhat. Adjon meg egy egyedi nevet az erőforráshoz.

1. Válassza ki az Azure Search megegyező helyen. Indexelés kognitív képességek jelenleg ezekben a régiókban támogatott:

  * USA nyugati középső régiója
  * USA déli középső régiója
  * USA keleti régiója
  * USA 2. keleti régiója
  * USA nyugati régiója, 2.
  * Közép-Kanada
  * Nyugat-Európa
  * Az Egyesült Királyság déli régiója
  * Észak-Európa
  * Dél-Brazília
  * Délkelet-Ázsia
  * Közép-India
  * Kelet-Ausztrália

1. Válassza ki a teljes körű tarifacsomagot **S0**. Ez a szint az előre megadott képesség a cognitive search biztonsági látás- és nyelvi szolgáltatásokat biztosítja.

    ![Hozzon létre egy új Cognitive Services-erőforrás](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "egy új Cognitive Services-erőforrás létrehozása")

1. Kattintson a **létrehozás** az új Cognitive Services-erőforrás létrehozásához. 

1. Térjen vissza az előző lapon tartalmazó a **adatimportálás** varázsló. Kattintson a **frissítése** megjelenítése a Cognitive Services-erőforrás, és válassza ki az erőforrást.

   ![Cognitive Services-erőforrás kiválasztott](./media/cognitive-search-attach-cognitive-services/attach2.png "kiválasztott Cognitive Service erőforrás")

1. Bontsa ki a **hozzáadása végrehajtott Információbeolvasás** válassza ki az adott kognitív képességekkel, amelyek az adatok futtatni kívánt szakaszt, és a többi folyamat folytatásához. A portálon elérhető képességek ismertetését lásd: ["2. lépés: A cognitive ismeretek hozzáadása"](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) a kognitív keresés a rövid útmutatóban.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Egy meglévő tudásával Cognitive Services-erőforrás csatolása

Ha rendelkezik egy meglévő tudásával, egy új vagy eltérő a Cognitive Services-erőforrás is csatlakoztatható.

1. Az a **szolgáltatás áttekintése** kattintson **szakértelmével**.

   ![Szakértelmével lapon](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "szakértelmével lap")

1. Kattintson a képességek alkalmazási lehetőségét, és válassza ki a meglévő erőforrás, vagy hozzon létre egy újat. Kattintson a **OK** , hagyja jóvá a módosításokat. 

   ![Képességcsoport erőforráslista](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "indexmezők erőforrások listája")

Emlékeztetnek arra, hogy **(korlátozott végrehajtott Információbeolvasás) ingyenes** korlátozott a naponta 20 dokumentumokhoz, és hogy van **új Cognitive Services-erőforrás létrehozása** épít ki egy új számlázható erőforrás. Ha egy új erőforrást hoz létre, válassza ki a **frissítése** a Cognitive Services-erőforrások listájának frissítése, és válassza ki az erőforrást.

## <a name="attach-cognitive-services-programmatically"></a>Cognitive Services programozott módon csatolása

Definiálásakor készségeitől programozott módon, adjon hozzá egy `cognitiveServices` készségeitől szakaszt. A szakaszban a kulcsot a Cognitive Services-erőforrás készségeitől társítani kívánt tartalmazza. Ne felejtse el, hogy az erőforrást az Azure Search és ugyanabban a régióban kell lennie. Is `@odata.type`, és állítsa be `#Microsoft.Azure.Search.CognitiveServicesByKey`. 

Az alábbi példa bemutatja ennek a mintának. Figyelje meg, hogy a cognitive Services szakasz alján, a definition

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>Példa: Költségek megbecslése

Indexelés kognitív kereséssel kapcsolatos költségeket megbecsülheti, indítsa el a képet az átlagos dokumentum néz ki, hogy egyes számok is futtatható. Ha például költségbecslési célokat, akkor előfordulhat, hogy hozzávetőleges:

+ 1000 PDF-fájlok
+ Hat oldalak
+ Egy rendszerkép egy-egy lapon (6000 lemezképek esetén)
+ 3000 karakterek száma oldalanként

Tegyük fel, repedés a képi és szöveges kinyerési, optikai karakterfelismerés (OCR) a képek, PDF-dokumentumot, elnevezett entitásfelismerés szervezetek álló folyamatot. 

Ebben a gyakorlatban a legköltségesebb tranzakciónkénti árral használunk. Tényleges költségek miatt adatmennyiségen díjszabás alacsonyabb lehet. Lásd: [Cognitive Services díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Szöveg- és tartalommal dokumentumleképezési szövegkinyerés jelenleg ingyenes. 6000 képek esetén minden 1000 lemezképek ki kell olvasni, ára $6,00 ehhez a lépéshez 1 dolláros feltételezik.

2. Az optikai Karakterfelismerés cognitive szakértelem optikai Karakterfelismerés 6000 lemezképek angol nyelven, a legjobb algoritmus (DescribeText) használja. Ha egy 2,50 $ / 1000 lemezképek elemezni költségét, ehhez a lépéshez $15,00 lenne fizet.

3. Az entitások kinyeréséhez 3 szöveg rekordok száma oldalanként összesen kell. Minden bejegyzés 1000 karakter szerepel. Három szöveg rekordok száma oldalanként * 6000 oldalak = 18,000 szöveg rögzíti. 2,00 $ / 1000 szöveg rekordok, feltéve, hogy ezt a lépést akkor költség $36.00.

A teljes kép, fizetjük készül $57.00 az itt ismertetett készségeitől ilyen jellegű 1000 PDF-dokumentumok betöltését. 

## <a name="next-steps"></a>További lépések
+ [Az Azure Search-díjszabást ismertető oldalon](https://azure.microsoft.com/pricing/details/search/)
+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)
+ [Képességcsoport (REST) létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Hogyan hidaljuk mezők leképezése](cognitive-search-output-field-mapping.md)
