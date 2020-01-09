---
title: 'Gyors útmutató: a blob Storage szolgáltatásban tárolt beszéd felismerése'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 495d2a8e38b58eb3ef1494e6a3a33ee1dc32d049
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467204"
---
Ebben a rövid útmutatóban egy REST API fogja felismerni a Batch-folyamatokban lévő fájlok beszédét. A Batch-folyamatok felhasználói interakció nélkül hajtják végre a beszéd átírását. Egyszerű programozási modellt biztosít, anélkül, hogy a párhuzamosságot, az egyéni beszédfelismerési modelleket vagy egyéb részleteket kellene kezelnie. Speciális vezérlési lehetőségeket is magában foglal, miközben az Azure Speech Service-erőforrások hatékony használatát teszi elérhetővé.

A [Batch-átirat áttekintése](../../../batch-transcription.md) a funkció használatának részleteit ismerteti. A részletes API a `Custom Speech transcriptins`vámtarifaszám alá tartozó, [hencegő dokumentumként](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A)érhető el. 

A következő rövid útmutató végigvezeti a használati példákon.
