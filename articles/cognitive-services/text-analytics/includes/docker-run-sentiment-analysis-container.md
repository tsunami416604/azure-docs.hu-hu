---
title: Tároló futtatási példa a docker-futtatás iparancsra
titleSuffix: Azure Cognitive Services
description: A Docker futtatása parancs a Hangulatelemzés tárolóhoz
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e67f65d252be0ea638d3b5fa241d9413e76f1a98
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877051"
---
A *Hangulatelemzés* tároló futtatásához `docker run` hajtsa végre a következő parancsot.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/sentiment \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* *Hangulatelemzési* tárolófuttatása a tárolórendszerképből
* Egy PROCESSZORmag és 4 gigabájt (GB) memória lefoglalása
* Kiteszi az 5000-es TCP-portot, és pszeudo-TTY-t rendel a tárolóhoz
* A tároló automatikus eltávolítása kilépés után. A tárolórendszerkép továbbra is elérhető a gazdaszámítógépen.