---
title: Karakterszámok – Fordítószöveg API
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogy az Azure Cognitive Services Translator Text API hogyan számolja a karaktereket, így megismerheti, hogyan hogyan történik a tartalom betöltése.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cb70b8624ac0d909511032622948f14f8764b153
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73888163"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Hogyan számolja a Fordító szöveg API-t karakterek

A Translator Text API a bemeneti szöveg minden Unicode-kódjástlefúvását karakterként számolja. A szöveg nyelvre történő minden egyes fordítása külön fordításnak számít, még akkor is, ha a kérés egyetlen API-hívásban történt, és több nyelvre fordított. A válasz hossza nem számít.

Ami számít, az:

* A translator text API-nak átadott szöveg a kérelem törzsében
   * `Text`A Fordítás, a Transliterate és a Szótárkeresés metódus használatakor
   * `Text`és `Translation` a Szótárpéldák módszer használatakor
* Minden jelölés: HTML, XML címkék, stb a kérelem törzsének szövegmezőjében. A kérelem létrehozásához használt JSON-jelölés (például "Szöveg:") nem számít.
* Egyedi levél
* Írásjelek
* Szóköz, tabulátor, korrektúra és bármilyen szóközkarakter
* A Unicode-ban definiált összes kódpont
* Ismételt fordítás, még akkor is, ha korábban ugyanazt a szöveget fordította le

Az ideogramokon alapuló parancsfájlok, például a kínai és a japán kanji esetében a Translator Text API továbbra is megszámolja a Unicode-kódpontok számát, egy karaktert ideogramonként. Kivétel: A Unicode helyettesítők két karakternek számítanak.

A kérések, szavak, bájtok vagy mondatok száma nem releváns a karakterszámban.

A Detect és a BreakSentence metódusok hívásai nem számítanak bele a karakterfelhasználásba. Azonban arra számítunk, hogy a hívások a detect és BreakSentence metódusok ésszerű arányban vannak a többi függvény ek, amelyek számítanak. Ha az észlelési vagy BreakSentence-hívások száma 100-szor meghaladja a többi megszámlált metódus számát, a Microsoft fenntartja a jogot, hogy korlátozza a Detect and BreakSentence metódusok használatát.


A karakterszámról további információt a [Microsoft Translator GYIK című részben talál.](https://www.microsoft.com/en-us/translator/faq.aspx)
