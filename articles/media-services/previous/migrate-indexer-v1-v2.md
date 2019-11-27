---
title: Migrálás az indexelő v1 és v2 rendszerről Azure Media Services Video Indexerra | Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan lehet áttérni Azure Media Indexer v1 és v2 rendszerről Azure Media Services Video Indexerre.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2019
ms.author: juliako
ms.openlocfilehash: 791287d693903007d09c2e82025bfe195f9f15d1
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74464056"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>Migrálás Media Indexer és Media Indexer 2 – Video Indexer

A [Azure Media Indexer](media-services-index-content.md) adathordozó-processzor a 2020-es október 1-én megszűnik. A [Azure Media Indexer 2 előnézeti](media-services-process-content-with-indexer2.md) adathordozó processzora 2020 január 1-től megszűnik.  A [Azure Media Services video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) ezeket a régi adathordozó-processzorokat váltja fel.

A Azure Media Services Video Indexer Azure Media Analytics, Azure Cognitive Search, Cognitive Servicesra épül (például a Face API, a Microsoft Translator, a Computer Vision API és Custom Speech Service). Lehetővé teszi, hogy megállapításokat nyerjen ki a videóiból a Video Indexer hang- és videómodelljeivel. Ha szeretné megtekinteni, hogy milyen forgatókönyvek Video Indexer használhatók a alkalmazásban, milyen funkciókat kínál, és hogyan kezdheti el az első lépéseket, tekintse meg [video Indexer videó-és hangmodelleket](../video-indexer/video-indexer-overview.md). 

A videó-és hangfájlokból kinyerheti az elemzéseket az [Azure Media Services v3 Analyzer-előállítók](../latest/analyzing-video-audio-files-concept.md) használatával, vagy közvetlenül a [video Indexer API](https://api-portal.videoindexer.ai/)-k használatával. Jelenleg a Video Indexer API-k és a Media Services V3 API-k által kínált funkciók átfedésben vannak.

> [!NOTE]
> Ha szeretné megismerni, hogy mikor érdemes használni a Video Indexer vs. Media Services Analyzer-készleteket, tekintse meg az [összehasonlító dokumentumot](../video-indexer/compare-video-indexer-with-media-services-presets.md). 

Ez a cikk az áttelepítésének lépéseit ismerteti a Azure Media Indexer és Azure Media Indexer a 2 Azure Media Services Video Indexer.  

## <a name="migration-options"></a>Áttelepítési beállítások 

|Ha szükséges  |majd |
|---|---|
|egy olyan megoldás, amely beszéd-szöveg átírást biztosít bármilyen médiafájl formátumhoz a kódolt képfájlok formátumában: VTT, SRT vagy TTML<br/>továbbá további hangelemzéseket, például a kulcsszavakat, a témaköröket, az akusztikus eseményeket, a hangszórók diarization, az entitások kinyerését és fordítását.| frissítse alkalmazásait az Azure Video Indexer képességeinek az Video Indexer v2 REST API vagy a Azure Media Services v3 audio Analyzer-készlettel való használatához.|
|beszéd és szöveg közötti képességek| használja közvetlenül a Cognitive Services Speech API-t.|  

## <a name="getting-started-with-video-indexer"></a>A Video Indexer első lépései

A következő szakasz a kapcsolódó hivatkozásokra mutat: Hogyan szerezhetem be a [video Indexer?](https://docs.microsoft.com/azure/media-services/video-indexer/video-indexer-overview#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>Media Services V3 API-k – első lépések

A Azure Media Services V3 API lehetővé teszi, hogy kinyerje a videóból és hangfájlokból származó elemzéseket a [Azure Media Services v3 Analyzer-előkészletből](../latest/analyzing-video-audio-files-concept.md). 

A **AudioAnalyzerPreset** lehetővé teszi több hang-elemzés kinyerését hang-vagy videofájl használatával. A kimenet tartalmaz egy VTT vagy TTML-fájlt a hangátirathoz és egy JSON-fájlhoz (az összes további hangelemzéssel). A hangelemzések közé tartoznak a kulcsszavak, a hangszórók indexelése és a beszéd hangulatának elemzése. A AudioAnalyzerPreset az egyes nyelvekhez is támogatja a nyelvfelismerés használatát. Részletes információk: [átalakítások](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset).

### <a name="get-started"></a>Első lépések

Első lépésként tekintse meg a következőt:

* [Oktatóanyag](../latest/analyze-videos-tutorial-with-api.md)
* AudioAnalyzerPreset-minták: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) vagy [.net SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* VideoAnalyzerPreset-minták: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) vagy [.net SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Cognitive Services Speech Services – első lépések

Az [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/) egy beszéd-szöveges szolgáltatást biztosít, amely valós időben írja át a hangadatfolyamokat a szöveggé, hogy alkalmazásai, eszközei vagy eszközei képesek legyenek a felhasználásra vagy a megjelenítésre. [A saját akusztikai modell, a nyelvi modell vagy a kiejtési modell testre szabására](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md)használhatja a beszédfelismerést a szövegre. További információ: [Cognitive Services beszéd – szöveg](../../cognitive-services/speech-service/speech-to-text.md). 

> [!NOTE] 
> A beszédfelismerési szolgáltatás nem készíti el a videofájl formátumait, és csak [bizonyos hangformátumokat](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#audio-formats)fogad el. 

További információ a szöveg-beszéd szolgáltatásról és az első lépésekről: [Mi az a beszéd – szöveg?](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-to-text)

## <a name="known-differences-from-deprecated-services"></a>Elavult szolgáltatások ismert eltérései 

Azt tapasztalja, hogy Video Indexer, Azure Media Services v3 AudioAnalyzerPreset, és Cognitive Services a Speech Services-szolgáltatások megbízhatóbbak, és jobb minőségű kimenetet eredményeznek, mint a kivont Azure Media Indexer 1 és Azure Media Indexer 2 processzor.  

Néhány ismert különbség: 

* Cognitive Services Speech Services nem támogatja a kulcsszavak kinyerését. A Video Indexer és a Media Services v3 AudioAnalyzerPreset azonban a kulcsszavakat a JSON-fájlformátumban is hatékonyabban kínálja. 

## <a name="need-help"></a>Segítségre van szüksége?

A támogatási jegy megnyitásához lépjen az [új támogatási kérelemre](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) .

## <a name="next-steps"></a>Következő lépések

* [Örökölt összetevők](legacy-components.md)
* [Díjszabási oldal](https://azure.microsoft.com/pricing/details/media-services/#encoding)


