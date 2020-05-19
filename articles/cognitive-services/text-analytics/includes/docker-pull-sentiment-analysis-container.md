---
title: Docker-lekérés a Hangulatelemzés tárolóhoz
titleSuffix: Azure Cognitive Services
description: Docker-lekérési parancs Hangulatelemzés tárolóhoz
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a3db0e2ffdd4a75f02634ca2227c3c41416d4f65
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588388"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Docker-lekérés a Hangulatelemzés v3 tárolóhoz

Az érzelmek elemzése tároló v3 tárolója több nyelven is elérhető. Az angol tárolóhoz tartozó tároló letöltéséhez használja az alábbi parancsot. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en
```

Egy másik nyelvhez tartozó tároló letöltéséhez cserélje le az `en` alábbi nyelvi kódok egyikét. 

| Text Analytics tároló | Nyelvkód |
|--|--|
| Angol | `en` |
| Spanyol | `es` |
| Francia | `fr` |
| Olasz | `it` |
| Német | `de` |
| Egyszerűsített kínai | `zh` |
| Kínai – hagyományos | `zht` |
| Japán | `ja` |
| Portugál | `pt` |
| Holland | `nl` |

A Text Analytics tárolók számára elérhető címkék teljes leírását lásd: [Docker hub](https://go.microsoft.com/fwlink/?linkid=2018654).