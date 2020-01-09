---
title: Dinamikus szótár – Translator Text API
titleSuffix: Azure Cognitive Services
description: Ez a cikk az Azure Cognitive Services Translator Text API dinamikus szótár funkciójának használatát ismerteti.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 944bca8644da6127e73af04eb75d01697cd34399
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446716"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Dinamikus szótár használata

Ha már tudja, hogy milyen fordítást szeretne alkalmazni egy szóhoz vagy kifejezésre, a kérelemben megjelölheti azt. A dinamikus szótár csak olyan összetett nevek esetén biztonságos, mint a nevek és a termékek neve.

**Szintaxis**

< msTrans: szótár fordítása = "kifejezés fordítása" > mondat </msTrans: szótár >

**Követelmények:**

* A `From` és a `To` nyelvnek angol és más támogatott nyelvet kell tartalmaznia. 
* Az automatikus észlelés funkció használata helyett az API-fordítási kérelemben meg kell adnia a `From` paramétert. 

**Példa: en-de:**

Forrás bemenet: `The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

Cél kimenete: `Das Wort "wordomatic" ist ein Wörterbucheintrag.`

Ez a funkció ugyanúgy működik, mint a HTML-módban és anélkül.

Használja a szolgáltatást takarékosan. A fordítás testreszabásának jobb módja az egyéni fordító használata. Az egyéni fordító a kontextus és statisztikai valószínűségek teljes körű kihasználását teszi lehetővé. Ha olyan képzési adatait is létrehozhat, amelyek kontextusban mutatják be a munkát vagy kifejezést, sokkal jobb eredményeket érhet el. Az egyéni fordítóról további információt a következő helyen talál: [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
