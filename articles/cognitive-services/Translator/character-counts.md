---
title: Translator Text API karakterszám
titlesuffix: Azure Cognitive Services
description: A Translator Text API hogyan számolja a karaktereket.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: c6234a46ae55d73739dcc23110c5e0f6375c3f96
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46128740"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Hogyan számolja a Translator Text API a karakterek

A Translator Text API minden egyes karakter a bemeneti számít. Karakterek Unicode az értelemben, nem pedig bájtban. Unicode pótló száma, a kettőt. Szóköz és jelölőnyelvi kódok is karakternek számítanak. A válasz hossza nem számít.

A Hibakeresés és BreakSentence metódusok nem számítanak bele a karakter használatalapú. Azonban várhatóan, hogy a hívások a Hibakeresés és BreakSentence módszerek más függvények, amely számításba vesszük a használatával egy megfelelő arányban vannak-e. A Microsoft fenntartja a jogot, észlelése és BreakSentence leltár indítása. 

További információ a karakterszám szerepel a [Microsoft Translator – gyakori kérdések](https://www.microsoft.com/en-us/translator/faq.aspx).
