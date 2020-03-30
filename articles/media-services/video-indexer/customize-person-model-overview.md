---
title: Személymodell testreszabása a Video Indexelőben – Azure
titleSuffix: Azure Media Services
description: Ez a cikk áttekintést nyújt arról, hogy mi a Személy modell a Video Indexerben, és hogyan szabhatja testre.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 3fabba98cb137975da749411ca9accb5a951742d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838298"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Személymodell testreszabása a Video Indexelőben

A Video Indexer támogatja a hírességek felismerését a videóidban. A híresség elismerés vonás borít körülbelül egy millió szembenéz azon alapszik általában igényelt adatforrás mint IMDB, Wikipedia, és tető Összekapcsolt influencers. A Video Indexer által nem felismert arcok továbbra is észlelhetők, de névtelenek maradnak. Az ügyfelek egyéni személymodelleket hozhatnak létre, és engedélyezhetik, hogy a Video Indexer felismerje azokat az arcokat, amelyeket a rendszer alapértelmezés szerint nem ismer fel. Az ügyfelek úgy hozhatják létre ezeket a Személy modelleket, hogy egy személy nevét párosítják a személy arcának képfájljaival.  

Ha fiókja különböző használati eseteket lát el, akkor fiókonként több személymodellt hozhat létre. Ha például a fiók tartalmát különböző csatornákra kell rendezni, érdemes lehet minden csatornához külön személymodellt létrehozni. 

> [!NOTE]
> Minden személy modell legfeljebb 1 millió embert támogat, és minden fiókban legfeljebb 50 személy modell áll. 

A modell létrehozása után használhatja azt egy adott személy modell modellazonosítójának megadásával egy videó feltöltésekor/indexelésekor vagy újraindexelésekor. Egy videó új arcának betanítása frissíti azt az egyéni modellt, amelyhez a videó társítva volt. 

Ha nincs szüksége a többszemélyes modell támogatására, ne rendeljen személymodell-azonosítót a videóhoz feltöltés/indexelés vagy újraindexelés közben. Ebben az esetben a Video Indexer az alapértelmezett személymodellt fogja használni a fiókjában. 

A Video Indexer webhely segítségével szerkesztheti a videóban észlelt arcokat, és kezelheti a fiókjában lévő több egyéni személymodellt, ahogy azt a [Személy testreszabása modell egy webhelytémakör használatával](customize-person-model-with-website.md) című témakör ismerteti. Az API-t is használhatja, ahogy azt a [Személy testreszabása modell API-k használatával](customize-person-model-with-api.md)című részben ismerteti.
