---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: d73b3e0938d278e53648a6af7151cecb4ae67c70
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185384"
---
Hozzon létre egy horgonyt, és mentse a megosztási szolgáltatásba **& megosztási horgony létrehozásával**. A Return utasításban vissza kell kérnie az azonosítóját, amelyet a rendszer a megosztási szolgáltatásból való lekérésére használhat. Ezután futtathatja a második forgatókönyvet, **megkeresheti a megosztott horgonyt** akár az eszközről, akár egy másikat. 

A **megosztott horgony keresése** lehetőséggel megkeresheti a korábban megosztott horgonyokat a korábban említett azonosító beírásával. A forgatókönyv kiválasztása után az alkalmazás további utasításokat is tartalmaz. A rendszer például arra kéri, hogy helyezze át az eszközt a környezeti információk összegyűjtéséhez. Később egy horgonyt helyezünk el a világba, várja meg a mentést, indítson el egy új munkamenetet, majd keresse meg.