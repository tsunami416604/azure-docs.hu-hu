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
ms.openlocfilehash: a7fa1813eec8e73f4cc937062ad4374aef93e6e0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79381993"
---
Ebben a rövid útmutatóban egy REST API-t fog használni a kötegelt folyamat fájljaiból származó beszédfelismeréshez. A kötegelt folyamat felhasználói beavatkozás nélkül hajtja végre a beszédátírást. Ez ad egy egyszerű programozási modell, anélkül, hogy kezelni egyidejűség, egyéni beszéd modellek, vagy egyéb részleteket. Ez speciális vezérlési lehetőségeket foglal magában, miközben hatékonyan használja az Azure beszédszolgáltatási erőforrásait.

A következő rövid útmutató végigvezeti a használati minta.
