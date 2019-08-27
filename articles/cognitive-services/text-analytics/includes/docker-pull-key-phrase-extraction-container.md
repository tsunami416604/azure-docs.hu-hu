---
title: Docker-lekérés a Kulcsszókeresés tárolóhoz
titleSuffix: Azure Cognitive Services
description: Docker-lekérési parancs Kulcsszókeresés tárolóhoz
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: e71db5455a3d6726cd6567d568a13fcdcf9cb520
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051331"
---
## <a name="pull-the-key-phrase-extraction-container"></a>A Kulcsszókeresés tároló lekérése

A Text Analytics tároló lemezképei a Microsoft Container Registry érhetők el.

| Tároló | Container Registry/adattár/rendszerkép neve |
|-----------|------------|
| Kulcskifejezések kinyerése | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal tölthet le egy tároló rendszerképet a Microsoft Container Registryból.

A Text Analytics tárolók számára elérhető címkék teljes leírását a Docker hub [kulcsszókeresés](https://go.microsoft.com/fwlink/?linkid=2018757) tárolójában találja.

### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker-lekérés a Kulcsszókeresés tárolóhoz

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
