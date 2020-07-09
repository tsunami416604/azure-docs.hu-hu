---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 7c3887367b6365ea3577bbff6cc19bf34d178ee6
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081433"
---
### <a name="create-a-notification-hub"></a>Értesítési központ létrehozása 

Ebben a szakaszban egy értesítési központot hoz létre, és konfigurálja a hitelesítést a **APNS**. Használhat P12 push-tanúsítványt vagy jogkivonat-alapú hitelesítést. Ha egy már létrehozott értesítési központot szeretne használni, ugorjon az 5. lépésre.

1. Jelentkezzen be az [Azure](https://portal.azure.com)-ba.

1. Kattintson az **erőforrás létrehozása**elemre, majd keresse meg és válassza az **értesítési központ**elemet, majd kattintson a **Létrehozás**gombra.

1. Frissítse a következő mezőket, majd kattintson a **Létrehozás**gombra:

    **ALAPSZINTŰ RÉSZLETEK**  

    **Előfizetés:** Válassza ki a cél **előfizetést** a legördülő listából.  
    **Erőforráscsoport:** Hozzon létre egy új **erőforráscsoportot** (vagy válasszon ki egy meglévőt)  

    **NÉVTÉR RÉSZLETEI**  

    **Értesítési központ névtere:** Adja meg az **értesítési központ** névterének globálisan egyedi nevét  

    > [!NOTE]
    > Győződjön meg arról, hogy az **új létrehozása** lehetőség ki van választva ehhez a mezőhöz.

    **ÉRTESÍTÉSI KÖZPONT – RÉSZLETEK**  

    **Értesítési központ:** Adja meg az **értesítési központ** nevét  
    **Hely:** Válasszon ki egy megfelelő helyet a legördülő listából.  
    **Árképzési szintek:** Az alapértelmezett **ingyenes** lehetőség megtartása  

    > [!NOTE]
    > Hacsak nem érte el az ingyenes szinten lévő hubok maximális számát.

1. Az **értesítési központ** üzembe helyezése után navigáljon az adott erőforráshoz.
1. Navigáljon az új **értesítési**központhoz.
1. A listáról válassza a **hozzáférési szabályzatok** lehetőséget (a **kezelés**alatt).
1. Jegyezze fel a **szabályzat nevének** értékeit, valamint a hozzájuk tartozó **kapcsolatok karakterlánc** -értékeit.
