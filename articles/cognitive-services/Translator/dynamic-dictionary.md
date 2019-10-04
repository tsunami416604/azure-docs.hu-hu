---
title: Dinamikus szótár – Translator Text API
titleSuffix: Azure Cognitive Services
description: A Translator Text API dinamikus szótár funkciójának használata.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: f586702350b38c6d546767f64a8e2f6115b59817
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595289"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Dinamikus szótár használata

Ha már tudja, hogy milyen fordítást szeretne alkalmazni egy szóhoz vagy kifejezésre, a kérelemben megjelölheti azt. A dinamikus szótár csak olyan összetett nevek esetén biztonságos, mint a megfelelő nevek és a termékek neve.

**Szintaxis**

< msTrans: szótár fordítása = "kifejezés fordítása" > mondat </msTrans: szótár >

**Követelmények**

* A `From` és`To` a nyelvnek eltérőnek kell lennie. 
* Az automatikus észlelés funkció `From` használata helyett az API-fordítási kérelemben meg kell adnia a paramétert. 

**Példa: en-de:**

Forrás bemenet: A Word < msTrans: Dictionary Translation =\"wordomatic\"> Word vagy kifejezés </msTrans: Dictionary > egy szótár bejegyzés.

Cél kimenete: Das orbáncfű "wordomatic" ist Ein Wörterbucheintrag.

Ez a funkció ugyanúgy működik, mint a HTML-módban és anélkül.

A szolgáltatást takarékosan kell használni. A fordítás testreszabásának megfelelő és sokkal jobb módja az egyéni fordító használata. Az egyéni fordító a kontextus és statisztikai valószínűségek teljes körű kihasználását teszi lehetővé. Ha olyan képzési adatait is létrehozhat, amelyek kontextusban mutatják be a munkát vagy kifejezést, sokkal jobb eredményeket érhet el. Az egyéni fordítóval kapcsolatos további információkért tekintse [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator)meg a következő témakört:.
