---
title: Vissza N-legjobb fordítások - Fordító szöveg
titleSuffix: Azure Cognitive Services
description: N-legjobb fordítások visszaadása a Translator Text API használatával.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ROBOTS: NOINDEX
ms.openlocfilehash: eff25877165ac365e0af77651147fcdd1eebe294
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837242"
---
# <a name="how-to-return-n-best-translations"></a>Hogyan lehet visszaadni az N-Legjobb fordításokat?

> [!NOTE]
> Ez a módszer elavult. Nem érhető el a Translator Text API 3.0-s verziójában.

A Microsoft Translator API GetTranslations() és GetTranslationsArray() metódusai egy választható logikai jelzőt tartalmaznak az "IncludeMultipleMTAlternatives" (IncludeMultipleMTAlternatives) jelzővel.
A módszer visszaáll maxTranslations alternatívák, ahol a delta a fordítómotor N-Legjobb listájából származik.

Az aláírás:

**Szintaxis**

| C# |
|:---|
| GetTranslationsResponse Microsoft.Translator.GetTranslations(appId, szöveg, a, a maxTranslations, options); |

**Paraméterek**

| Paraméter | Leírás |
|:---|:---|
| appId | **Kötelező** Ha az engedélyezésfejlécet használja, hagyja üresen az appid mezőt, adjon meg egy "Tulajdonos" + " " + hozzáférési jogkivonatot tartalmazó karakterláncot.|
| szöveg | **Kötelező** A fordítandó szöveget jelölő karakterlánc. A szöveg mérete nem haladhatja meg az 10000 karaktert.|
| honnan | **Kötelező** A lefordítandó szöveg nyelvkódját jelölő karakterlánc. |
| erre: | **Kötelező** A szöveg lefordításához használt nyelvkódot jelölő karakterlánc. |
| maxFordítások | **Kötelező** A visszaadandó fordítások maximális számát képviselő int. |
| Lehetőségek | **Nem kötelező** Az alábbi értékeket tartalmazó TranslateOptions objektum. Ezek mind választható, és alapértelmezés szerint a leggyakoribb beállításokat.

* Kategória: Az egyetlen támogatott, és az alapértelmezett beállítás az "általános".
* ContentType: Az egyetlen támogatott és az alapértelmezett beállítás a "szöveg/sima".
* Állapot: A kérelem és a válasz összeegyeztésének felhasználói állapota. A válaszban ugyanaz a tartalom jelenik meg.
* IncludeMultipleMTAlternatives: jelző annak meghatározásához, hogy egynél több alternatívát adjon-e vissza az MT motorból. Az alapértelmezett érték hamis, és csak 1 alternatívát tartalmaz.

## <a name="ratings"></a>Minősítések
A minősítések a következőképpen érvényesek: A legjobb automatikus fordítás értékelése 5.
Az automatikusan generált (N-Legjobb) fordítási alternatívák értékelése 0, és 100-as egyezési fokúa van.

## <a name="number-of-alternatives"></a>Alternatívák száma
A visszaadott alternatívák száma legfeljebb maxTranslations, de lehet, hogy kevesebb.

## <a name="language-pairs"></a>Nyelvi párok
Ez a funkció nem érhető el az egyszerűsített és a hagyományos kínai közötti fordításhoz mindkét irányban. Az összes többi Microsoft Translator által támogatott nyelvpárhoz elérhető.
