---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2019
ms.openlocfilehash: f4925401235aedb341a7e29ca36b079126647f7b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66124337"
---
## <a name="validate-that-a-container-is-running"></a>Ellenőrizze, hogy a tároló fut-e. 

Többféleképpen is lehet ellenőrizni, hogy a tároló fut-e. 

|Kérés|Cél|
|--|--|
|`http://localhost:5000/`|A tároló kezdőlap biztosít.|
|`http://localhost:5000/status`|GET, a kért ellenőrzése, hogy a tároló fut-e anélkül, hogy ez egy végpont lekérdezést. Ezt a kérelmet a Kubernetes esetében használható [liveness és a készültségi vizsgálatok](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).|
|`http://localhost:5000/swagger`|A tároló biztosít teljes körű dokumentációja a végpontok és a egy `Try it now` funkció. Ezzel a funkcióval az beállítások megadása HTML webes űrlapon, és győződjön meg arról, a lekérdezés, kód írása nélkül. A lekérdezés adja vissza, miután egy példaparancs a CURL biztosított szükséges a HTTP-fejlécek és törzs szövegformátumot bemutatása. |

![Tároló kezdőlapja](./media/cognitive-services-containers-api-documentation/container-webpage.png)
