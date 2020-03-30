---
title: Animált karakterfelismerés a Video Indexelővel
titleSuffix: Azure Media Services
description: Ez a témakör bemutatja, hogyan használható animált karakterfelismerés a Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: af608dcfbb5d98cf3116de4e14dc12bf6facb97b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989909"
---
# <a name="animated-character-detection-preview"></a>Animált karakterfelismerés (előnézet)

Az Azure Media Services Video Indexelő támogatja az animált tartalmakban lévő karakterek észlelését, csoportosítását és elismerését a [Cognitive Services egyéni jövőképpel](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)való integrációja révén. Ez a funkció elérhető mind a portálon, mind az API-n keresztül.

Egy adott animációs modellel rendelkező animált videó feltöltése után a Video Indexer kinyeri a kulcsképeket, észleli az animált karaktereket ezekben a képkockákban, csoportosítja a hasonló karaktereket, és kiválasztja a legjobb mintát. Ezután elküldi a csoportosított karaktereket a Custom Vision programba, amely azonosítja a karaktereket a betanított modellek alapján. 

A modell betanítása előtt a rendszer névtelenül észleli a karaktereket. Nevek hozzáadásakor és a modell betanításakor a Video Indexer felismeri a karaktereket, és ennek megfelelően elnevezi őket.

## <a name="flow-diagram"></a>Folyamatábra

Az alábbi ábra az animált karakterészlelési folyamat folyamatát mutatja be.

![Folyamatábra](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Fiókok

A Video Indexer-fiók típusától függően különböző szolgáltatáskészletek érhetők el. A fiók Azure-hoz való csatlakoztatásáról az [Azure-hoz csatlakoztatott VideoIndexer-fiók létrehozása című témakörben](connect-to-azure.md)talál további információt.

* Próbafiók: A Video Indexer egy belső Egyéni Látás-fiókot használ a modell létrehozásához és a videoindexer-fiókhoz való csatlakoztatásához. 
* Fizetős fiók: egyéni Vision-fiókját csatlakoztatja a Video Indexer-fiókjához (ha még nem rendelkezik ilyenel, először létre kell hoznia egy fiókot).

### <a name="trial-vs-paid"></a>Tárgyalás vs. fizetett

|Funkció|Próbaverzió|Fizetett|
|---|---|---|
|Egyéni Vision-fiók|Kezelt a színfalak mögött a Video Indexer. |Az Egyéni Vision-fiók a Video Indexer hez kapcsolódik.|
|Animációs modellek száma|Eggyel|Fiókonként legfeljebb 100 modell (Egyéni látáskorlátozás).|
|A modell betanítása|A Video Indexer bekéri a modellt az új karakterekhez, és további példákat ad a meglévő karakterekre.|A fiók tulajdonosa beiktatja a modellt, amikor készen állnak a módosításokra.|
|Speciális beállítások az egyéni látásban|Nincs hozzáférés az egyéni látás portálhoz.|A modelleket saját maga is módosíthatja az egyéni látás portálon.|

## <a name="use-the-animated-character-detection-with-portal"></a>Az animált karakterfelismerés használata a portállal 

Ez a szakasz az animált karakterészlelési modell használatának megkezdéséhez szükséges lépéseket ismerteti. 

Mivel a próbaverziós fiókokban a Custom Vision integrációt a Video Indexer kezeli, elkezdheti az animált karakterek modelljének létrehozását és használatát, és kihagyhatja a következő szakaszt ("Az egyéni vision-fiók csatlakoztatása").

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Egyéni Vision-fiók csatlakoztatása (csak fizetős fiókok esetén)

Ha van egy videó indexelő fizetett fiókja, először egyéni vision-fiókot kell csatlakoztatnia. Ha még nem rendelkezik Custom Vision-fiókkal, hozzon létre egyet. További információ: [Custom Vision](../../cognitive-services/custom-vision-service/home.md).

> [!NOTE]
> Mindkét fióknak ugyanabban a régióban kell lennie. A Custom Vision integráció jelenleg nem támogatott a japán régióban.

#### <a name="connect-a-custom-vision-account-with-api"></a>Egyéni Vision-fiók csatlakoztatása API-val 

Az alábbi lépésekkel kapcsolhatja össze egyéni vision-fiókját a Video Indexerhez, vagy módosíthatja a Video Indexerhez jelenleg csatlakoztatott Egyéni látás-fiókot:

1. Tallózással keresse meg [a www.customvision.ai](https://www.customvision.ai) és a bejelentkezést.
1. Másolja a következő kulcsokat: 

    * Képzési kulcs (a képzési erőforráshoz)
    * Előrejelzési kulcs (az előrejelzési erőforráshoz)
    * Végpont 
    * Előrejelzési erőforrás azonosítója
    
    > [!NOTE]
    > Ahhoz, hogy az összes kulcsot meg kell, hogy két külön erőforrást Custom Vision, egy a képzés és egy előrejelzés.
1. Tallózzon a Videoindexelővel, és jelentkezzen be a [Video Indexerbe.](https://vi.microsoft.com/)
1. Kattintson a kérdőjelre az oldal jobb felső sarkában, és válassza az **API-referencia lehetőséget.**
1. Győződjön meg arról, hogy feliratkozott az API Management szolgáltatásra a **Termékek** fülre kattintva. Ha van egy API-csatlakoztatva, akkor továbbra is a következő lépés, egyébként, iratkozzon fel. 
1. A fejlesztői portálon kattintson a **Teljes API-referencia** elemre, és keresse meg az **Operations (Műveletek) gombot.**  
1. Válassza **az Egyéni látásfiók csatlakoztatása (PREVIEW)** lehetőséget, és kattintson **a Kipróbálás gombra.**
1. Töltse ki a szükséges mezőket, valamint a hozzáférési jogkivonatot, és kattintson a **Küldés gombra.** 

    A Video Indexer hozzáférési jogkivonat beszerzéséről további információt a [fejlesztői portálon](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?)talál, és tekintse meg a [megfelelő dokumentációt](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api).  
1. Miután a hívás vissza 200 OK választ, a fiók csatlakozik.
1. A kapcsolat ellenőrzése a [Video Indexer](https://vi.microsoft.com/)) portálon való tallózással:
1. Kattintson a **Tartalommodell testreszabási** gombjára a jobb felső sarokban.
1. Nyissa meg az **Animált karakterek** lapot.
1. Miután rákattintott a Modellek kezelése a Custom Vision"**-ban, átkerül a most csatlakoztatott Custom Vision fiókba.

> [!NOTE]
> Jelenleg csak a Video Indexer segítségével létrehozott modellek támogatottak. A Custom Vision segítségével létrehozott modellek nem lesznek elérhetők. Ezenkívül az ajánlott eljárás a Video Indexer segítségével létrehozott modellek szerkesztése csak a Video Indexer platformon keresztül, mivel az Egyéni látáson keresztül végrehajtott módosítások nem kívánt eredményeket okozhatnak.

### <a name="create-an-animated-characters-model"></a>Animált karaktermodell létrehozása

1. Nyissa meg a [Video Indexer](https://vi.microsoft.com/) webhelyét, és jelentkezzen be.
1. Kattintson a tartalommodell testreszabási gombjára a lap jobb felső sarkában.

    ![Tartalommodell testreszabása](./media/animated-characters-recognition/content-model-customization.png)
1. Nyissa meg az **Animált karakterek** lapot a modell testreszabása szakaszban.
1. Kattintson a **Modell hozzáadása gombra.**
1. A kívánt nevet modellezheti, majd a név mentéséhez kattintson a beírásgombra.

> [!NOTE]
> A legjobb gyakorlat az, hogy egy egyéni látás modell minden animációs sorozat. 

### <a name="index-a-video-with-an-animated-model"></a>Videó indexelése animált modellel

1. Kattintson a **feltöltés** gombra a felső menüben.
1. Válassza ki a feltöltendő videót (fájlból vagy URL-címből).
1. Kattintson a **Speciális beállítások gombra.**
1. A **Kapcsolatok / Animált karakterek csoportban** válassza az Animációs modellek **lehetőséget.**
1. Ha van egy modell, akkor automatikusan kiválasztódik, és ha több modell van, kiválaszthatja a megfelelőt a legördülő menüből.
1. Kattintson a feltöltésre.
1. A videó indexelése után az észlelt karakterek az **Elemzési** adatok ablaktábla **Animált karakterek** szakaszában jelennek meg.

> [!NOTE] 
> A modell címkézése és betanítása előtt az összes animált karakter neve "Ismeretlen #X". A modell betanítása után a rendszer is felismeri őket.

### <a name="customize-the-animated-characters-models"></a>Az animált karakterek modelljeinek testreszabása

1. Címkézze fel és tanítsa be a modellt.

    1. Címkézze fel az észlelt karaktert a nevének szerkesztésével. Miután egy karakter bevan tanítva a modellbe, a rendszer felismeri a következő, ezzel a modellel indexelt videót. 
    1. Animált karakter megjelöléséhez lépjen az **Elemzések** lapra, és kattintson az ablak jobb felső sarkában található **Szerkesztés** gombra.
    1. Az **Elemzések** ablaktáblán kattintson bármelyik észlelt animált karakterre, és módosítsa a nevüket az "Ismeretlen #X" (vagy a karakterhez korábban hozzárendelt név) értéken.
    1. Miután beírta az új nevet, kattintson az új név melletti ellenőrző ikonra. Ezzel menti az új nevet a modell videoindexelő.
    1. Miután befejezte az összes kívánt név szerkesztését, be kell tanítania a modellt.

        Nyissa meg a testreszabási oldalt, és kattintson az **Animált karakterek** fülre, majd kattintson a **Vonat** gombra a modell betanításához.
         
        Ha fizetős fiókkal rendelkezik, kattintson a Modellek kezelése az **Ügyféllátó mezőben** linkre (az alábbiak szerint). Ezután a rendszer továbbítja a modell oldalára a **Custom Vision**programban.
 
        ![Tartalommodell testreszabása](./media/animated-characters-recognition/content-model-customization-tab.png)

     1. Betanítás után minden olyan videó, amely indexelt vagy újraindexelt, hogy a modell felismeri a betanított karaktereket. 
    A fizetős fiókok, amelyek hozzáférnek a Custom Vision-fiókjukhoz, ott láthatják a modelleket és a címkézett képeket. További információ [az osztályozó fejlesztéséről a Custom Vision programban.](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier)

1. Animált karakter törlése.

    1. Ha törölni szeretne egy animált karaktert a videóelemzési adatokból, lépjen az **Elemzések** lapra, és kattintson az ablak jobb felső sarkában található **Szerkesztés** gombra.
    1. Válassza ki az animált karaktert, majd kattintson a **Törlés** gombra a nevük alatt.

    > [!NOTE]
    > Ez törli a betekintést ebből a videóból, de nem befolyásolja a modellt.

1. Modell törlése.

    1. Kattintson a felső menü **Tartalommodell testreszabási** gombjára, és lépjen az **Animált karakterek** lapra.
    1. Kattintson a törölni kívánt modell jobb oldalán található három pont ikonra, majd a törlés gombra.
    
    * Fizetett számla: a modell lelesz választva a Video Indexerről, és nem fogja tudni újra csatlakoztatni.
    * Próbafiók: a modell a vámügyi jövőképből is törlődik. 
    
        > [!NOTE]
        > Próbaverziós fiókban csak egy modellhasználható. A törlés után nem lehet betanítani más modelleket.

## <a name="use-the-animated-character-detection-with-api"></a>Az animált karakterfelismerés használata API-val 

1. Egyéni vision-fiók csatlakoztatása.

    Ha van egy videó indexelő fizetett fiókja, először egyéni vision-fiókot kell csatlakoztatnia. <br/>
    Ha még nem rendelkezik Custom Vision-fiókkal, hozzon létre egyet. További információ: [Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home).

    [Csatlakoztassa egyéni Vision-fiókját az API segítségével.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag)
1. Hozzon létre egy animált karakter modell.

    Használja a create animation model API.Use the [create animation model](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag) API.
1. Videó indexelése vagy újraindexelése.

    Használja az [újraindexelő API-t.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) 
1. Testreszabhatja az animált karakterek modellek.

    Használja a vonat animációs modell API.Use the [train animation model](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag) API.

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

* Az "animáció-azonosítás" funkció jelenleg nem támogatott kelet-ázsiai régióban.
* Előfordulhat, hogy a videóban kicsinek vagy távolinak tűnő karakterek nem azonosíthatók megfelelően, ha a videó minősége gyenge.
* A javaslat az, hogy egy modell tinős edző karakterek (például egy animációs sorozat).

## <a name="next-steps"></a>További lépések

[A Video Indexer áttekintése](video-indexer-overview.md)
