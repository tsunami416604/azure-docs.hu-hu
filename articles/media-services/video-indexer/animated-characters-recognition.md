---
title: Animált karakterek észlelése Video Indexer
titleSuffix: Azure Media Services
description: Ez a témakör bemutatja, hogyan használható az animált karakterfelismerés a Video Indexer használatával.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: af608dcfbb5d98cf3116de4e14dc12bf6facb97b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76989909"
---
# <a name="animated-character-detection-preview"></a>Animált karakterfelismerés (előzetes verzió)

A Azure Media Services Video Indexer támogatja az animált tartalomban lévő karakterek észlelését, csoportosítását és felismerését [Cognitive Services egyéni jövőképtel](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)való integráció révén. Ez a funkció a Portálon és az API-n keresztül is elérhető.

Egy adott animációs modellel rendelkező animált videó feltöltése után Video Indexer Kinyeri a képkockákat, észleli az animált karaktereket ezekben a keretekben, a hasonló karaktereket csoportosítja, és kiválasztja a legjobb mintát. Ezt követően elküldi a csoportosított karaktereket a Custom Visionnak, amelyek a betanított modellek alapján azonosítják a karaktereket. 

A modell képzésének megkezdése előtt a rendszer a karaktereket észleli namelessly. A nevek hozzáadása és a modell betanítása során a Video Indexer felismeri a karaktereket, és ennek megfelelően nevezi el őket.

## <a name="flow-diagram"></a>Folyamatábra

Az alábbi ábra az animált karakterfelismerési folyamat folyamatát mutatja be.

![Folyamatábra](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Fiókok

A Video Indexer-fiók típusától függően különböző szolgáltatáskészlet-készletek érhetők el. A fiók Azure-hoz való csatlakoztatásával kapcsolatos információkért lásd: az [Azure-hoz csatlakoztatott video Indexer-fiók létrehozása](connect-to-azure.md).

* Próbaverziós fiók: Video Indexer belső Custom Vision fiókot használ a modell létrehozásához és a Video Indexer-fiókhoz való kapcsolódáshoz. 
* Fizetős fiók: a Custom Vision fiókját a Video Indexer-fiókjához kapcsolja (ha még nem rendelkezik ilyennel, először létre kell hoznia egy fiókot).

### <a name="trial-vs-paid"></a>Próbaverzió és fizetett

|Funkció|Próbaverzió|Fizetős|
|---|---|---|
|Custom Vision fiók|A színfalak mögött a Video Indexer által felügyelt. |A Custom Vision-fiókja Video Indexerhoz van csatlakoztatva.|
|Animációs modellek száma|Eggyel|Akár 100 modell/fiók (Custom Vision korlátozás).|
|A modell betanítása|A Video Indexer a modellt a meglévő karakterek további példáit képező új karakterekre.|A fiók tulajdonosa betanítja a modellt, amikor készen állnak a módosítások elvégzésére.|
|Speciális beállítások a Custom Vision|Nincs hozzáférése a Custom Vision portálhoz.|A modelleket saját kezűleg is módosíthatja a Custom Vision-portálon.|

## <a name="use-the-animated-character-detection-with-portal"></a>Az animált karakterek észlelése a portál használatával 

Ez a szakasz azokat a lépéseket ismerteti, amelyeket az animált karakterfelismerési modell használatának megkezdéséhez kell végrehajtania. 

Mivel a próbaverziós fiókokban a Custom Vision-integrációt Video Indexer felügyeli, megkezdheti az animált karakterek létrehozását és használatát, és kihagyhatja a következő szakaszt ("a Custom Vision-fiók összekapcsolásával").

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Custom Vision-fiók összekötése (csak fizetett fiókok esetében)

Ha Video Indexer fizetős fiókkal rendelkezik, először egy Custom Vision-fiókot kell összekötnie. Ha még nincs Custom Vision fiókja, hozzon létre egyet. További információ: [Custom Vision](../../cognitive-services/custom-vision-service/home.md).

> [!NOTE]
> Mindkét fióknak ugyanabban a régióban kell lennie. A Custom Vision integráció jelenleg nem támogatott a japán régióban.

#### <a name="connect-a-custom-vision-account-with-api"></a>Custom Vision-fiók összekötése API-val 

Az alábbi lépéseket követve összekapcsolhatja Custom Vision fiókját Video Indexerhoz, vagy módosíthatja a jelenleg a Video Indexerhoz csatlakozó Custom Vision fiókot:

1. Tallózással keresse meg a [www.customvision.ai](https://www.customvision.ai) , és jelentkezzen be.
1. Másolja a következő kulcsokat: 

    * Betanítási kulcs (a betanítási erőforráshoz)
    * Előrejelzési kulcs (az előrejelzési erőforráshoz)
    * Végpont 
    * Előrejelzési erőforrás azonosítója
    
    > [!NOTE]
    > Az összes kulcs megadásához két különálló erőforrásra van szükség a Custom Visionban, egyet a betanításhoz, egyet pedig az előrejelzéshez.
1. Tallózással keresse meg és jelentkezzen be a [video Indexerba](https://vi.microsoft.com/).
1. Kattintson a lap jobb felső sarkában látható kérdőjelre, és válassza az API- **hivatkozás**lehetőséget.
1. Győződjön meg arról, hogy előfizetett API Management a **Products (termékek** ) lapra kattintva. Ha van egy API-val csatlakoztatva, folytassa a következő lépéssel, ellenkező esetben: előfizetés. 
1. A fejlesztői portálon kattintson a **teljes API-referenciára** , és keresse meg a **műveleteket**.  
1. Válassza a **kapcsolat Custom Vision fiók (előzetes verzió)** lehetőséget, majd kattintson a **kipróbálás**gombra.
1. Töltse ki a kötelező mezőket, valamint a hozzáférési jogkivonatot, és kattintson a **Küldés**gombra. 

    A Video Indexer hozzáférési token beszerzésével kapcsolatos további információkért látogasson el a [fejlesztői portálra](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?), és tekintse meg a [kapcsolódó dokumentációt](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api).  
1. Ha a hívás visszaadja a 200 OK választ, a fiókja csatlakoztatva van.
1. A kapcsolódás ellenőrzéséhez keresse fel a [video Indexer](https://vi.microsoft.com/)) portált:
1. Kattintson a **tartalmi modell testreszabása** gombra a jobb felső sarokban.
1. Nyissa meg az **animált karakterek** lapot.
1. Ha a Custom Vision "* * modellben a modellek kezelése lehetőségre kattint, a rendszer átviszi az imént csatlakoztatott Custom Vision-fiókba.

> [!NOTE]
> Jelenleg csak a Video Indexer használatával létrehozott modellek támogatottak. A Custom Vision használatával létrehozott modellek nem lesznek elérhetők. Emellett az ajánlott eljárás az, ha a Video Indexeron keresztül létrehozott modelleket csak az Video Indexer platformon keresztül hozta létre, mert a Custom Visionon végrehajtott módosítások nem szándékolt eredményeket eredményezhetnek.

### <a name="create-an-animated-characters-model"></a>Animált karakteres modell létrehozása

1. Nyissa meg a [Video Indexer](https://vi.microsoft.com/) webhelyét, és jelentkezzen be.
1. Kattintson a tartalmi modell testreszabása gombra a lap jobb felső sarkában.

    ![Tartalmi modell testreszabása](./media/animated-characters-recognition/content-model-customization.png)
1. Nyissa meg a modell testreszabása szakasz **animált karakterek** lapját.
1. Kattintson a **modell hozzáadása**lehetőségre.
1. Adja meg a modell nevét, majd kattintson az ENTER gombra a név mentéséhez.

> [!NOTE]
> Az ajánlott eljárás az, hogy az egyes animált sorozatokhoz egyetlen egyéni víziós modellt lehessen létrehozni. 

### <a name="index-a-video-with-an-animated-model"></a>Videó indexelése animált modellel

1. A felső menüben kattintson a **feltöltés** gombra.
1. Válassza ki a feltölteni kívánt videót (fájlból vagy URL-címről).
1. Kattintson a **Speciális beállítások**elemre.
1. Az **emberek/animált karakterek** területen válassza az **animációs modellek**lehetőséget.
1. Ha az egyik modellt választja, a rendszer automatikusan kiválasztja, és ha több modellel rendelkezik, kiválaszthatja a legördülő menü megfelelő részét.
1. Kattintson a feltöltés gombra.
1. A videó indexelése után a **rendszer az** észlelt karaktereket az észlelések ablaktábla **animált karakterek** szakaszában tekinti meg.

> [!NOTE] 
> A modell címkézése és betanítása előtt az összes animált karakter neve "Unknown #X" lesz. A modell betanítása után a rendszer felismeri a modellt is.

### <a name="customize-the-animated-characters-models"></a>Az animált karakterek modelljeinek testreszabása

1. A modell címkézése és betanítása.

    1. A név szerkesztésével címkézze fel az észlelt karaktert. Ha egy karaktert betanítanak a modellbe, azt a rendszer a következő, a modellel indexelt videót fogja felismerni. 
    1. Ha egy animált karaktert szeretne címkézni a videóban, lépjen az **elemzések lapra,** és kattintson az ablak jobb felső sarkában található **Szerkesztés** gombra.
    1. Az **észlelések ablaktáblán** kattintson bármelyik észlelt animált karakterre, és módosítsa a nevüket "ismeretlen #X" (vagy a karakterhez korábban hozzárendelt név).
    1. Az új név beírása után kattintson az új név melletti pipa ikonra. Ezzel az új nevet a modellben menti Video Indexerban.
    1. Miután befejezte az összes kívánt név szerkesztését, be kell tanítania a modellt.

        Nyissa meg a Testreszabás lapot, és kattintson az **animált karakterek** fülre, majd kattintson a **vonat** gombra a modell betanításához.
         
        Ha díjköteles fiókkal rendelkezik, a **modellek kezelése a Customer látási** kapcsolaton lehetőségre kattintva (az alább látható módon). Ezután a modell oldalára kerül a **Custom Vision**.
 
        ![Tartalmi modell testreszabása](./media/animated-characters-recognition/content-model-customization-tab.png)

     1. A betanítást követően minden, a modellel indexelt vagy újraindexelt videó felismeri majd a betanított karaktereket. 
    A Custom Vision fiókjához hozzáféréssel rendelkező fizetős fiókok a modelleket és a címkézett képeket is láthatják. További információ az [osztályozó javítása Custom Visionban](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier).

1. Animált karakter törlése.

    1. Ha törölni szeretne egy animált karaktert a videójában, lépjen az elemzések lapra **,** és kattintson az ablak jobb felső sarkában található **Szerkesztés** gombra.
    1. Válassza ki az animált karaktert, majd kattintson a név alatt található **Törlés** gombra.

    > [!NOTE]
    > Ezzel törli az elemzést ebből a videóból, de a modellre nem lesz hatással.

1. Modell törlése.

    1. A felső menüben kattintson a **tartalmi modell testreszabása** gombra, és válassza az **animált karakterek** lapot.
    1. Kattintson a törölni kívánt modell jobb oldalán a három pontot ábrázoló ikonra, majd a Törlés gombra.
    
    * Fizetős fiók: a modell leválasztása Video Indexer, és nem fogja tudni újracsatlakozni.
    * Próbaverziós fiók: a modellt a rendszer a vámügyi jövőképből is törli. 
    
        > [!NOTE]
        > Egy próbaverziós fiókban csak egy modell használható. A törlés után más modelleket nem lehet betanítani.

## <a name="use-the-animated-character-detection-with-api"></a>Az animált karakterfelismerés használata API-val 

1. Custom Vision-fiók összekötése.

    Ha Video Indexer fizetős fiókkal rendelkezik, először egy Custom Vision-fiókot kell összekötnie. <br/>
    Ha még nincs Custom Vision fiókja, hozzon létre egyet. További információ: [Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home).

    [Custom Vision-fiókjának összekapcsolásához használja az API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag)-t.
1. Hozzon létre egy animált karakterekből álló modellt.

    Használja az [animációs modell létrehozása](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag) API-t.
1. Egy videó indexelése vagy újraindexelése.

    Használja az [újbóli indexelés](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) API-t. 
1. Testreszabhatja az animált karakterek modelljeit.

    Használja a [Train Animation Model](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag) API-t.

### <a name="view-the-output"></a>A kimenet megtekintése

Tekintse meg az animált karaktereket a generált JSON-fájlban.

```json
"animatedCharacters": [
    {
    "videoId": "e867214582",
    "confidence": 0,
    "thumbnailId": "00000000-0000-0000-0000-000000000000",
    "seenDuration": 201.5,
    "seenDurationRatio": 0.3175,
    "isKnownCharacter": true,
    "id": 4,
    "name": "Bunny",
    "appearances": [
        {
            "startTime": "0:00:52.333",
            "endTime": "0:02:02.6",
            "startSeconds": 52.3,
            "endSeconds": 122.6
        },
        {
            "startTime": "0:02:40.633",
            "endTime": "0:03:16.6",
            "startSeconds": 160.6,
            "endSeconds": 196.6
        },
    ]
    },
]
```

## <a name="limitations"></a>Korlátozások

* Jelenleg az "animációs azonosítás" képesség nem támogatott a kelet-ázsiai régióban.
* A videóban kis vagy nagy méretű karakterek nem azonosíthatók megfelelően, ha a videó minősége gyenge.
* Javasoljuk, hogy az animált karakterek (például egy animált sorozat) esetében használjon modellt.

## <a name="next-steps"></a>További lépések

[A Video Indexer áttekintése](video-indexer-overview.md)
