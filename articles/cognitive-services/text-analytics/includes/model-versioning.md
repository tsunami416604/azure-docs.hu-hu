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
ms.openlocfilehash: 29850cb9cb40eae0829b5d8c2b58b5f9518f18d5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021105"
---
A Text Analytics API 3. verziója lehetővé teszi az adatain használt Text Analytics modell kiválasztását. A választható `model-version` paraméterrel kiválaszthatja a modell egy verzióját a kérelmekben. Ha ez a paraméter nincs megadva, az API alapértelmezett értéke `latest`, a legújabb stabil modell verziója.

Elérhető modell-verziók:
* `2019-10-01` (`latest`)

A v3 végpontok minden válasza tartalmaz egy `model-version` mezőt, amely megadja a modell használt verzióját.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
