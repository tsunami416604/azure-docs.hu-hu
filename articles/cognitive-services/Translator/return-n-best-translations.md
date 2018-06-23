---
title: Térjen vissza a Microsoft Translator szöveggel API N-legjobb fordítások |} Microsoft Docs
description: A Microsoft Translator szöveg API-jával N-legjobb fordítások visszaadása.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 3eafe50f69ae1a6748342e64a414ecee4467d0d1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347683"
---
# <a name="how-to-return-n-best-translations"></a>N-legjobb fordítások visszaállítása

> [!NOTE]
> Ez a metódus elavult. Nem érhető el a fordító szöveg API 3.0-s verzió.

A Microsoft Translator API GetTranslations() és GetTranslationsArray() módszerek közé tartozik egy elhagyható logikai jelző "IncludeMultipleMTAlternatives".
A metódus ahol a különbözeti van megadva, a legjobb N listából a fordító motor legfeljebb maxTranslations alternatívák visszaadható.

Az aláírás van:

**Syntax**

| C# |
|:---|
| GetTranslationsResponse Microsoft.Translator.GetTranslations(appId, text, from, to, maxTranslations, options); |

**Paraméterek**

| Paraméter | Leírás |
|:---|:---|
| appId | **Szükséges** hitelesítési fejlécéhez használata esetén hagyja üresen a appid mezőt ellenkező esetben adja meg egy karakterláncot, amely tartalmazza a "Tulajdonos" + "" + a hozzáférési jogkivonat.|
| szöveg | **Szükséges** képviselő lefordítani a szöveges karakterláncot. A szöveg mérete legfeljebb 10000 karakterből állhat.|
| forrás: | **Szükséges** egy karakterlánc, amely a szöveg lefordítani a nyelvi kódot. |
| erre: | **Szükséges** karakterláncként lefordítani az a szöveg a nyelvi kódot. |
| maxTranslations | **Szükséges** egy int vissza fordítások maximális számát. |
| beállítások | **Nem kötelező** A TranslateOptions objektum, amely az alábbi értékeket tartalmazza. Azok az összes megadása nem kötelező, és a leggyakrabban használt beállításait az alapértelmezett.

* Kategória: Az egyetlen támogatott, és az alapértelmezett beállítás "általános".
* A ContentType: Az egyetlen támogatott, és az alapértelmezett beállítás "text/plain".
* Állapot: Felhasználói állapot összefüggésbe kérés és válasz. A válaszban visszaadott ugyanaz a tartalma.
* IncludeMultipleMTAlternatives: Ez a jelző azt határozza meg, hogy a fő Célkiszolgáló motor alternatívák egynél több vissza. Alapértelmezett értéke false, és tartalmazza a csak az 1 helyett.

## <a name="ratings"></a>Minősítések
A minősítések alkalmazza az alábbiak szerint: A legjobb automatikus fordítás, 5 besorolású.
Az automatikusan generált (N-legjobb) fordítási alternatívák 0 minősítést van, és 100 egyezés fokú.

## <a name="number-of-alternatives"></a>Számos lehetőség közül
A visszaadott alternatívák száma legfeljebb maxTranslations, de kisebb lehet.

## <a name="language-pairs"></a>Nyelvi párok
Ez a funkció nem érhető el fordítások egyszerűsített és hagyományos kínai, mindkét irányban között. Érhető el az összes többi támogatott Microsoft Translator nyelvi párokat.
