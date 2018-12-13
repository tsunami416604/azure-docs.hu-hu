---
title: A Video Indexer – Azure személy modell testreszabása
titlesuffix: Azure Media Services
description: Ez a cikk áttekintést nyújt a mi egy személy modell a Video Indexer, és hogyan szabható testre.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/05/2018
ms.author: anzaman
ms.openlocfilehash: 073cff22f17f496c2ff85cfbf716751dfea1e03e
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285024"
---
# <a name="customize-a-person-model-in-video-indexer"></a>A Video Indexer személy modell testreszabása


A video Indexer videótartalmak támogatja az arcfelismerés és hírességek felismerése. A hírességek felismerése funkció IMDB Wikipedia és felső LinkedIn véleményvezérek például gyakran lekérdezett adatforrás alapján körülbelül 1 000 000 arcok ismerteti. Észlelt, amely nem ismeri fel a híresség-felismerés szolgáltatás; azonban van hátra névtelen. Miután a videó feltöltése a Video Indexer, és vissza eredményt, lépjen vissza, és nevezze el az arcok, amely nem ismerhetők fel. Miután Ön címke egy ARC nevére, a fiókhoz tartozó személy modell hozzáadja az arcfelismerés és nevét. Video Indexer majd fogja felismerni a face a jövőbeli videók és a múltbeli videókat.

A Video Indexer webhely vagy az API segítségével, amely az észlelt egy videót a fiókjában, a szerkeszthető, mert a következő témakörök ismertetnek:

- [API-k használatával személy modell testreszabása](customize-person-model-with-api.md)
- [A webhelyen személy modell testreszabása](customize-person-model-with-website.md)
