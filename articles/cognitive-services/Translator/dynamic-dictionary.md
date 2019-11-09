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
ms.openlocfilehash: 5f007660b98a22ff9ffad072991a39cf59518b6a
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837341"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Dinamikus szótár használata

Ha már tudja, hogy milyen fordítást szeretne alkalmazni egy szóhoz vagy kifejezésre, a kérelemben megjelölheti azt. A dinamikus szótár csak olyan összetett nevek esetén biztonságos, mint a megfelelő nevek és a termékek neve.

**Szintaxis**

< msTrans: szótár fordítása = "kifejezés fordítása" > mondat </msTrans: szótár >

**Követelmények**

* A `From` és a `To` nyelvnek eltérőnek kell lennie. 
* Az automatikus észlelés funkció használata helyett az API-fordítási kérelemben meg kell adnia a `From` paramétert. 

**Példa: en-de:**

Forrás bemenet: a Word < msTrans: Dictionary Translation =\"wordomatic\"> szó vagy kifejezés </msTrans: szótár > egy szótár bejegyzés.

Cél kimenete: Das orbáncfű "wordomatic" ist Ein Wörterbucheintrag.

Ez a funkció ugyanúgy működik, mint a HTML-módban és anélkül.

A szolgáltatást takarékosan kell használni. A fordítás testreszabásának megfelelő és sokkal jobb módja az egyéni fordító használata. Az egyéni fordító a kontextus és statisztikai valószínűségek teljes körű kihasználását teszi lehetővé. Ha olyan képzési adatait is létrehozhat, amelyek kontextusban mutatják be a munkát vagy kifejezést, sokkal jobb eredményeket érhet el. Az egyéni fordítóról további információt a következő helyen talál: [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
