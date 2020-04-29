---
title: Tároló futtatása példa a Docker Run parancsra
titleSuffix: Azure Cognitive Services
description: Docker-futtatási parancs Kulcsszókeresés tárolóhoz
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 8ed42d5b0461b6273c2f8d84a267b65461d160ef
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877111"
---
A *kulcsszókeresés* tároló futtatásához hajtsa végre a `docker run` következő parancsot.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* *Kulcsszókeresés* tárolót futtat a tároló rendszerképből
* Egy CPU mag és 4 gigabájt (GB) memóriát foglal le
* Elérhetővé teszi a 5000-es TCP-portot, és kiosztja a tárolóhoz tartozó pszeudo-TTY-t
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.