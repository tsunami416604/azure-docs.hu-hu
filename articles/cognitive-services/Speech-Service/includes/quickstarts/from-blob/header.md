---
title: 'Rövid útmutató: A blobstorageban tárolt beszédfelismerés – Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/13/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 567d8cec10543f233a384635daba1474b41bde9b
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80573269"
---
Ebben a rövid útmutatóban egy REST API-t fog használni a kötegelt folyamat fájljaiból származó beszédfelismeréshez. A kötegelt folyamat felhasználói beavatkozás nélkül hajtja végre a beszédátírást. Ez ad egy egyszerű programozási modell, anélkül, hogy kezelni egyidejűség, egyéni beszéd modellek, vagy egyéb részleteket. Ez speciális vezérlési lehetőségeket foglal magában, miközben hatékonyan használja az Azure beszédszolgáltatási erőforrásait.

A rendelkezésre álló lehetőségekről és a konfigurációs részletekről további információt a [kötegelt átírás című](../../../batch-transcription.md)témakörben talál.

A következő rövid útmutató végigvezeti a használati minta.
