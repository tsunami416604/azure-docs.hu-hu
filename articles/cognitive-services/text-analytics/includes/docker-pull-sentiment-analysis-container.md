---
title: Docker-lekérés a Hangulatelemzés tárolóhoz
titleSuffix: Azure Cognitive Services
description: Docker-lekérési parancs Hangulatelemzés tárolóhoz
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: 0954ef88f6917159156fbb73103b7b7af3283c00
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051409"
---
## <a name="pull-the-sentiment-analysis-container"></a>A Hangulatelemzés tároló lekérése

A Text Analytics tároló lemezképei a Microsoft Container Registry érhetők el.

| Tároló | Container Registry/adattár/rendszerkép neve |
|-----------|------------|
| Véleményelemzés | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal tölthet le egy tároló rendszerképet a Microsoft Container Registryból.

A Text Analytics tárolók számára elérhető címkék teljes leírását a Docker hub [Hangulatelemzés](https://go.microsoft.com/fwlink/?linkid=2018654) tárolójában találja.

### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Docker-lekérés a Hangulatelemzés tárolóhoz

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
