---
title: A Video Indexer – Azure személy modell testreszabása
titlesuffix: Azure Media Services
description: Ez a cikk áttekintést nyújt a mi egy személy modell a Video Indexer, és hogyan szabható testre.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: c74b913fc3ac35039d914fc97c9c438d2e4a3069
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799431"
---
# <a name="customize-a-person-model-in-video-indexer"></a>A Video Indexer személy modell testreszabása

Video Indexer – hírességek felismerése a videók támogatja. A hírességek felismerése funkció körülbelül 1 millió arcok IMDB Wikipedia és felső LinkedIn véleményvezérek például gyakran lekérdezett adatforrás alapján ismerteti. Továbbra is észlelt, amely nem ismeri fel a Video Indexer, de van hátra névtelen. Ügyfelek egyéni személy modelleket, és engedélyezze a Video Indexer alapértelmezés szerint nem felismerhető arcok felismerésére. Ügyfelek által egy személy neve párosítást képfájlok, a személy face személy modellek hozhat létre.  

Ha a fiók caters a különböző használati esetek, élvezheti képes arra, hogy fiókonként több személy modelleket hozhat létre. Például ha a tartalom-fiókjában található hivatott különböző csatornákon rendezni, érdemes egy külön személy modellt használ az egyes csatornák létrehozására. 

> [!NOTE]
> Minden egyes személy modell legfeljebb 1 millió embert támogatja, és mindegyik fiók legfeljebb 50 személy modellek. 

Modell létrehozása után használhatja azáltal, hogy egy adott személy modell feltöltése/indexelő vagy videó újraindexelés modell Azonosítóját. Videó egy új arc képzésekről, frissíti az adott egyéni modell, amelyhez a videó társítva lett. 

Ha nem kell a több személy modellt támogató, nem rendel egy személy Modellazonosító a videó feltöltése/indexelő vagy újraindexelés. A Video Indexer ebben az esetben a fiókot fogja használni az alapértelmezett személy modellt. 

Használhatja a Video Indexer webhely, amely az észlelt a videó szerkesztése és kezelése a fiók több egyéni személy modell leírtak szerint a [webhely használó személy modell testreszabása](customize-person-model-with-website.md) témakör. Is használhatja az API-t, a leírtak szerint [API-kat használó személy modell testreszabása](customize-person-model-with-api.md).