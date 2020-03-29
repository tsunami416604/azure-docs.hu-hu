---
title: Docker-lekérésea a kulcskifejezés-kivonási tárolóhoz
titleSuffix: Azure Cognitive Services
description: Docker-lekéréseparancs a kulcskifejezés-kivonási tárolóhoz
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 59313528e4beb343f9ac1103d3f9f2faae5dfc16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966745"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker-lekérésea a kulcskifejezés-kivonási tárolóhoz

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal tárolólemezképet tölthet le a Microsoft Container Registry rendszerből.

A Text Analytics-tárolók elérhető címkéinek teljes leírását a Docker-központban található [key phrase extraction](https://go.microsoft.com/fwlink/?linkid=2018757) tárolóban található.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
