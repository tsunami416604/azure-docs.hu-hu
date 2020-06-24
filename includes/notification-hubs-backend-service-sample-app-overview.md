---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 5c3277394350036b8863185e83b7cfcd4975be1c
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081417"
---
Lehetővé teszi az értesítési központ regisztrálását és regisztrációját a létrehozott háttér-szolgáltatáson keresztül. 

Riasztás jelenik meg, ha meg van adva egy művelet, és az alkalmazás az előtérben található. Ellenkező esetben az értesítések az értesítési központban jelennek meg.

> [!NOTE]
> Általában az alkalmazás életciklusának megfelelő pontján (vagy az első futtatású élmény részeként) kell végrehajtania a regisztrációt (és a regisztrációt) az explicit felhasználói regisztráció/regisztráció nélkül. Ebben a példában azonban explicit felhasználói adatbevitelre van szükség, hogy ezt a funkciót könnyebben lehessen feltárni és tesztelni.
