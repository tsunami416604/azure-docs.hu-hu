---
title: Támogatott nyelv és nyelven – Custom Speech Service
titlesuffix: Azure Cognitive Services
description: Támogatott nyelvek a Cognitive Services Custom Speech Service áttekintése.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 34af6673689244364ab3a1fe3f2a6ab056ea8598
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223955"
---
# <a name="supported-locales-in-custom-speech-service"></a>A Custom Speech Service támogatott nyelv
A Custom Speech Service jelenleg támogatja modell testreszabása a következő területi beállításokkal:

| Modell típusa | Nyelvi támogatás |
|----|-----|
| Akusztikai modell | Amerikai angol (en-US) |
| Nyelvi modell | Amerikai angol (en-US), kínai (zh-CN) |

Akusztikai modell testreszabása csak az angol támogatják, testre szabott kínai nyelvi modellek offline tesztelés céljából kínai akusztikai adatok importálása támogatott.

Bármely művelet elvégzése előtt ki kell választani a megfelelő területi beállítást. Az aktuális területi beállítás minden adat-, modell- és üzembehelyezési oldalon megjelenik a táblázat címében. A területi beállítás módosításához kattintson a "Területi beállítás módosítása" gombra, a tábla címe alatt található. Ekkor a területi beállítás jóváhagyást jelző oldal. A táblázathoz való visszatéréshez kattintson az „OK” gombra.

A következő lépésekkel kell követés
* Ismerje meg, [egy importálni akusztikai modell létrehozása](cognitive-services-custom-speech-create-acoustic-model.md) pontosságának javítása érdekében
* Ismerje meg, [egyéni nyelvi modell létrehozása](cognitive-services-custom-speech-create-language-model.md) a felismerés sebesség javítása érdekében
* Kövesse a [beszédátírási irányelvek](cognitive-services-custom-speech-transcription-guidelines.md) az adatok előkészítése
