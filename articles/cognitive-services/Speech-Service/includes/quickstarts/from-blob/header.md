---
title: 'Gyors útmutató: a blob Storage szolgáltatásban tárolt beszéd felismerése'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/13/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 514d1197c5e6d0f4c0f38faf24c6c2522d489820
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400920"
---
Ebben a rövid útmutatóban egy REST API fogja felismerni a Batch-folyamatokban lévő fájlok beszédét. A Batch-folyamatok felhasználói interakció nélkül hajtják végre a beszéd átírását. Egyszerű programozási modellt biztosít, anélkül, hogy a párhuzamosságot, az egyéni beszédfelismerési modelleket vagy egyéb részleteket kellene kezelnie. Speciális vezérlési lehetőségeket is magában foglal, miközben az Azure Speech Service-erőforrások hatékony használatát teszi elérhetővé.

További információ az elérhető beállításokról és a konfiguráció részleteiről: [Batch átírása](../../../batch-transcription.md).

A következő rövid útmutató végigvezeti a használati példákon.
