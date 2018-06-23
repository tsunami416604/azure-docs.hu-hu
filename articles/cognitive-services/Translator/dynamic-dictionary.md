---
title: A Microsoft Translator szöveg API dinamikus szótár |} Microsoft Docs
description: Tudnivalók a Microsoft Translator szöveg API dinamikus szótár funkcióját használja.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: a18348c9786669ac41c4e149577d97cd631d5531
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349718"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-microsoft-translator-text-api"></a>A dinamikus szótár szolgáltatás a Microsoft Translator szöveg API használata

Ha már ismeri a fordítás egy szót vagy kifejezést alkalmazni kívánja, a jelölés belül a kérelmet, megadhat. A dinamikus szótár csak nem biztonságos főnevek például a megfelelő és a termék nevét. 

**Szintaxis:** 

< mstrans:dictionary fordítási = "kifejezés fordítása" > kifejezést < / mstrans:dictionary >

**Például: hu-de:**

Bemeneti forrás: A word < mstrans:dictionary fordítási =\"wordomatic\"> szót vagy kifejezést < / mstrans:dictionary > dictionary-bejegyzés.

Kimeneti cél: Das Wort "wordomatic" ist ein Wörterbucheintrag.

Ez a funkció használatával és anélkül HTML mód ugyanúgy működik. 

A szolgáltatás takarékosan. A megfelelő és sokkal hatékonyabb testreszabásának fordítás módja a Microsoft Translator központ használatával. A központ lehetővé teszi a teljes mértékben hasznosítani környezetet és statisztikai valószínűség. Ha van, vagy is biztosít, amely a munkahelyi vagy kifejezés környezetben betanítási adatok létrehozásához, mennyi jobb eredményeket kap. További információ a következő hub található [ http://hub.microsofttranslator.com ](http://hub.microsofttranslator.com).

