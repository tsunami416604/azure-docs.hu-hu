---
title: Vissza N-legjobb fordítások – Translator Text
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "73837242"
---
# <a name="how-to-return-n-best-translations"></a>N – legjobb fordítások visszaküldése

> [!NOTE]
> Ez a metódus elavult. A Translator Text API 3.0-s verziójában nem érhető el.

A Microsoft Translator API GetTranslations () és GetTranslationsArray () metódusa egy opcionális logikai jelzőt ("IncludeMultipleMTAlternatives") tartalmaz.
A metódus olyan maxTranslations alternatívákat ad vissza, amelyekben a Delta a Translator Engine N-Best listájából származik.

Az aláírás:

**Szintaxis**

| C# |
|:---|
| GetTranslationsResponse Microsoft. Translator. GetTranslations (appId, Text, from, to, maxTranslations, Options); |

**Paraméterek**

| Paraméter | Leírás |
|:---|:---|
| appId | **Kötelező megadni** Ha az engedélyezési fejlécet használja, hagyja üresen a AppID mezőt, és írjon be egy olyan karakterláncot, amely tartalmazza a "tulajdonos" + "+" hozzáférési tokent.|
| szöveg | **Kötelező megadni** A lefordítani kívánt szöveget jelölő sztring. A szöveg mérete nem haladhatja meg a 10000 karaktert.|
| a | **Kötelező megadni** A lefordítani kívánt szöveg nyelvi kódját jelölő sztring. |
| erre: | **Kötelező megadni** A szöveg fordítására szolgáló nyelvkód jelölő sztring. |
| maxTranslations | **Kötelező megadni** Egy int, amely a visszaküldött fordítások maximális számát jelöli. |
| beállítások | Nem **kötelező** Egy TranslateOptions objektum, amely az alább felsorolt értékeket tartalmazza. Ezek mind opcionálisak, és alapértelmezés szerint a leggyakoribb beállítások.

* Kategória: az egyetlen támogatott és az alapértelmezett beállítás az "általános".
* ContentType: az egyetlen támogatott és az alapértelmezett beállítás a "text/plain".
* State (állapot): a kérések és válaszok korrelációját segítő felhasználói állapot. A válaszban ugyanazt a tartalmat adja vissza a rendszer.
* IncludeMultipleMTAlternatives: jelző, amely meghatározza, hogy az MT motorból egynél több alternatíva legyen visszaadva. Az alapértelmezett érték false (hamis), és csak 1 alternatívát tartalmaz.

## <a name="ratings"></a>Minősítések
A minősítések a következőképpen lesznek alkalmazva: a legjobb automatikus fordítás az 5 minősítéssel rendelkezik.
Az automatikusan generált (N-Best) fordítási alternatívák 0 minősítéssel rendelkeznek, és a megfelelési fok 100.

## <a name="number-of-alternatives"></a>Alternatívák száma
A visszaadott alternatívák száma maxTranslations, de kevesebb is lehet.

## <a name="language-pairs"></a>Nyelvi párok
Ez a funkció nem érhető el az egyszerűsített és hagyományos kínai, mindkét irányban zajló fordításokhoz. A Microsoft Translator minden más támogatott nyelvi párja számára elérhető.
