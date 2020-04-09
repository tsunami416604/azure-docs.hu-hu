---
title: Docker-lekérésea a kulcskifejezés-kivonási tárolóhoz
titleSuffix: Azure Cognitive Services
description: Docker-lekéréseparancs a kulcskifejezés-kivonási tárolóhoz
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: db5d0cfecde32d3dd6afe181d67a6ce6c0826eda
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877128"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker-lekérésea a kulcskifejezés-kivonási tárolóhoz

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal tárolólemezképet tölthet le a Microsoft Container Registry rendszerből.

A Text Analytics-tárolók elérhető címkéinek teljes leírását a Docker-központban található [key phrase extraction](https://go.microsoft.com/fwlink/?linkid=2018757) tárolóban található.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
