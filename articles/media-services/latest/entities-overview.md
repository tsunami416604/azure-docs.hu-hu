---
title: Az Azure Media Services entitások áttekintése – Azure |} A Microsoft Docs
description: Ez a cikk áttekintést nyújt az Azure Media Services entitások.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/20/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 902014ff3c242a18b3872ba490845eb0923f39a4
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54451187"
---
# <a name="azure-media-services-entities-overview"></a>Az Azure Media Services entitások áttekintése

Ez a cikk rövid áttekintést nyújt az Azure Media Services-entitások és pontok cikk minden entitás további információt. 

| Témakör | Leírás |
|---|---|
| [Fiók és eszköz szűrőket](filters-dynamic-manifest-overview.md)|Az ügyfelek számára (élő eseményeket vagy igény szerinti Videószolgáltatás streaming) tartalomtovábbításkor az ügyfél igényelhet az alapértelmezett eszköz jegyzékfájl leírtnál nagyobb rugalmasságot. Az Azure Media Services lehetővé teszi, hogy meghatározza [Fiókszűrők](https://docs.microsoft.com/rest/api/media/accountfilters) és [eszköz szűrők](https://docs.microsoft.com/rest/api/media/assetfilters). Ezután használja **dinamikus jegyzékfájlok** az előre definiált szűrők alapján. |
| [Eszközök](assets-concept.md)|Egy [eszköz](https://docs.microsoft.com/rest/api/media/assets) entitás tartalmazza a digitális fájlok (beleértve a videót, hangot, képeket, miniatűröket, szövegsávok és feliratfájlok), és ezek a fájlok metaadatait. Miután a digitális fájlok feltöltése egy adategységbe, azok a Media Services encoding, streaming, tartalom munkafolyamatok elemzése használható.|
| [Tartalmi kulcs házirendjei](content-key-policy-concept.md)|A Media Services segítségével az az idő, akkor hagyja, hogy a számítógép tárolási, feldolgozási és kézbesítési a médiatartalmak védelmét. A Media Services élő és igény szerinti tartalmait az Advanced Encryption Standard (AES-128) vagy a három fő digitális jogkezelési (technológia DRM) felügyeleti rendszerek bármelyikét dinamikusan titkosított juttathat el: A Microsoft PlayReady, a Google Widevine és az Apple fairplay által. Media Services is biztosít a modult az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licenceket az arra jogosult ügyfelek.|
| [LiveEvents és LiveOutputs](live-events-outputs-concept.md)|Media Services lehetővé teszi, hogy az ügyfeleknek az Azure-felhőben lévő események élő közvetítésére. Az élő események streamelése konfigurálása a Media Services v3-as, ismerje meg kell [élő események](https://docs.microsoft.com/rest/api/media/liveevents) és [élő kimenetek](https://docs.microsoft.com/rest/api/media/liveoutputs).|
| [Streamvégpontok](streaming-endpoint-concept.md)|A [adatfolyam-továbbítási végpontok](https://docs.microsoft.com/rest/api/media/streamingendpoints) entitás jelöli egy adatfolyam-szolgáltatást, amely tartalmat továbbít közvetlenül az ügyfél lejátszóalkalmazásába, vagy az egy Content Delivery Network (CDN) további terjesztés céljából. A folyamatos átviteli végponton szolgáltatásból származó kimenő adatfolyamot élő stream, vagy igény szerinti Videoobjektum a Media Services-fiók lehet. Egy Media Services-fiók létrehozásakor egy **alapértelmezett** folyamatos átviteli végponton, létrehozott egy leállított állapotban. Nem lehet törölni a **alapértelmezett** folyamatos átviteli végponton. További Streamelési végpontokkal is létrehozhatók a fiókban. Videók streamelése indításához kell elindítani a folyamatos átviteli végponton, ahonnan a videó. |
| [A streamelési Lokátorok](streaming-locators-concept.md)|Meg kell adnia az ügyfelek is lejátszására használható URL-kódolású video- vagy fájlokat kell, hogy hozzon létre egy [Streamelési lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators) és a streamelési URL-címeket.|
| [Streamelési házirendek](streaming-policy-concept.md)| [Adatfolyam-házirendek](https://docs.microsoft.com/rest/api/media/streamingpolicies) lehetővé teszi az adatfolyam-továbbítási protokollok és a StreamingLocators titkosítási beállításainak megadása. Adja meg a létrehozott egyéni Streamelési szabályzat nevét, vagy használja a Media Services által kínált előre definiált adatfolyam-házirendek egyikét. <br/><br/>Streamelési egyéni szabályzat használata esetén az ilyen szabályzatok korlátozott számú tervezzen a Media Services-fiókhoz, és újból felhasználja őket a Streamelési Lokátorok, amikor az ugyanazon titkosítási lehetőségeket és a protokollok van szükség. Meg kell nem lehet új szabályzatot hoz létre Streamelési az egyes Streamelési lokátor.|
| [Átalakítások és feladatok](transforms-jobs-concept.md)|Használat [alakítja át](https://docs.microsoft.com/rest/api/media/transforms) és videók elemzése gyakori feladatok konfigurálása. Minden egyes **átalakítása** egy módszereivel, vagy egy munkafolyamatot a video- és audiotartalmak fájlok feldolgozása kapcsolatos feladatokat ismerteti.<br/><br/>A [feladat](https://docs.microsoft.com/rest/api/media/jobs) van az Azure Media Services tényleges kérelem a alkalmazni a **átalakítása** egy adott a bemeneti videó vagy hang tartalomhoz. A **feladat** információkat, például a bemeneti videó helyét, és a kimeneti helyét adja meg. Megadhatja a helyét, a bemeneti videó használatával: HTTPS URL-címek, SAS URL-címeit, vagy egy eszköz.|

## <a name="next-steps"></a>További lépések

[Fájl streamelése](stream-files-dotnet-quickstart.md)
