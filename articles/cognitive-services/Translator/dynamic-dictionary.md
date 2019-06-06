---
title: A dinamikus szótár – Translator Text API
titlesuffix: Azure Cognitive Services
description: Tudnivalók a Translator Text API dinamikus szótár funkcióját használja.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: v-pawal
ms.openlocfilehash: d3a77ee9273e9f587b6157bb6ed2294865b1fca5
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514261"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>A dinamikus szótár használata

Ha már ismeri a fordítást egy szót vagy kifejezést a alkalmazni szeretné, adja meg, mint belül a kérelem markup. A dinamikus szótár csak eszköze biztonságosan főnevek, mint például a megfelelő és a termék nevét.

**Szintaxis:**

< mstrans:dictionary fordítási = "kifejezés helyett szerepel fordítási" > kifejezést < / mstrans:dictionary >

**Példa: en-de:**

Forrás bemenet: A word < mstrans:dictionary fordítási =\"wordomatic\"> szó vagy kifejezés < / mstrans:dictionary > dictionary-bejegyzés.

Kimenet: Das Wort "wordomatic" Izraeli normál idő szerint ein Wörterbucheintrag.

Ez a funkció rendelkező és anélküli HTML mód ugyanúgy működik.

A szolgáltatás takarékosan. A megfelelő, és sokkal jobb megoldás a fordítás testreszabása módja egyéni Translator használatával. Egyéni a fordítót lehetővé teszi a környezetet és statisztikai valószínűségek teljes használatát. Ha van, vagy hozhat létre a betanítási adatok, amely megjeleníti a munkahelyi vagy az a kifejezés a környezetben, sokkal jobb eredményeket kap. További információ az egyéni Translator, annak [ https://aka.ms/CustomTranslator ](https://aka.ms/CustomTranslator).
