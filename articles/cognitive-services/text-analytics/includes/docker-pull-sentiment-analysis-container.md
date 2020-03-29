---
title: Docker-lekérésea a Hangulatelemzés tárolóhoz
titleSuffix: Azure Cognitive Services
description: Docker lekéréseparancs a Hangulatelemzés tárolóhoz
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: df00d469052fa30c3f2aaa5afe1881ef74587f9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966678"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Docker-lekérésea a Hangulatelemzés tárolóhoz

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal tárolólemezképet tölthet le a Microsoft Container Registry rendszerből.

A Text Analytics-tárolók elérhető címkéinek teljes leírását a Docker-központ [Hangulatelemzési](https://go.microsoft.com/fwlink/?linkid=2018654) tárolójában található.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
