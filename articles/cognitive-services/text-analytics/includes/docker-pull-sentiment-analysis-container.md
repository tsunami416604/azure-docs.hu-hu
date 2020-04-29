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
ms.openlocfilehash: e502b886c8031174180d40f1c5b7e373a974ee8e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877068"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Docker-lekérés a Hangulatelemzés tárolóhoz

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal tölthet le egy tároló rendszerképet a Microsoft Container Registryból.

A Text Analytics tárolók számára elérhető címkék teljes leírását a Docker hub [Hangulatelemzés](https://go.microsoft.com/fwlink/?linkid=2018654) tárolójában találja.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
