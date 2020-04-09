---
title: Docker-lekérésea a Language Detection tárolóhoz
titleSuffix: Azure Cognitive Services
description: Docker lekéréseparancs a Language Detection tárolóhoz
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 3444d6267c09056697abf8339cc688a606b6dc17
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877108"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Docker-lekérésea a Language Detection tárolóhoz

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal tárolólemezképet tölthet le a Microsoft Container Registry rendszerből.

A Text Analytics-tárolók elérhető címkéinek teljes leírását a Docker-központban található [Nyelvészlelési](https://go.microsoft.com/fwlink/?linkid=2018759) tároló ban található.

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
