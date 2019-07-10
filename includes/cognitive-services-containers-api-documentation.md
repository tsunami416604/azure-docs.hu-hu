---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 00cc63f53388ab7bea05a0b55784247f63477684
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704303"
---
## <a name="validate-that-a-container-is-running"></a>Ellenőrizze, hogy a tároló fut-e. 

Többféleképpen is lehet ellenőrizni, hogy a tároló fut-e. 

|Kérés|Cél|
|--|--|
|`http://localhost:5000/`|A tároló kezdőlap biztosít.|
|`http://localhost:5000/status`|GET, a kért ellenőrzése, hogy a tároló fut-e anélkül, hogy ez egy végpont lekérdezést. Ezt a kérelmet a Kubernetes esetében használható [liveness és a készültségi vizsgálatok](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).|
|`http://localhost:5000/swagger`|A tároló biztosít teljes körű dokumentációja a végpontok és a egy `Try it now` funkció. Ezzel a funkcióval az beállítások megadása HTML webes űrlapon, és győződjön meg arról, a lekérdezés, kód írása nélkül. A lekérdezés adja vissza, miután egy példaparancs a CURL biztosított szükséges a HTTP-fejlécek és törzs szövegformátumot bemutatása. |

![Tároló kezdőlapja](./media/cognitive-services-containers-api-documentation/container-webpage.png)
