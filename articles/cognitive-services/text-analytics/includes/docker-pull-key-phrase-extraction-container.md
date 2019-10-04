---
title: Docker-lekérés a Kulcsszókeresés tárolóhoz
titleSuffix: Azure Cognitive Services
description: Docker-lekérési parancs Kulcsszókeresés tárolóhoz
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 59313528e4beb343f9ac1103d3f9f2faae5dfc16
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966745"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker-lekérés a Kulcsszókeresés tárolóhoz

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal tölthet le egy tároló rendszerképet a Microsoft Container Registryból.

A Text Analytics tárolók számára elérhető címkék teljes leírását a Docker hub [kulcsszókeresés](https://go.microsoft.com/fwlink/?linkid=2018757) tárolójában találja.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
