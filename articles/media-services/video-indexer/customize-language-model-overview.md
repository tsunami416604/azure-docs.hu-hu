---
title: Nyelvi modell testreszabása a Video Indexelőben – Azure
titleSuffix: Azure Media Services
description: Ez a cikk áttekintést nyújt arról, hogy mi a nyelvi modell a Video Indexer ben, és hogyan szabhatja testre.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: b096b9352be65033f2fb782b118e815dc16b43b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838320"
---
# <a name="customize-a-language-model-with-video-indexer"></a>Nyelvi modell testreszabása a Video Indexelővel

A Video Indexer támogatja az automatikus beszédfelismerést a Microsoft [Egyéni beszédszolgáltatással](https://azure.microsoft.com/services/cognitive-services/custom-speech-service/)való integráció révén. Testreszabhatja a nyelvi modellt adaptációs szöveg feltöltésével, nevezetesen azon tartományból származó szöveg feltöltésével, amelynek szókincséhez a motort szeretné alkalmazkodni. A modell betanítása után a program felismeri az adaptációs szövegben megjelenő új szavakat, feltéve, hogy az alapértelmezett kiejtés, és a Nyelvi modell új, valószínű szósorozatokat tanul meg. Az egyéni nyelvi modellek angol, spanyol, francia, német, olasz, kínai (egyszerűsített), japán, orosz, brazil portugál, hindi és koreai nyelven támogatottak. 

Vegyünk egy szót, amely nagyon specifikus, például a "Kubernetes" (az Azure Kubernetes szolgáltatás környezetében), példaként. Mivel a szó új a Video Indexer, ez elismerten "közösségek". Be kell tanítania a modellt, hogy "Kubernetes" néven ismerje el. Más esetekben a szavak léteznek, de a nyelvi modell nem számít arra, hogy egy bizonyos környezetben jelennek meg. A "tárolószolgáltatás" például nem egy kétszavas sorozat, amelyet egy nem specializált nyelvi modell meghatározott szóhalmazként ismerne fel.

Lehetősége van arra, hogy szövegfájl listájába kontextus nélkül töltsön fel szavakat. Ez részleges alkalmazkodásnak minősül. Azt is megteheti, hogy szöveges dokumentációt vagy a tartalomhoz kapcsolódó mondatokat tölt fel a jobb alkalmazkodás érdekében.

A Video Indexer API-k vagy a webhely segítségével egyéni nyelvi modelleket hozhat létre és szerkeszthet, a témakör [Következő lépései](#next-steps) szakaszában ismertetett témakörökben leírtak szerint.

## <a name="best-practices-for-custom-language-models"></a>Gyakorlati tanácsok egyéni nyelvi modellekhez

Video Indexer tanul alapján valószínűségek szó kombinációk, így tanulni a legjobban:

* Adj elég valós példát a mondatok, mert lenne beszélni.
* Sonkánként csak egy mondatot tegyél, ne többet. Ellenkező esetben a rendszer megtanulja a valószínűségeket a mondatok között.
* Ez rendben van, hogy egy szót, mint egy mondat, hogy növeljék a szót mások ellen, de a rendszer tanul a legjobban a teljes mondatokat.
* Amikor új szavakat vagy rövidítéseket vezetbe be, ha lehetséges, adjon meg annyi példát a használatra egy teljes mondatban, hogy a lehető legtöbb kontextust adja a rendszernek.
* Próbálja meg, hogy több alkalmazkodási lehetőségeket, és hogyan működnek az Ön számára.
* Kerülje az ismétlődést pontosan ugyanazt a mondatot többször. Elfogultságot okozhat a bemenet többi részével szemben.
* Kerülje a nem gyakori szimbólumok (~, @ @ % &) feladását, mivel azok elvesznek. A mondatokat, amelyekben megjelennek, szintén elvetik.
* Kerülje a túl nagy bemenetek, például több százezer mondat elhelyezését, mert ez gyengíti a kiemelés hatását.

## <a name="next-steps"></a>További lépések

[Nyelvi modell testreszabása API-k használatával](customize-language-model-with-api.md)

[Nyelvi modell testreszabása a webhely használatával](customize-language-model-with-website.md)
