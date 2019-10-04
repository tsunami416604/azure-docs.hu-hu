---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 081155c8984f39cc9cc8e905eb108c07dee98d2e
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034432"
---
## <a name="validate-that-a-container-is-running"></a>Annak ellenőrzése, hogy a tároló fut-e 

Több módon is ellenőrizheti, hogy a tároló fut-e. Keresse meg a kérdéses tároló *külső IP-* címét és a hozzá tartozó portot, és nyissa meg a kedvenc webböngészőjét. Az alábbi kérelmek URL-címeivel ellenőrizheti, hogy a tároló fut-e. Az alábbi `http://localhost:5000`példa a kérelem URL-címeit tartalmazza, de az adott tároló eltérő lehet. Ne feledje, hogy a tároló *külső IP-* címére és az elérhető portra támaszkodik.

| Kérés URL-címe | Cél |
|--|--|
| `http://localhost:5000/` | A tároló egy kezdőlapot biztosít. |
| `http://localhost:5000/status` | HTTP GET-kéréssel, annak ellenőrzéséhez, hogy a tároló fut-e, és nem okoz-e végponti lekérdezést. Ez a kérelem az Kubernetes [és készültségi](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)tesztekhez használható. |
| `http://localhost:5000/swagger` | A tároló teljes körű dokumentációt biztosít a végpontokhoz és a kipróbálható funkciókhoz. Ezzel a funkcióval megadhatja a beállításokat egy webalapú HTML-űrlapon, és anélkül teheti meg a lekérdezést, hogy kódot kellene írnia. A lekérdezés visszaadása után egy példa CURL-parancs van megadva a szükséges HTTP-fejlécek és-szövegtörzs bemutatásához. |

![Tároló kezdőlapja](./media/cognitive-services-containers-api-documentation/container-webpage.png)
