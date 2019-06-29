---
title: A dinamikus szótár – Translator Text API
titlesuffix: Azure Cognitive Services
description: Tudnivalók a Translator Text API dinamikus szótár funkcióját használja.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 2ef1e474dd5d36f1967501ea7bdedc4736954a2b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67436011"
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
