---
title: Modellverziószámozás
titleSuffix: Azure Cognitive Services
description: Modellverziók megadása a V3-végpontokban
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 4cd8d0901ce23fb227bb6919dee18f5aa3d063ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77089000"
---
A Text Analytics API 3-as verziója lehetővé teszi az adatokhoz legaktuálisabb modellverzió kiválasztását. A választható `model-version` paraméterrel válassza ki a modell nek a kérésekhez szükséges verzióját. Ha ez a paraméter nincs megadva, `latest`az API alapértelmezés szerint a legújabb stabil verzióra kerül. Annak ellenére, hogy a legújabb modellverzió bármely kérelemben használható, minden verzióban csak néhány szolgáltatás frissül. Az alábbi táblázat leírja, hogy mely funkciók frissültek az egyes modellverziókban:

| Modell verziószáma           | Frissített funkciók         | Legújabb verzió:           |
|-------------------------|--------------------------|--------------------------|
| `2020-02-01`            | Entitások felismerése                      | Entitások felismerése                      |
| `2019-10-01`            | Entitás felismerése, hangulatelemzés  | Nyelvfelismerés, Kulcskifejezések kinyerése, Hangulatelemzés|


A v3-végpontok minden `model-version` válasza tartalmaz egy mezőt, amely meghatározza a használt modellverziót.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
Az [újdonságok](../whats-new.md) című témakörben talál részleteket a modellverziók frissítéseiről.
