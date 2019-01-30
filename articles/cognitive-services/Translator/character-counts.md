---
title: Karakterek száma – Translator Text API
titlesuffix: Azure Cognitive Services
description: A Translator Text API hogyan számolja a karaktereket.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: c56622ee5e25fc422d02cec6ecfaa1f847e9e769
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226434"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Hogyan számolja a Translator Text API a karakterek

A Translator Text API minden egyes karakter a bemeneti számít. Karakterek Unicode az értelemben, nem pedig bájtban. Unicode pótló száma, a kettőt. Szóköz és jelölőnyelvi kódok is karakternek számítanak. A válasz hossza nem számít.

A Hibakeresés és BreakSentence metódusok nem számítanak bele a karakter használatalapú. Azonban várhatóan, hogy a hívások a Hibakeresés és BreakSentence módszerek más függvények, amely számításba vesszük a használatával egy megfelelő arányban vannak-e. A Microsoft fenntartja a jogot, észlelése és BreakSentence leltár indítása.

További információ a karakterszám szerepel a [Microsoft Translator – gyakori kérdések](https://www.microsoft.com/en-us/translator/faq.aspx).
