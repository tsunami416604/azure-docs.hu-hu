---
title: Tároló futtatása példa a Docker Run parancsra
titleSuffix: Azure Cognitive Services
description: Docker-futtatási parancs Kulcsszókeresés tárolóhoz
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: b8c3bdfc05e855139b595cd0ba7bd723cdeaee45
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966787"
---
A *kulcsszókeresés* tároló futtatásához hajtsa végre a `docker run` következő parancsot.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Ez a parancs:

* *Kulcsszókeresés* tárolót futtat a tároló rendszerképből
* Egy CPU mag és 4 gigabájt (GB) memóriát foglal le
* Elérhetővé teszi az 5000-es TCP-porton és a egy pszeudo-TTY lefoglalja a tároló
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.