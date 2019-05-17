---
title: Egy indexmezők – Azure Search a Cognitive Services-erőforrás csatolása
description: A Cognitive Services teljes körű előfizetéssel csatolása az Azure Search cognitive Adatbővítés folyamat utasítások.
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 69b03bd24abcdf502bf80cfce4221f4958058932
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65541711"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Az Azure Search-képességek alkalmazási lehetőségét a Cognitive Services-erőforrás csatolása 

AI-algoritmusokat meghajtó a [indexelés kognitív folyamatok](cognitive-search-concept-intro.md) az Azure Search strukturálatlan adatok feldolgozására használható. Ezek az algoritmusok alapuló [Azure Cognitive Services-erőforrások](https://azure.microsoft.com/services/cognitive-services/), többek között [Computer Vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/) képelemzés és optikai karakterfelismerés (OCR) és [Szövegelemzés](https://azure.microsoft.com/services/cognitive-services/text-analytics/) entitásfelismerés, kulcsszókeresést és más végrehajtott információbeolvasás.

Dokumentumok korlátozott számú ingyenes bővítheti, vagy csatolhat a Cognitive Services-számlázható erőforrás, nagyobb és gyakoribb számítási feladatokhoz. Ebből a cikkből megtudhatja, hogyan Cognitive Services-erőforrás társítása során adatokat feldúsítani a kognitív képességek alkalmazási lehetőségét [Azure Search-indexelő](search-what-is-an-index.md).

Akkor is, ha a folyamat áll, amelyek nem kapcsolódnak a Cognitive Services API-k képességeit, a Cognitive Services-erőforrás továbbra is kell csatlakoztatnia. Az ingyenes erőforrás, amely korlátozza, hogy naponta végrehajtott információbeolvasás kis számú ezzel felülírja. Cognitive Services API-k nem kötött képességek nem kell fizetnie. Ezek a képességek közé tartozik [egyéni képesség](cognitive-search-create-custom-skill-example.md), [szövegegyesítő](cognitive-search-skill-textmerger.md), [szöveg felosztó](cognitive-search-skill-textsplit.md), és [shaper](cognitive-search-skill-shaper.md).

> [!NOTE]
> Hatókör kibontásának feldolgozása, további dokumentumok hozzáadása, illetve további AI-algoritmusokat hozzáadása gyakoriságának növelése, mivel kell számlázható Cognitive Services-erőforrás csatolása. Az elszámolás a Cognitive Services API-k és a lemezkép kinyerése a az Azure Search-dokumentumfeltörést fázis részeként. A dokumentumok közül szövegkinyerés nem kell fizetnie.
>
> Beépített képességek végrehajtási történik a [használatalapú-as-, a Cognitive Services nyissa meg az árat](https://azure.microsoft.com/pricing/details/cognitive-services/). Kép kinyerési díjszabással kapcsolatos információkért lásd: a [díjszabását ismertető oldalt az Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="use-free-resources"></a>Ingyenes erőforrások használata

A korlátozott, ingyenes feldolgozási beállítás segítségével végezze el a cognitive search oktatóanyag és a rövid útmutató gyakorlatok.

Ingyenes (korlátozott végrehajtott információbeolvasás) erőforrások / nap-előfizetésenként 20 dokumentumok korlátozódnak.

1. Nyissa meg az adatok importálása varázsló:

   ![Nyissa meg az adatok importálása varázsló](media/search-get-started-portal/import-data-cmd2.png "nyissa meg az adatok importálása varázsló")

1. Válasszon egy adatforrást, és továbbra is **hozzáadása a kognitív keresés (nem kötelező)**. Ezzel a varázslóval részletes bemutatóért lásd: [importálása index és a portál eszközei használatával lekérdezés](search-get-started-portal.md).

1. Bontsa ki a **csatolása a Cognitive Services** majd **(korlátozott végrehajtott információbeolvasás) ingyenes**:

   ![Cognitive Services csatolása szakasz kibontva](./media/cognitive-search-attach-cognitive-services/attach1.png "Cognitive Services csatolása kibontva szakasz")

1. A következő lépésben **hozzáadása végrehajtott Információbeolvasás**. A portálon elérhető képességek ismertetését lásd: [2. lépés: Kognitív képességek hozzáadása](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) a kognitív keresés a rövid útmutatóban.

## <a name="use-billable-resources"></a>Számlázható erőforrások használata

A számítási feladatokhoz, amely naponta több mint 20 végrehajtott információbeolvasás létrehozása kell csatolnia a Cognitive Services-számlázható erőforrás.

Csak a képességek, amelyek a Cognitive Services API-k hívása díjkötelesek. Nem díjköteles [egyéni képesség](cognitive-search-create-custom-skill-example.md), vagy képességek, például [szövegegyesítő](cognitive-search-skill-textmerger.md), [szöveg felosztó](cognitive-search-skill-textsplit.md), és [shaper](cognitive-search-skill-shaper.md), amely nem az API-alapú.

1. Adatok importálása varázsló megnyitása, válasszon egy adatforrást, és továbbra is **hozzáadása a kognitív keresés (nem kötelező)**.

1. Bontsa ki a **csatolása a Cognitive Services** majd **új Cognitive Services-erőforrás létrehozása**. Egy új lapon nyílik meg, hogy az erőforrás hozhat létre:

   ![Cognitive Services-erőforrás létrehozása](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Cognitive Services-erőforrás létrehozása")

1. Az a **hely** listában, válassza ki a régiót, ahol az Azure Search szolgáltatás megtalálható. Ebben a régióban használja a megfelelő teljesítmény biztosítása érdekében kell. Régiók között is használata ebben a régióban szabadhézag kimenő sávszélesség-költségeket.

1. Az a **tarifacsomag** listáról válassza ki **S0** , a Cognitive Services szolgáltatásait, beleértve a biztonsági másolatot az Azure Search által használt, előre megadott képesség látás- és nyelvi funkciók teljes körű gyűjtemény beolvasása.

   A S0 szint található árfolyamok adott munkaterhelés konkrét a [Cognitive Services díjszabási oldalán](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + Az a **ajánlat kiválasztása** listában, ellenőrizze, hogy **Cognitive Services** van kiválasztva.
   + A **nyelvi** szolgáltatásokat, a díjait **Text Analytics Standard** mesterséges indexeléshez vonatkoznak.
   + Alatt **Vision** szolgáltatásokat, a díjait **számítógép Vision S1** vonatkoznak.

1. Válassza ki **létrehozás** az új Cognitive Services-erőforrás létrehozásához.

1. Térjen vissza az előző lapra, amely tartalmazza az adatok importálása varázsló. Válassza ki **frissítése** megjelenítése a Cognitive Services-erőforrás, és válassza ki az erőforrást:

   ![Válassza ki a Cognitive Services-erőforrás](./media/cognitive-search-attach-cognitive-services/attach2.png "válassza ki a Cognitive Services-erőforrás")

1. Bontsa ki a **hozzáadása végrehajtott Információbeolvasás** szakaszban jelölje be az adott kognitív képességeket, amely futtatja az adatokon. Fejezze be a varázslót. A portálon elérhető képességek ismertetését lásd: [2. lépés: Kognitív képességek hozzáadása](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) a kognitív keresés a rövid útmutatóban.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Egy meglévő tudásával Cognitive Services-erőforrás csatolása

Ha rendelkezik egy meglévő tudásával, egy új vagy eltérő a Cognitive Services-erőforrás is csatlakoztatható.

1. Az a **szolgáltatás áttekintése** lapon jelölje be **szakértelmével**:

   ![Szakértelmével lapon](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "szakértelmével lap")

1. Válassza ki a nevét, a képességek alkalmazási lehetőségét, és válassza ki a meglévő erőforrást, vagy hozzon létre egy újat. Válassza ki **OK** , hagyja jóvá a módosításokat.

   ![Képességcsoport erőforráslista](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "indexmezők erőforrások listája")

   Ne feledje, hogy a **(korlátozott végrehajtott információbeolvasás) ingyenes** a beállítás korlátozza, 20 dokumentumok naponta, és használható **új Cognitive Services-erőforrás létrehozása** egy új számlázható erőforrás kiépítéséhez. Ha egy új erőforrást hoz létre, válassza ki a **frissítése** a Cognitive Services-erőforrások listájának frissítése, és válassza ki az erőforrást.

## <a name="attach-cognitive-services-programmatically"></a>Cognitive Services programozott módon csatolása

Definiálásakor készségeitől programozott módon, adjon hozzá egy `cognitiveServices` készségeitől szakaszt. A szakasz a kulcsot a Cognitive Services-erőforrás készségeitől társítani kívánt tartalmazzák. Ne feledje, hogy az erőforrás és az Azure Search-erőforrásnak ugyanabban a régióban kell lennie. Is `@odata.type`, és állítsa be `#Microsoft.Azure.Search.CognitiveServicesByKey`.

Az alábbi példa bemutatja ennek a mintának. Figyelje meg a `cognitiveServices` szakasz végén található a definíciót.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
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

Ha meg szeretné becsülni az indexelés kognitív kereséssel kapcsolatos költségeket, kezdje ötlet, mire az átlagos dokumentum néz ki számok futtatásához. Előfordulhat például, a hozzávetőleges:

+ 1000 PDF-fájlok.
+ Hat lapok minden.
+ Egy kép egy-egy lapon (6000 lemezképek esetén).
+ 3000 karakterek egy-egy lapon.

Tegyük fel, hogy egy folyamatot, amely az egyes PDF-, kép- és szöveg kinyerése, optikai karakterfelismerés (OCR) lemezképek dokumentumfeltörést és szervezetek entitások felismerése.

Az ebben a cikkben látható árak elméleti. Bemutatják a becslési folyamat használhatók. Lehet, hogy a költség alacsonyabb. A tényleges árak a tranzakciók, lásd: lásd: [Cognitive Services díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Szöveg- és tartalommal dokumentumleképezési szövegkinyerés jelenleg ingyenes. 6000 képek esetén minden 1000 lemezképek kibontott 1 dolláros feltételezik. Ez az ehhez a lépéshez 6,00 $ költsége.

2. Az optikai Karakterfelismerés cognitive szakértelem optikai Karakterfelismerés 6000 lemezképek angol nyelven, a legjobb algoritmus (DescribeText) használja. Ha egy 2,50 $ / 1000 lemezképek elemezni költségét, ehhez a lépéshez $15,00 lenne fizet.

3. Az entitások kinyeréséhez összesen három szöveg rekordok száma oldalanként kell. Minden bejegyzés 1000 karakter szerepel. Három szöveg rekordok száma oldalanként 6000 oldalak megszorozza 18,000 szöveg rekordok egyenlő. 2,00 $ / 1000 szöveg rekordok, feltéve, hogy ezt a lépést akkor költség $36.00.

A teljes kép, fizetjük készül $57.00 az itt ismertetett készségeitől ilyen típusú 1000 PDF-dokumentumok betöltését.

## <a name="next-steps"></a>További lépések
+ [Az Azure Search-díjszabást ismertető oldalon](https://azure.microsoft.com/pricing/details/search/)
+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)
+ [Képességcsoport (REST) létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Hogyan hidaljuk mezők leképezése](cognitive-search-output-field-mapping.md)
