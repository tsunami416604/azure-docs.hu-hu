---
title: Migrálaz Indexer v1-ről és v2-ről az Azure Media Services videoindexelőjébe | Microsoft dokumentumok
description: Ez a témakör bemutatja, hogyan lehet áttelepíteni az Azure Media Indexer v1 és v2 az Azure Media Services videoindexelő.
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
ms.openlocfilehash: 2268c074480f99ca23117ca2ffd2c87c1dbb10a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513235"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>Áttelepítés a Media Indexer ről és a 2-es médiaindexelőről a Video Indexerre

Az [Azure Media Indexer](media-services-index-content.md) médiaprocesszor és az [Azure Media Indexer 2 előzetes médiaprocesszorok](media-services-process-content-with-indexer2.md) kivonása folyamatban van. A nyugdíjazási dátumokat lásd az [örökölt összetevők](legacy-components.md) témakörben. [Az Azure Media Services videoindexelő](https://docs.microsoft.com/azure/media-services/video-indexer/) jeleváltja ezeket az örökölt médiaprocesszorokat.

Az Azure Media Services videoindexelője az Azure Media Analytics, az Azure Cognitive Search, a Cognitive Services (például a Face API, a Microsoft Translator, a Computer Vision API és az Egyéni beszédszolgáltatás) épül. Lehetővé teszi, hogy megállapításokat nyerjen ki a videóiból a Video Indexer hang- és videómodelljeivel. Ha meg szeretné tekinteni, hogy a Video Indexer milyen forgatókönyvekben használható, milyen szolgáltatásokat kínál, és hogyan kell elkezdeni, olvassa el a [Video Indexer video- és hangmodellek című témakört.](../video-indexer/video-indexer-overview.md) 

Az [Azure Media Services v3-elemzőkészletei](../latest/analyzing-video-audio-files-concept.md) vel vagy közvetlenül a Video [Indexer API-k](https://api-portal.videoindexer.ai/)használatával nyerheti ki a video- és hangfájlokból származó elemzéseket. Jelenleg átfedés van a Video Indexer API-k és a Media Services v3 API-k által kínált funkciók között.

> [!NOTE]
> Ha meg szeretné tudni, hogy mikor szeretné használni a Video Indexer vs. Media Services elemzőkészleteket, tekintse meg az [összehasonlító dokumentumot.](../video-indexer/compare-video-indexer-with-media-services-presets.md) 

Ez a cikk ismerteti az Azure Media Indexer és az Azure Media Indexer 2 az Azure Media Services videoindexelő áttelepítésének lépéseit.  

## <a name="migration-options"></a>Migrálási lehetőségek 

|Ha szükséges,  |akkor |
|---|---|
|olyan megoldás, amely beszédfelismerési átírást biztosít bármilyen médiafájlformátumhoz feliratos fájlformátumban: VTT, SRT vagy TTML<br/>valamint további audio betekintést, mint például: kulcsszavak, téma viszonyítási, akusztikus események, hangszóró diarization, szervezetek kitermelése és fordítása| frissítse alkalmazásait az Azure Video Indexer képességeinek használatára a Video Indexer v2 REST API-n vagy az Azure Media Services v3 Audio Analyzer készleten keresztül.|
|beszéd-szöveg lehetőségek| használja a Cognitive Services beszédfelismerési API-t közvetlenül.|  

## <a name="getting-started-with-video-indexer"></a>A Video Indexer – első lépések

A következő szakasz a releváns hivatkozásokra mutat rá: [Hogyan kezdhetem el a Video Indexer t?](https://docs.microsoft.com/azure/media-services/video-indexer/video-indexer-overview#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>A Media Services 3-as v3 API-jának első lépései

Az Azure Media Services v3 API-ja lehetővé teszi, hogy az [Azure Media Services v3-elemzőkészletein](../latest/analyzing-video-audio-files-concept.md)keresztül kivonatolja ki a video- és hangfájlokból az elemzéseket. 

**Az AudioAnalyzerPreset** lehetővé teszi több hangelemzés kinyerését hang- vagy videofájlból. A kimenet tartalmaz egy VTT vagy TTML fájlt a hangátirathoz és egy JSON fájlt (az összes további audio betekintést). A hangelemzési adatok kulcsszavakat, hangszóróindexelést és beszédhangulat-elemzést tartalmaznak. Az AudioAnalyzerPreset támogatja az adott nyelvek nyelvfelismerését is. További információt az Átalakítások című [témakörben talál.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)

### <a name="get-started"></a>Bevezetés

Első lépések:

* [Bemutató](../latest/analyze-videos-tutorial-with-api.md)
* AudioAnalyzerPreset minták: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) vagy [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* VideoAnalyzerPreset minták: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) vagy [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>A Cognitive Services beszédszolgáltatásokkal való ismerkedés

[Az Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/) beszédfelismerési szolgáltatást biztosít, amely valós időben átírja a hangadatfolyamokat olyan szöveggé, amelyet az alkalmazások, eszközök vagy eszközök használhatnak vagy jeleníthetnek meg. A beszédfelismeréssegítségével [testreszabhatja saját akusztikai modelljét, nyelvi modelljét vagy kiejtési modelljét.](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md) További információ: [Cognitive Services speech-to-text](../../cognitive-services/speech-service/speech-to-text.md). 

> [!NOTE] 
> A beszéd-szöveg szolgáltatás nem fogad videofájlformátumokat, és csak [bizonyos hangformátumokat.](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#audio-formats) 

A szövegfelolvasó szolgáltatásról és az első lépésekről a [Mi a beszédfelismerési szolgáltatás?](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-to-text)

## <a name="known-differences-from-deprecated-services"></a>Ismert különbségek az elavult szolgáltatásokkal szemben 

A Video Indexelő, az Azure Media Services v3 AudioAnalyzerPreset és a Cognitive Services Speech Services-szolgáltatások megbízhatóbbak, és jobb minőségű kimenetet eredményeznek, mint a kivisszavonult Azure Media Indexer 1 és az Azure Media Indexer 2 processzorok.  

Néhány ismert különbség: 

* A Cognitive Services beszédszolgáltatások nem támogatják a kulcsszavak kinyerését. Azonban a Video Indexer és a Media Services v3 AudioAnalyzerPreset egyaránt kínál robusztusabb kulcsszavak at JSON fájlformátumban. 

## <a name="need-help"></a>Segítségre van szüksége?

Támogatási jegyet úgy nyithat meg, hogy az [Új támogatási kérelemre](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) navigál.

## <a name="next-steps"></a>További lépések

* [Örökölt összetevők](legacy-components.md)
* [Díjszabás lap](https://azure.microsoft.com/pricing/details/media-services/#encoding)


