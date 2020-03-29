---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 081155c8984f39cc9cc8e905eb108c07dee98d2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70034432"
---
## <a name="validate-that-a-container-is-running"></a>Annak ellenőrzése, hogy fut-e egy tároló 

A tároló futásának számos módja van. Keresse meg a kérdéses tároló *külső IP-címét* és kitett portját, és nyissa meg kedvenc webböngészőjét. Használja az alábbi különböző kérelem URL-címeket a tároló futásának ellenőrzéséhez. Az alábbi példakérési URL-címek a következők, `http://localhost:5000`de az adott tároló eltérő lehet. Ne feledje, hogy a tároló külső *IP-címére* és a szabadon elérhető portra kell támaszkodnia.

| Kérés URL-címe | Cél |
|--|--|
| `http://localhost:5000/` | A tároló kezdőlapot biztosít. |
| `http://localhost:5000/status` | Http GET-vel kért, ellenőrizze, hogy a tároló fut-e végpontlekérdezés okozása nélkül. Ez a kérés kubernetes [élési és készenléti szondákhoz](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)használható. |
| `http://localhost:5000/swagger` | A tároló a végpontok teljes dokumentációkészletét és a **Try it** funkciót biztosítja. Ezzel a funkcióval megadhatja a beállításokat egy webalapú HTML-űrlapon, és anélkül, hogy kódot kellene írnia. Miután a lekérdezés visszatér, egy példa CURL parancs jelenik meg a HTTP-fejlécek és a törzs formátuma, amely szükséges. |

![A tároló kezdőlapja](./media/cognitive-services-containers-api-documentation/container-webpage.png)
