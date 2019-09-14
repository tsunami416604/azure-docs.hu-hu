---
title: Docker-lekérés a Hangulatelemzés tárolóhoz
titleSuffix: Azure Cognitive Services
description: Docker-lekérési parancs Hangulatelemzés tárolóhoz
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: df00d469052fa30c3f2aaa5afe1881ef74587f9a
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966678"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Docker-lekérés a Hangulatelemzés tárolóhoz

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal tölthet le egy tároló rendszerképet a Microsoft Container Registryból.

A Text Analytics tárolók számára elérhető címkék teljes leírását a Docker hub [Hangulatelemzés](https://go.microsoft.com/fwlink/?linkid=2018654) tárolójában találja.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
