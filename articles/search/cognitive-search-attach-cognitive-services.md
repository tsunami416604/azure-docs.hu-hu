---
title: Cognitive Services csatolása egy készségkészlet
titleSuffix: Azure Cognitive Search
description: Útmutató Cognitive Services összes-az-egyhez előfizetéshez az Azure Cognitive Search AI-bővítési folyamatához.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 254c912114e3f1c7a495f389bc6a6416cbde7e11
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472451"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Cognitive Services-erőforrás csatlakoztatása egy készségkészlet az Azure-ban Cognitive Search 

A dúsítási folyamat Azure-Cognitive Search való konfigurálásakor a korlátozott számú dokumentum díjmentesen bővíthető. Nagyobb és gyakoribb munkaterhelések esetén számlázható Cognitive Services-erőforrást kell csatolni.

Ebből a cikkből megtudhatja, hogyan csatolhat egy erőforrást úgy, hogy hozzárendel egy kulcsot egy olyan készségkészlet, amely a dúsítási folyamatot definiálja.

## <a name="resources-used-during-enrichment"></a>A dúsítás során használt erőforrások

Az Azure Cognitive Search függ a Cognitive Servicestől, beleértve a képelemzéshez és az optikai karakterfelismeréshez (OCR), a természetes nyelvi feldolgozáshoz [text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) , valamint más, például a [szöveg fordításához](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)szükséges bővítésekhez [Computer Vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/) . Az Azure Cognitive Search-bővítés kontextusában ezeket az AI-algoritmusokat egy *készségkészlet*helyezi el *, és*az indexelés során egy *Indexelő* hivatkozik rá.

## <a name="how-billing-works"></a>A számlázás működése

+ Az Azure Cognitive Search a készségkészlet megadott Cognitive Services erőforrás-kulcsot használja a képek és a szöveg gazdagítása érdekében. A számlázható képességek végrehajtása az [Cognitive Services utólagos elszámolású áron](https://azure.microsoft.com/pricing/details/cognitive-services/)érhető el.

+ A képek kinyerése egy Azure Cognitive Search művelet, amely akkor fordul elő, ha a rendszer a dúsítás előtt megrepedt a dokumentumokat. A rendszerkép kibontása számlázható. A képek kibontásával kapcsolatos díjszabásért tekintse meg az [Azure Cognitive Search díjszabását ismertető oldalt](https://go.microsoft.com/fwlink/?linkid=2042400).

+ A szöveg kibontása a dokumentum csinos kifejezésében is előfordul. Nem számlázható.

+ A Cognitive Servicest nem meghívó készségek, beleértve a feltételes, a formázó, a szöveges egyesítés és a szöveg felosztása, nem számlázható.

## <a name="same-region-requirement"></a>Azonos régióra vonatkozó követelmény

Az Azure Cognitive Search és az Azure Cognitive Services ugyanazon a régión belül kell lennie. Ellenkező esetben az üzenetet a következő futtatáskor kapja meg: `"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

Egy szolgáltatás nem helyezhető át a régiók között. Ha ezt a hibaüzenetet kapja, hozzon létre egy új Cognitive Services erőforrást ugyanabban a régióban, mint az Azure Cognitive Search.

> [!NOTE]
> Néhány beépített képesség nem regionális Cognitive Services alapul (például a [szöveges fordítási képességre](cognitive-search-skill-text-translation.md)). A nem regionális képesség azt jelenti, hogy a kérést az Azure Cognitive Search régiójától eltérő régióban lehet kiszolgálni. További információ a nem regionális szolgáltatásokról: [Cognitive Services termékek régiónként](https://aka.ms/allinoneregioninfo) oldal.

## <a name="use-free-resources"></a>Ingyenes erőforrások használata

A mesterséges intelligenciával kapcsolatos oktatóanyag és a gyors üzembe helyezési feladatok elvégzéséhez korlátozott, ingyenes feldolgozási lehetőség is használható.

Az ingyenes (korlátozott dúsítású) erőforrások napi 20 dokumentumra korlátozódnak. Az indexelő törlésével és újbóli létrehozásával visszaállíthatja a számlálót.

1. Nyissa meg az adatimportálás varázslót:

   ![Az adatimportálás varázsló megnyitása](media/search-get-started-portal/import-data-cmd.png "Az adatimportálás varázsló megnyitása")

1. Válasszon ki egy adatforrást, és folytassa a **AI-bővítés hozzáadásával (opcionális)** . A varázsló lépésenkénti útmutatója: [index létrehozása a Azure Portalban](search-get-started-portal.md).

1. Bontsa ki a **csatolás Cognitive Services** elemet, majd válassza az **ingyenes (korlátozott dúsítások)** lehetőséget:

   ![Kibontott csatolás Cognitive Services szakasz](./media/cognitive-search-attach-cognitive-services/attach1.png "Kibontott csatolás Cognitive Services szakasz")

1. Most már folytathatja a következő lépéseket, beleértve a **kognitív képességek hozzáadását**is.

## <a name="use-billable-resources"></a>Számlázható erőforrások használata

A napi 20-nál több frissítést létrehozó munkaterhelések esetében ügyeljen arra, hogy számlázható Cognitive Services erőforrást csatoljon. Azt javasoljuk, hogy mindig csatoljon számlázható Cognitive Services-erőforrást, még akkor is, ha soha nem kívánja hívni a Cognitive Services API-k. Egy erőforrás csatolása felülbírálja a napi korlátot.

Csak olyan készségekért kell fizetnie, amelyek meghívja a Cognitive Services API-k. Nem számítunk fel díjat az [Egyéni készségekért](cognitive-search-create-custom-skill-example.md), vagy olyan készségekért, mint a [szöveges egyesítés](cognitive-search-skill-textmerger.md), a [szöveges osztó](cognitive-search-skill-textsplit.md)és a [formáló](cognitive-search-skill-shaper.md), amelyek nem API-alapúak.

1. Nyissa meg az adatimportálás varázslót, válasszon ki egy adatforrást, és folytassa a **AI-bővítés hozzáadásával (opcionális)** .

1. Bontsa ki a **csatolás Cognitive Services** elemet, majd válassza az **új Cognitive Services erőforrás létrehozása**lehetőséget. Megnyílik egy új lap, ahol létrehozhatja az erőforrást:

   ![Cognitive Services erőforrás létrehozása](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Cognitive Services-erőforrás létrehozása")

1. A **hely** listában válassza ki azt a régiót, ahol az Azure Cognitive Search szolgáltatás található. Ügyeljen arra, hogy ezt a régiót használja a teljesítmény szempontjából. Ezen régió használata esetén a kimenő sávszélességgel kapcsolatos díjak is megadhatók a régiók között.

1. A **díjszabási réteg** listában válassza a **S0** lehetőséget a Cognitive Services-funkciók teljes gyűjteményének beszerzéséhez, beleértve az Azure-Cognitive Search által nyújtott beépített készségeket támogató jövőképet és nyelvi funkciókat.

   A S0 szinten a [Cognitive Services díjszabási oldalán](https://azure.microsoft.com/pricing/details/cognitive-services/)találhat meghatározott számítási feladatokra vonatkozó díjszabást.
  
   + Győződjön meg arról, hogy az **ajánlat kiválasztása** listán a **Cognitive Services** van kiválasztva.
   + A **nyelvi** szolgáltatások területen az **text Analytics standard** díjszabása az AI-indexelésre vonatkozik.
   + A **látási** funkciók területen **Computer Vision S1** díjszabása érvényes.

1. Válassza a **Létrehozás** lehetőséget az új Cognitive Services erőforrás kiépítéséhez.

1. Térjen vissza az előző lapra, amely tartalmazza az adatimportálás varázslót. Válassza a **frissítés** lehetőséget a Cognitive Services erőforrás megjelenítéséhez, majd válassza ki az erőforrást:

   ![Cognitive Services erőforrás kiválasztása](./media/cognitive-search-attach-cognitive-services/attach2.png "Cognitive Services erőforrás kiválasztása")

1. Bontsa ki a **kognitív képességek hozzáadása** szakaszt az adatain futtatni kívánt kognitív képességek kiválasztásához. Fejezze be a varázsló hátralévő részét.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Meglévő készségkészlet csatolása Cognitive Services erőforráshoz

Ha rendelkezik meglévő készségkészlet, csatolhatja azt egy új vagy egy másik Cognitive Services erőforráshoz.

1. A **szolgáltatás áttekintése** lapon válassza a **szakértelmével**:

   ![Szakértelmével lap](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Szakértelmével lap")

1. Válassza ki a készségkészlet nevét, majd válasszon ki egy meglévő erőforrást, vagy hozzon létre újat. A módosítások jóváhagyásához kattintson **az OK gombra** .

   ![Készségkészlet-erőforrások listája](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Készségkészlet-erőforrások listája")

   Ne feledje, hogy az **ingyenes (korlátozott dúsítású)** beállítás naponta 20 dokumentumra korlátozza a dokumentumokat, és az új **Cognitive Services erőforrás létrehozása** lehetőséggel új számlázható erőforrást építhet ki. Ha új erőforrást hoz létre, válassza a **frissítés** lehetőséget a Cognitive Services erőforrások listájának frissítéséhez, majd válassza ki az erőforrást.

## <a name="attach-cognitive-services-programmatically"></a>Cognitive Services programozott csatolása

Ha programozott módon határozza meg a készségkészlet, vegyen fel egy `cognitiveServices` szakaszt a készségkészlet. Ebben a szakaszban adja meg a készségkészlet társítandó Cognitive Services erőforrás kulcsát. Ne feledje, hogy az erőforrásnak ugyanabban a régióban kell lennie, mint az Azure Cognitive Search erőforrásnak. Adja meg a `@odata.type`is, és állítsa be `#Microsoft.Azure.Search.CognitiveServicesByKey`re.

A következő példa ezt a mintát mutatja be. Figyelje meg a definíció végén található `cognitiveServices` szakaszt.

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

## <a name="example-estimate-costs"></a>Példa: becsült költségek

A kognitív keresési indexeléshez kapcsolódó költségek kiszámításához Kezdje azzal, hogy egy átlagos dokumentum hogyan néz ki, így néhány számot futtathat. Például megközelítheti a következőket:

+ 1 000 PDF-fájlok.
+ Hat oldal.
+ Oldalanként egy kép (6 000 lemezkép).
+ 3 000 karakter/oldal.

Tegyük fel, hogy egy olyan folyamat, amely az egyes PDF-fájlokból, képekből és szöveg-kivonásokból, optikai karakterfelismerésből (OCR) és a szervezetek entitás-felismerésével kapcsolatos dokumentumok repedését tartalmazza.

A cikkben szereplő díjak feltételezettek. A becslési folyamat szemléltetésére szolgálnak. A költségek alacsonyabbak lehetnek. A tranzakciók tényleges áraival kapcsolatban lásd: [Cognitive Services díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Szöveg-és képtartalommal való repedés esetén a szöveg kibontása jelenleg ingyenes. 6 000-lemezkép esetén tegyük fel, hogy $1 minden kinyert 1 000-lemezképhez. Ez a lépés a $6,00-as díj.

2. Az 6 000-es rendszerképek optikai KARAKTERFELISMERÉSi képességei a legjobb algoritmust használják (DescribeText). Ennek a lépésnek a $2,50/1 000 rendszerképekkel való elemzését feltételezve kell $15,00 fizetnie.

3. Az entitások kinyeréséhez oldalanként összesen három szöveges rekord van. Minden rekord 1 000 karakterből áll. Oldalanként három szöveges rekord, szorozva az 6 000-lapokkal, 18 000 szöveges rekordokkal. Az $2,00-as 1 000-es szöveges rekordok esetében ennek a lépésnek a díja $36,00.

Mindezt együttesen a $57,00-es számú 1 000-es PDF-dokumentumot kell fizetnie a leírt készségkészlet.

## <a name="next-steps"></a>Következő lépések
+ [Az Azure Cognitive Search díjszabási oldala](https://azure.microsoft.com/pricing/details/search/)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Készségkészlet létrehozása (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [A dúsított mezők leképezése](cognitive-search-output-field-mapping.md)
