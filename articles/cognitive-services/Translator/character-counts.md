---
title: Karakterek száma – Translator Text API
titlesuffix: Azure Cognitive Services
description: A Translator Text API hogyan számolja a karaktereket.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: v-jansko
ms.openlocfilehash: c88eb56288d3a7cf46ce84430a53c12a4ee31c7a
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58917413"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Hogyan számolja a Translator Text API a karakterek

A Translator Text API minden Unicode kódpont a bemeneti szöveg számít egy karakter. Minden egyes SMS-t egy nyelv fordítását egy külön fordítási számít, még akkor is, ha a kérés érkezett a egy egyetlen API hívással fordítása több nyelvhez. A válasz hossza nem számít.

Mi számít a következő:

* A Translator Text API, a kérelem törzsében szereplő átadott szöveg
   * `Text` Ha a fordítás Transliterate és szótár keresési módszerekkel
   * `Text` és `Translation` szótár példák módszer használata esetén
* Az összes jelölés: HTML, XML-címkéket, a szövegmezőbe, a kérelem törzsében lévő stb. A kérelem létrehozásához használt JSON-jelölés (például "szöveget:") nem vesszük számításba.
* Az egyes levelek
* Absztrakt
* Egy szóközt, lapon, markup és bármilyen típusú szóköz karakter
* Minden Unicode meghatározott kód ponttal
* Ismétlődő fordítását, akkor is, ha ugyanannak a szövegnek korábban van lefordítva

Parancsprogramok, például a kínai, japán és képírásjelek alapján a Translator Text API továbbra is beleszámít a Unicode kódpontok száma, ideogram egy karaktert. Kivétel: Unicode pótló száma, a kettőt.

Kérelmek, szavak, bájt vagy mondatokat száma nincs jelentősége, a karakterek száma. 

A Hibakeresés és BreakSentence metódusok nem számítanak bele a karakter használatalapú. Azonban várhatóan, hogy a hívások a Hibakeresés és BreakSentence módszerek más függvények, amely számításba vesszük a használatával egy megfelelő arányban vannak-e. Ha, győződjön meg arról, hibakeresés vagy BreakSentence hívások száma meghaladja a 100-szor, a Microsoft által leltározott másfajta fenntartja a jogot a Hibakeresés és BreakSentence módszerek használatát korlátozása.


További információ a karakterszám szerepel a [Microsoft Translator – gyakori kérdések](https://www.microsoft.com/en-us/translator/faq.aspx).
