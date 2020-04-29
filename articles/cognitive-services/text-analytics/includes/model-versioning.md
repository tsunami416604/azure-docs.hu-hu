---
title: Modell verziószámozása
titleSuffix: Azure Cognitive Services
description: Modell-verziók meghatározása a v3-végpontokban
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 4cd8d0901ce23fb227bb6919dee18f5aa3d063ed
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "77089000"
---
A Text Analytics API 3. verziója lehetővé teszi az adataihoz legnaprakészebb modell verziójának kiválasztását. A választható `model-version` paraméter használatával kiválaszthatja a kérésekhez kívánt modell verzióját. Ha ez a paraméter nincs megadva, a rendszer alapértelmezés `latest`szerint az API-t, a legújabb stabil verziót adja meg. Annak ellenére, hogy bármely kérelemben használhatja a legújabb modell-verziót, csak néhány funkció frissül minden verzióban. Az alábbi táblázat ismerteti, hogy mely funkciók lettek frissítve az egyes modellek verzióiban:

| Modell verziója           | Frissített funkciók         | Legújabb verzió:           |
|-------------------------|--------------------------|--------------------------|
| `2020-02-01`            | Entitások felismerése                      | Entitások felismerése                      |
| `2019-10-01`            | Entitások felismerése, érzelmek elemzése  | Nyelvfelismerés, fő kifejezés kinyerése, hangulat elemzése|


A v3 végpontok minden válasza tartalmaz egy `model-version` mezőt, amely meghatározza a modell használt verzióját.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
A modellek frissítéseivel kapcsolatos további részletekért [tekintse meg a](../whats-new.md) újdonságokat.
