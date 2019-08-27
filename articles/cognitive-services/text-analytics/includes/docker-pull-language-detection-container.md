---
title: Docker-lekérés a Nyelvfelismerés tárolóhoz
titleSuffix: Azure Cognitive Services
description: Docker-lekérési parancs Nyelvfelismerés tárolóhoz
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: da6f04f1042d1a02178f345c5fe67387ae0a7d0f
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051278"
---
## <a name="pull-the-language-detection-container"></a>A Nyelvfelismerés tároló lekérése

A Text Analytics tároló lemezképei a Microsoft Container Registry érhetők el.

| Tároló | Container Registry/adattár/rendszerkép neve |
|-----------|------------|
| Nyelvfelismerés | `mcr.microsoft.com/azure-cognitive-services/language` |

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal tölthet le egy tároló rendszerképet a Microsoft Container Registryból.

A Text Analytics tárolók számára elérhető címkék teljes leírását a Docker hub [nyelvfelismerés](https://go.microsoft.com/fwlink/?linkid=2018759) tárolójában találja.


### <a name="docker-pull-for-the-language-detection-container"></a>Docker-lekérés a Nyelvfelismerés tárolóhoz

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
