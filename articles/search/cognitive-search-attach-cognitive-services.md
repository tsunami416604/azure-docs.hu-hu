---
title: Cognitive Services csatolása egy skillsethez
titleSuffix: Azure Cognitive Search
description: Útmutató a Cognitive Services all-in-one előfizetésének az Azure Cognitive Search ai-dúsítási folyamathoz csatolásához.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 254c912114e3f1c7a495f389bc6a6416cbde7e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472451"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Cognitive Services-erőforrás csatolása egy skillsethez az Azure Cognitive Search-ben 

Egy dúsítási folyamat konfigurálása az Azure Cognitive Search, gazdagíthatja a korlátozott számú dokumentumot ingyenesen. Nagyobb és gyakoribb számítási feladatok esetén csatolnia kell egy számlázható Cognitive Services-erőforrást.

Ebben a cikkben megtudhatja, hogyan csatolhat egy erőforrást egy kulcsot egy dúsítási folyamatot meghatározó skillset hozzárendelésével.

## <a name="resources-used-during-enrichment"></a>A dúsítás során felhasznált erőforrások

Az Azure Cognitive Search függőséget mutat a Cognitive Services szolgáltatástól, beleértve a [számítógépes látást](https://azure.microsoft.com/services/cognitive-services/computer-vision/) a képelemzéshez és az optikai karakterfelismeréshez (OCR), a természetes nyelvi [feldolgozáshoz szükséges szövegelemzéseket](https://azure.microsoft.com/services/cognitive-services/text-analytics/) és más bővítéseket, például [a szövegfordítást.](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) Az Azure Cognitive Search gazdagításának környezetében ezek az AI-algoritmusok egy *szakértelembe*vannak csomagolva, egy *skillsetbe*helyezve, és egy *indexelő* hivatkozik az indexelés során.

## <a name="how-billing-works"></a>A számlázás működése

+ Az Azure Cognitive Search a Cognitive Services erőforráskulcs át, amelyet egy skillset a kép és a szöveg gazdagítása számlázásához. A számlázható készségek végrehajtása a [Cognitive Services díjfizetéses díja.](https://azure.microsoft.com/pricing/details/cognitive-services/)

+ A képkinyerés egy Azure Cognitive Search-művelet, amely akkor fordul elő, ha a dokumentumok a dúsítás előtt feltörik. A kép kinyerése számlázható. A lemezképek kinyerési díjszabását az [Azure Cognitive Search díjszabási oldalán találja.](https://go.microsoft.com/fwlink/?linkid=2042400)

+ A szöveg kibontása a dokumentum feltörési mondata során is előfordul. Ez nem számlázható.

+ A Cognitive Services-t nem hívó szakértelem, beleértve a feltételes, a shaper, a szövegegyesítési és a szövegfelosztási készségeket, nem számlázhatók.

## <a name="same-region-requirement"></a>Azonos régiókövetelmény

Megköveteljük, hogy az Azure Cognitive Search és az Azure Cognitive Services ugyanabban a régióban létezzen. Ellenkező esetben ezt az üzenetet futáskor kapja meg:`"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

A szolgáltatás régiók közötti áthelyezésére nincs mód. Ha ez a hiba jelenik meg, hozzon létre egy új Cognitive Services-erőforrást ugyanabban a régióban, mint az Azure Cognitive Search.

> [!NOTE]
> Egyes beépített képességek nem regionális kognitív szolgáltatásokon alapulnak (például a [szöveges fordítási szakértelemen).](cognitive-search-skill-text-translation.md) Egy nem regionális szakértelem használata azt jelenti, hogy a kérés előfordulhat, hogy az Azure Cognitive Search régiótól eltérő régióban. További információ a nem regionális szolgáltatások, tekintse meg a [Cognitive Services termék régiónként](https://aka.ms/allinoneregioninfo) i lap.

## <a name="use-free-resources"></a>Szabad források használata

Az AI-bővítési oktatóanyag és a gyorsindítási gyakorlatok végrehajtásához korlátozott, ingyenes feldolgozási lehetőséggel végezheti el a gyorsműveleteket.

Az ingyenes (korlátozott dúsítások) erőforrások indexelőnként napi 20 dokumentumra korlátozódnak. Törölheti, majd újra létrehozhatja az indexelőt a számláló alaphelyzetbe állításához.

1. Nyissa meg az Adatok importálása varázslót:

   ![Az Adatok importálása varázsló megnyitása](media/search-get-started-portal/import-data-cmd.png "Az Adatok importálása varázsló megnyitása")

1. Válasszon adatforrást, és folytassa a **AI-bővítés (nem kötelező) lehetőséget.** A varázsló részletes forgatókönyvét az [Index létrehozása az Azure Portalon](search-get-started-portal.md)című témakörben találja.

1. Bontsa ki **a Cognitive Services csatolása** lehetőséget, majd válassza **az Ingyenes (Korlátozott dúsítások) lehetőséget:**

   ![Cognitive Services kiterjesztett csatolása szakasz](./media/cognitive-search-attach-cognitive-services/attach1.png "Cognitive Services kiterjesztett csatolása szakasz")

1. Most már folytathatja a következő lépésekkel, beleértve **a Kognitív képességek hozzáadása**.

## <a name="use-billable-resources"></a>Számlázható erőforrások használata

A napi 20-nál több bővítést hozó számítási feladatokhoz győződjön meg arról, hogy egy számlázható Cognitive Services-erőforrást csatol. Azt javasoljuk, hogy mindig csatolja a számlázható Cognitive Services-erőforrás, akkor is, ha soha nem kívánja felhívni a Cognitive Services API-k. Az erőforrás csatolása felülbírálja a napi korlátot.

Csak a Cognitive Services API-kat hívó képességekért kell fizetnie. Nem kell egyéni [képességekért](cognitive-search-create-custom-skill-example.md), vagy olyan készségekért fizetnie, mint [a szövegegyesítés,](cognitive-search-skill-textmerger.md) [a szövegosztó](cognitive-search-skill-textsplit.md)és [a formázó](cognitive-search-skill-shaper.md), amelyek nem API-alapúak.

1. Nyissa meg az Adatok importálása varázslót, válasszon adatforrást, és folytassa **az AI-bővítés (Nem kötelező) lehetőséggel.**

1. Bontsa ki **a Cognitive Services csatolása** lehetőséget, majd válassza **az Új Cognitive Services-erőforrás létrehozása**lehetőséget. Megnyílik egy új lap, amelyleaz erőforrást hozhatja létre:

   ![Cognitive Services-erőforrás létrehozása](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Cognitive Services-erőforrás létrehozása")

1. A **Hely** listában válassza ki azt a régiót, ahol az Azure Cognitive Search szolgáltatás található. Győződjön meg arról, hogy ezt a régiót teljesítménybeli okokból használja. A régió használata is érvényteleníti a kimenő sávszélesség-díjak régiók között.

1. A **díjszabási szint** listában válassza az **S0** lehetőséget a Cognitive Services-funkciók all-in-one gyűjteményének leküzdéséhez, beleértve a Vision és language funkciókat, amelyek az Azure Cognitive Search által biztosított beépített képességeket támasztják alá.

   Az S0 szint esetében a [Cognitive Services díjszabási lapján](https://azure.microsoft.com/pricing/details/cognitive-services/)megtalálhatja az adott számítási feladatok díjait.
  
   + Az **Ajánlat kiválasztása** listában győződjön meg arról, hogy a **Cognitive Services** ki van jelölve.
   + A **Nyelvi** funkciók csoportban a **Text Analytics Standard** díjai vonatkoznak az AI indexelésre.
   + A **Vision** funkciók alatt a **Computer Vision S1** díjai érvényesek.

1. Válassza **a Létrehozás** lehetőséget az új Cognitive Services-erőforrás kiépítéséhez.

1. Visszatérés az előző lapra, amely az Adatok importálása varázslót tartalmazza. Válassza a **Frissítés** lehetőséget a Cognitive Services-erőforrás megjelenítéséhez, majd válassza ki az erőforrást:

   ![Válassza ki a Cognitive Services erőforrást](./media/cognitive-search-attach-cognitive-services/attach2.png "Válassza ki a Cognitive Services erőforrást")

1. A **Kognitív képességek hozzáadása** szakaszban válassza ki az adatokon futtatni kívánt kognitív képességeket. Töltse ki a varázsló többi részét.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Meglévő skillset csatolása Cognitive Services-erőforráshoz

Ha rendelkezik egy meglévő skillset, csatolhatja egy új vagy egy másik Cognitive Services-erőforráshoz.

1. A **Szolgáltatás áttekintése** lapon válassza a **Skillsets**lehetőséget:

   ![Skillsets lap](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Skillsets lap")

1. Válassza ki a skillset nevét, majd jelöljön ki egy meglévő erőforrást, vagy hozzon létre egy újat. A módosítások megerősítéséhez válassza az **OK gombot.**

   ![Skillset erőforráslista](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Skillset erőforráslista")

   Ne feledje, hogy az **ingyenes (korlátozott dúsítások)** beállítás naponta 20 dokumentumra korlátozza, és hogy **új Cognitive Services-erőforrás létrehozása** segítségével új számlázható erőforrást hozhat létre. Ha új erőforrást hoz létre, válassza a **Frissítés** lehetőséget a Cognitive Services-erőforrások listájának frissítéséhez, majd válassza ki az erőforrást.

## <a name="attach-cognitive-services-programmatically"></a>Cognitive Services csatolása programozott módon

A skillset programozási meghatározásakor adjon hozzá `cognitiveServices` egy szakaszt a skillsethez. Ebben a szakaszban adja meg a cognitive Services-erőforrás kulcsát, amelyet a skillsethez kíván társítani. Ne feledje, hogy az erőforrásnak ugyanabban a régióban kell lennie, mint az Azure Cognitive Search erőforrásnak. Is, `@odata.type`és állítsa `#Microsoft.Azure.Search.CognitiveServicesByKey`a .

A következő példa ezt a mintát mutatja be. Figyelje `cognitiveServices` meg a szakasz végén a meghatározás.

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

## <a name="example-estimate-costs"></a>Példa: Becsült költségek

A kognitív keresési indexeléssel kapcsolatos költségek becsléséhez kezdje egy ötlettel, hogy hogyan néz ki egy átlagos dokumentum, így futtathat bizonyos számokat. Például a következőket közelítheti meg:

+ 1000 PDF.
+ Fejenként hat oldal.
+ Oldalanként egy kép (6000 kép).
+ 3000 karakter oldalanként.

Tegyük fel, hogy egy folyamat, amely a dokumentum repedés minden PDF, kép és szöveg kinyerése, optikai karakterfelismerés (OCR) a képek, és az entitás felismerése szervezetek.

Az ebben a cikkben feltüntetett árak hipotetikusak. A becslési folyamat szemléltetésére szolgálnak. A költségei alacsonyabbak is lehetnek. A tranzakciók tényleges árait lásd: [Cognitive Services-díjszabás.](https://azure.microsoft.com/pricing/details/cognitive-services)

1. A szöveg- és képtartalommal rendelkező dokumentumok feltöréséhez a szöveg kinyerése jelenleg ingyenes. 6000 kép esetén minden kivont 1000 képután 1 $-t kell feltételezni. Ez a költsége 6,00 $ erre a lépésre.

2. A 6000 angol nyelvű OCR esetében az OCR kognitív képességek a legjobb algoritmust (DescribeText) használják. Feltételezve, hogy a költsége 2,50 $ / 1000 kép kell elemezni, akkor fizet $ 15,00 ezt a lépést.

3. Entitás kinyeréséhez oldalanként összesen három szöveges rekord van. Minden rekord 1000 karakterből áll. Oldalanként három szövegrekord és 6000 oldal szorzata 18 000 szöveges rekordnak felel meg. Feltételezve, hogy 1000 szöveges rekordonként 2,00 USD, ez a lépés 36,00 USD-be kerülne.

Mindent összevetve, akkor fizetne körülbelül 57,00 $ betöltése 1000 PDF dokumentumok ilyen típusú a leírt skillset.

## <a name="next-steps"></a>További lépések
+ [Az Azure Cognitive Search díjszabási lapja](https://azure.microsoft.com/pricing/details/search/)
+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)
+ [Szakértelemkészlet létrehozása (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Bővített mezők leképezése](cognitive-search-output-field-mapping.md)
