---
title: Docker-lekérés a Nyelvfelismerés tárolóhoz
titleSuffix: Azure Cognitive Services
description: Docker-lekérési parancs Nyelvfelismerés tárolóhoz
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 23669a5440799ff071b3a30e3e41ab5d49f212df
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966662"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Docker-lekérés a Nyelvfelismerés tárolóhoz

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal tölthet le egy tároló rendszerképet a Microsoft Container Registryból.

A Text Analytics tárolók számára elérhető címkék teljes leírását a Docker hub [nyelvfelismerés](https://go.microsoft.com/fwlink/?linkid=2018759) tárolójában találja.

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
