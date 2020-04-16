---
title: 'Rövid útmutató: A blobstorageban tárolt beszédfelismerés – Beszédszolgáltatás'
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400920"
---
Ebben a rövid útmutatóban egy REST API-t fog használni a kötegelt folyamat fájljaiból származó beszédfelismeréshez. A kötegelt folyamat felhasználói beavatkozás nélkül hajtja végre a beszédátírást. Ez ad egy egyszerű programozási modell, anélkül, hogy kezelni egyidejűség, egyéni beszéd modellek, vagy egyéb részleteket. Ez speciális vezérlési lehetőségeket foglal magában, miközben hatékonyan használja az Azure beszédszolgáltatási erőforrásait.

A rendelkezésre álló lehetőségekről és a konfigurációs részletekről további információt a [kötegelt átírás című](../../../batch-transcription.md)témakörben talál.

A következő rövid útmutató végigvezeti a használati minta.
