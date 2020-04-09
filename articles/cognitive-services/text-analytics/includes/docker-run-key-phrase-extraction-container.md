---
title: Tároló futtatási példa a docker-futtatás iparancsra
titleSuffix: Azure Cognitive Services
description: Docker futtatása parancs a kulcskifejezés-kivonási tárolóhoz
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 8ed42d5b0461b6273c2f8d84a267b65461d160ef
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877111"
---
A *kulcskifejezés-kivonási* tároló `docker run` futtatásához hajtsa végre a következő parancsot.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* *Kulcskifejezés-kivonási* tárolót futtat a tárolórendszerképből
* Egy PROCESSZORmag és 4 gigabájt (GB) memória lefoglalása
* Kiteszi az 5000-es TCP-portot, és pszeudo-TTY-t rendel a tárolóhoz
* A tároló automatikus eltávolítása kilépés után. A tárolórendszerkép továbbra is elérhető a gazdaszámítógépen.