---
title: Docker-lekérésea a Hangulatelemzés tárolóhoz
titleSuffix: Azure Cognitive Services
description: Docker lekéréseparancs a Hangulatelemzés tárolóhoz
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e502b886c8031174180d40f1c5b7e373a974ee8e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877068"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Docker-lekérésea a Hangulatelemzés tárolóhoz

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal tárolólemezképet tölthet le a Microsoft Container Registry rendszerből.

A Text Analytics-tárolók elérhető címkéinek teljes leírását a Docker-központ [Hangulatelemzési](https://go.microsoft.com/fwlink/?linkid=2018654) tárolójában található.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
