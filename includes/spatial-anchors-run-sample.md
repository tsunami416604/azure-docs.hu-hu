---
author: shreyasharma
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 10/23/2019
ms.author: shreshar
ms.openlocfilehash: 68dda321f0088a28f7f3ad1a80a02473fe0e7379
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971347"
---
Hozzon létre egy horgonyt, és mentse a megosztási szolgáltatásba **& megosztási horgony létrehozásával**. A Return utasításban vissza kell kérnie az azonosítóját, amelyet a rendszer a megosztási szolgáltatásból való lekérésére használhat. Ezután futtathatja a második forgatókönyvet, **megkeresheti a megosztott horgonyt**akár az eszközről, akár egy másikat. 

A **megosztott horgony keresése**lehetőséggel megkeresheti a korábban megosztott horgonyokat a korábban említett azonosító beírásával. A forgatókönyv kiválasztása után az alkalmazás további utasításokat is tartalmaz. A rendszer például arra kéri, hogy helyezze át az eszközt a környezeti információk összegyűjtéséhez. Később egy horgonyt helyezünk el a világba, várja meg a mentést, indítson el egy új munkamenetet, majd keresse meg.