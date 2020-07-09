---
title: Magas rendelkezésre állású streaming Azure Media Services
description: Ismerje meg, hogyan hajthat végre feladatátvételt másodlagos Media Services-fiókra, ha regionális adatközpont-leállás vagy-meghibásodás történik.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: 1492dd392eabc4331f8e3d4604fb245a89dedff5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "78899229"
---
# <a name="media-services-high-availability-streaming"></a>Magas rendelkezésre állású Streaming Media Services

Azure Media Services jelenleg nem biztosítja a szolgáltatás azonnali feladatátvételét, ha a mögöttes összetevő vagy a függő szolgáltatások regionális adatközpont-kimaradása vagy meghibásodása van. Ez a cikk útmutatást nyújt az igény szerinti, többrégiós adatfolyamok létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

[A régiók közötti kódolási rendszer kiépítésének](media-services-high-availability-encoding.md) áttekintése

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Igény szerinti, többrégiós adatfolyamok készítése 

* Az igény szerinti, többrégiós adatfolyam-továbbítás magában foglalja az [eszközök](assets-concept.md), a [tartalmi kulcsokra vonatkozó házirendek](content-key-policy-concept.md) (ha használatos), a [folyamatos átviteli szabályzatok](streaming-policy-concept.md)és a [streaming-lokátorok](streaming-locators-concept.md)másolását. 
* Mindkét régióban létre kell hoznia a házirendeket, és naprakészen kell tartania azokat. 
* A folyamatos átviteli lokátorok létrehozásakor ugyanazt a lokátor azonosító értéket, a ContentKey azonosító értékét és a ContentKey értéket kell használnia.  
* Ha kódolja a tartalmat, azt javasoljuk, hogy kódolja a tartalmat az A régióban, és tegye közzé, majd másolja a kódolt tartalmat a B régióba, és tegye közzé az a régióval megegyező értékek használatával.
* A forrás és a kulcs kézbesítési szolgáltatás (Media Services konfigurációban) Traffic Manager a gazdagép neveit használhatja (ez az egyéni kulcs-kiszolgáló URL-címéhez hasonlóan fog kinézni).

## <a name="next-steps"></a>További lépések

nézd meg:

* [Oktatóanyag: távoli fájl kódolása URL-cím alapján és a videó továbbítása](stream-files-dotnet-quickstart.md)
* [kód mintái](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
