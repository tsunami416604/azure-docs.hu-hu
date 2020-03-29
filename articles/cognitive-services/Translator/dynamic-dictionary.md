---
title: Dinamikus szótár - Fordító szöveg API
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan használhatja az Azure Cognitive Services Translator Text API dinamikus szótárfunkcióját.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 944bca8644da6127e73af04eb75d01697cd34399
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75446716"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Dinamikus szótár használata

Ha már ismeri azt a fordítást, amelyet alkalmazni szeretne egy szóra vagy kifejezésre, akkor a kérelemben megjelölésként is megadhatod. A dinamikus szótár csak összetett főnevek, például a megfelelő nevek és terméknevek esetén biztonságos.

**Szintaxis:**

<mstrans:dictionary translation="kifejezés fordítása">kifejezés</mstrans:dictionary>

**Követelmények:**

* A `From` `To` és a nyelveknek tartalmazniuk kell az angol és egy másik támogatott nyelvet. 
* Az automatikus `From` észlelési szolgáltatás használata helyett a paramétert is bele kell foglalnia az API-fordítási kérelembe. 

**Példa: en-de:**

Forrásbemenet:`The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

Célteljesítmény:`Das Wort "wordomatic" ist ein Wörterbucheintrag.`

Ez a szolgáltatás ugyanúgy működik HTML módban és anélkül is.

Takarékosan használja a funkciót. A fordítás testreszabásának jobb módja az Egyéni fordító használata. Az egyéni fordító teljes mértékben kihasználja a környezetet és a statisztikai valószínűségeket. Ha rendelkezik vagy hozhat létre képzési adatokat, amelyek a munkáját vagy kifejezését kontextusban jelenítik meg, sokkal jobb eredményeket érhet el. Az egyéni fordítóról további [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator)információt a .
