---
author: shreyasharma
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 10/23/2019
ms.author: shreshar
ms.openlocfilehash: 38a0ad9ecd7a2a9f1e14be6e4b0d1023a581a675
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91358767"
---
**& megosztási horgony létrehozásával** létrehozhat egy horgonyt, és mentheti a megosztási szolgáltatásba. A Return utasításban vissza kell kérnie az azonosítóját, amelyet a rendszer a megosztási szolgáltatásból való lekérésére használhat. Ezután futtathatja a második forgatókönyvet, **megkeresheti a megosztott horgonyt**, akár az eszközről, akár egy másikat. A **megosztott horgony** lehetőséggel megkeresheti a korábban megosztott horgonyokat a korábban említett azonosító beírásával. A forgatókönyv kiválasztását követően az alkalmazás további útmutatást nyújt a teendők megoldásához. A rendszer például arra kéri, hogy helyezze át az eszközt a környezeti információk összegyűjtéséhez. Később egy horgonyt helyezünk el a világba, várja meg a mentést, indítson el egy új munkamenetet, majd keresse meg.
