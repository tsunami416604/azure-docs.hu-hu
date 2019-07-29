---
title: Vissza N-legjobb fordítások – Translator Text API
titleSuffix: Azure Cognitive Services
description: N-legjobb fordítások visszaadása a Microsoft Translator Text API használatával.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ms.openlocfilehash: 7aec6bb81f3ca99ead744767668c2e4f1d1d4d53
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68594883"
---
# <a name="how-to-return-n-best-translations"></a>N – legjobb fordítások visszaküldése

> [!NOTE]
> Ez a metódus elavult. A Translator Text API 3.0-s verziójában nem érhető el.

A Microsoft Translator API GetTranslations () és GetTranslationsArray () metódusa egy opcionális logikai jelzőt ("IncludeMultipleMTAlternatives") tartalmaz.
A metódus olyan maxTranslations alternatívákat ad vissza, amelyekben a Delta a Translator Engine N-Best listájából származik.

Az aláírás:

**Syntax**

| C# |
|:---|
| GetTranslationsResponse Microsoft.Translator.GetTranslations(appId, text, from, to, maxTranslations, options); |

**Paraméterek**

| Paraméter | Leírás |
|:---|:---|
| appId | **Kötelező megadni** Ha az engedélyezési fejlécet használja, hagyja üresen a AppID mezőt, és írjon be egy olyan karakterláncot, amely tartalmazza a "tulajdonos" + "+" hozzáférési tokent.|
| text | **Kötelező megadni** A lefordítani kívánt szöveget jelölő sztring. A szöveg mérete nem haladhatja meg a 10000 karaktert.|
| from | **Kötelező megadni** A lefordítani kívánt szöveg nyelvi kódját jelölő sztring. |
| to | **Kötelező megadni** A szöveg fordítására szolgáló nyelvkód jelölő sztring. |
| maxTranslations | **Kötelező megadni** Egy int, amely a visszaküldött fordítások maximális számát jelöli. |
| options | Nem **kötelező** Egy TranslateOptions objektum, amely az alább felsorolt értékeket tartalmazza. Ezek mind opcionálisak, és alapértelmezés szerint a leggyakoribb beállítások.

* Kategória Az egyetlen támogatott és az alapértelmezett beállítás az "általános".
* ContentType: Az egyetlen támogatott és az alapértelmezett beállítás a "text/plain".
* Állapot: A kérelem és válasz összekapcsolását segítő felhasználói állapot. A válaszban ugyanazt a tartalmat adja vissza a rendszer.
* IncludeMultipleMTAlternatives: jelző, amely meghatározza, hogy az MT motorból egynél több alternatíva legyen visszaadva. Az alapértelmezett érték false (hamis), és csak 1 alternatívát tartalmaz.

## <a name="ratings"></a>Értékelés
A minősítések a következőképpen lesznek alkalmazva: A legjobb automatikus fordítás 5-ös minősítéssel rendelkezik.
Az automatikusan generált (N-Best) fordítási alternatívák 0 minősítéssel rendelkeznek, és a megfelelési fok 100.

## <a name="number-of-alternatives"></a>Alternatívák száma
A visszaadott alternatívák száma maxTranslations, de kevesebb is lehet.

## <a name="language-pairs"></a>Nyelvi párok
Ez a funkció nem érhető el az egyszerűsített és hagyományos kínai, mindkét irányban zajló fordításokhoz. A Microsoft Translator minden más támogatott nyelvi párja számára elérhető.
