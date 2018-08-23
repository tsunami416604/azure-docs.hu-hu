---
title: A Microsoft Translator Text API karakter Counts |} A Microsoft Docs
description: A Microsoft Translator Text API hogyan számolja a karaktereket.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: 1b4987509c17e4064d7c54608395e272efa8de3b
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "41987466"
---
# <a name="how-the-microsoft-translator-text-api-counts-characters"></a>Hogyan számolja a Microsoft Translator Text API a karakterek

A Microsoft Translator megszámolja a bemenet minden egyes karakter. Karakterek Unicode az értelemben, nem pedig bájtban. Unicode pótló száma, a kettőt. Szóköz és jelölőnyelvi kódok is karakternek számítanak. A válasz hossza nem számít.

A Hibakeresés és BreakSentence metódusok nem számítanak bele a karakter használatalapú. Azonban várhatóan, hogy a hívások a Hibakeresés és BreakSentence módszerek más függvények, amely számításba vesszük a használatával egy megfelelő arányban vannak-e. A Microsoft fenntartja a jogot, észlelése és BreakSentence leltár indítása. 

További információ a karakterszám szerepel a [Microsoft Translator – gyakori kérdések](https://www.microsoft.com/en-us/translator/faq.aspx).
