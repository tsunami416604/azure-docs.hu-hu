---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 8b3d856d255968b4a6736db908ce3999cbd56193
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108931"
---
## <a name="validate-that-a-container-is-running"></a>Annak ellenőrzése, hogy a tároló fut-e 

Több módon is ellenőrizheti, hogy a tároló fut-e. Keresse meg a kérdéses tároló *külső IP-* címét és a hozzá tartozó portot, és nyissa meg a kedvenc webböngészőjét. Az alábbi kérelmek URL-címeivel ellenőrizheti, hogy a tároló fut-e. Az alábbi példa a kérelem URL-címeit tartalmazza `http://localhost:5000` , de az adott tároló eltérő lehet. Ne feledje, hogy a tároló *külső IP-* címére és az elérhető portra támaszkodik.

| URL-cím kérése | Szerep |
|--|--|
| `http://localhost:5000/` | A tároló egy kezdőlapot biztosít. |
| `http://localhost:5000/ready` | A GET használatával a rendszer ellenőrzi, hogy a tároló készen áll-e a modellre irányuló lekérdezés elfogadására.  Ez a kérelem az Kubernetes [és készültségi](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)tesztekhez használható. |
| `http://localhost:5000/status` | A GET-mel is kérték, ezzel ellenőrzi, hogy a tároló indításához használt API-kulcs érvényes-e végponti lekérdezés nélkül. Ez a kérelem az Kubernetes [és készültségi](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)tesztekhez használható. |
| `http://localhost:5000/swagger` | A tároló teljes körű dokumentációt biztosít a végpontokhoz és a **kipróbálható** funkciókhoz. Ezzel a funkcióval megadhatja a beállításokat egy webalapú HTML-űrlapon, és anélkül teheti meg a lekérdezést, hogy kódot kellene írnia. A lekérdezés visszaadása után egy példa CURL-parancs van megadva a szükséges HTTP-fejlécek és-szövegtörzs bemutatásához. |

![Tároló kezdőlapja](./media/cognitive-services-containers-api-documentation/container-webpage.png)
