---
title: A Microsoft Translator szöveg API karaktert száma |} Microsoft Docs
description: A Microsoft Translator szöveg API hogyan karakterek száma.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: ebe3e3606a0413730e1fbfd704a6403f77275f89
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347674"
---
# <a name="how-the-microsoft-translator-text-api-counts-characters"></a>Hogyan a a Microsoft Translator szöveg API karakterek száma

A Microsoft Translator minden a bemeneti karakter található. Az karakter Unicode értelemben véve nem bájt. Unicode helyettesek száma két karaktere. Szóközt és jelölő is karakternek számítanak. A válasz hossza nem számít.

A Hibakeresés és BreakSentence metódusok nem számítanak bele a karakter fogyasztás. Azonban elvárjuk, hogy vannak-e a hívások a Hibakeresés és BreakSentence módszer egy megfelelő arányban más számlálási függvények használatát. A Microsoft fenntartja a jogot észlelése és BreakSentence számbavételi elindításához. 

Fordítása ajánlatok automatikus észleléséhez, ha a From nyelvi paraméter. 

További információ a karakterszám van a [Microsoft Translator – gyakori kérdések](https://www.microsoft.com/en-us/translator/faq.aspx).
