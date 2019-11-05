---
title: Modell verziószámozása
titleSuffix: Azure Cognitive Services
description: Modell-verziók meghatározása a v3-végpontokban
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/25/2019
ms.author: aahi
ms.openlocfilehash: 5a06e26e5f1640024e343c714db3df134422115c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488649"
---
A Text Analytics API 3. verziója lehetővé teszi az adatain használt Text Analytics modell kiválasztását. A választható `model-version` paraméterrel kiválaszthatja a modell egy verzióját a kérelmekben. Ha ez a paraméter nincs megadva, az API alapértelmezett értéke `latest`, a legújabb stabil modell verziója.

Elérhető modell-verziók:
* `2019-10-01` (`latest`)

A v3 végpontok minden válasza tartalmaz egy `model-version` mezőt, amely megadja a modell használt verzióját.

```json
{
    “documents”: […]
    “errors”: []
    “model-version”: “2019-10-01”
}
```
