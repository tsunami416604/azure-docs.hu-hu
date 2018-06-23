---
title: Nyelv támogatott egyéni beszéd szolgáltatásban az Azure-on |} Microsoft Docs
description: Egyéni beszéd szolgáltatás kognitív szolgáltatásban támogatott nyelvek áttekintése.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: c378fd951f9cd04884f44fbec5accb5d9a886bfe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346887"
---
# <a name="supported-locales-in-custom-speech-service"></a>Az egyéni beszéd Service támogatott nyelv
Az egyéni beszéd szolgáltatás jelenleg modellek testreszabási támogatja a következő területi beállításokkal:

| Modell típusa | Nyelvi támogatás |
|----|-----|
| Akusztikus modellek | Amerikai angol (en-US) |
| Nyelvi modellek | Amerikai angol (en-US), kínai (zh-CN) |

Bár akusztikus modell testreszabási csak akkor támogatott a angol, kínai akusztikus adatok importálása támogatott testreszabott kínai nyelvi modellek offline tesztelés céljából.

A megfelelő területi beállítás bármely műveletek előtt meg kell adni. Az aktuális területi beállítása minden adat, a modell és a központi telepítési lapok tábla címében jelzi. A területi beállítás módosításához kattintson a "Területi beállítás módosítása" gombra, a tábla cím alatt. Ekkor megjelenik a területi beállítás visszaigazolási lapra. Kattintson az "OK" táblázatban.

Kell tartania a következő lépések
* Ismerje meg, [egy egyéni akusztikus modell létrehozása](cognitive-services-custom-speech-create-acoustic-model.md) pontosságának javítása érdekében
* Ismerje meg, [egy egyéni nyelvi modell létrehozása](cognitive-services-custom-speech-create-language-model.md) a felismerési sebesség növelése érdekében
* Kövesse a [írjanak elő irányelvek](cognitive-services-custom-speech-transcription-guidelines.md) való felkészülés az adatok
