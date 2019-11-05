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
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 195c4c94f7ab00a8740c9dd14aad0d0fd0daa9b7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503975"
---
Ebben a rövid útmutatóban a [Batch átíró REST API](../../../batch-transcription.md) használatával ismeri fel a [sas-blobban](https://aka.ms/ignite2019/speech/placeholder)tárolt beszédet. Néhány előfeltétel kielégítése után a beszéd felismerése REST API csak néhány lépést vesz igénybe:
> [!div class="checklist"]
> * Küldje el a JSON-kérést a beszédfelismerési szolgáltatásnak a beszéd átírásának megkezdéséhez.
> * Az átírás állapotának ellenõrzése.
> * Ha elkészült, töltse le az átírási eredményeket.