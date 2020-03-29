---
title: Docker-lekérésea a Language Detection tárolóhoz
titleSuffix: Azure Cognitive Services
description: Docker lekéréseparancs a Language Detection tárolóhoz
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 23669a5440799ff071b3a30e3e41ab5d49f212df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966662"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Docker-lekérésea a Language Detection tárolóhoz

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal tárolólemezképet tölthet le a Microsoft Container Registry rendszerből.

A Text Analytics-tárolók elérhető címkéinek teljes leírását a Docker-központban található [Nyelvészlelési](https://go.microsoft.com/fwlink/?linkid=2018759) tároló ban található.

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
