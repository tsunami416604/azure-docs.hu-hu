---
title: Tároló futtatási példa a docker-futtatás iparancsra
titleSuffix: Azure Cognitive Services
description: Docker futtatási parancs a Language Detection tárolóhoz
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: d2818336de2b3d4d810932cefb21edd95b8cf733
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966728"
---
A *Language Detection* tároló futtatásához `docker run` hajtsa végre a következő parancsot.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* *Nyelvfelismerési* tároló futtatása a tárolórendszerképből
* Egy PROCESSZORmag és 4 gigabájt (GB) memória lefoglalása
* Kiteszi az 5000-es TCP-portot, és pszeudo-TTY-t rendel a tárolóhoz
* A tároló automatikus eltávolítása kilépés után. A tárolórendszerkép továbbra is elérhető a gazdaszámítógépen.