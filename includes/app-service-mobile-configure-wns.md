---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 79459be30a5a2018dc82486a84895b1a954941bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179618"
---
1. Az [Azure Portalon](https://portal.azure.com/)válassza az > Összes**alkalmazásszolgáltatás** **tallózása**lehetőséget. Ezután válassza ki a mobilalkalmazások háttértartalékát. A **Beállítások csoportban**válassza **az App Service Push**lehetőséget. Ezután válassza ki az értesítési központ nevét.
2. Tovább a **Windows (WNS)**. Ezután adja meg a **Biztonsági kulcsot** (ügyféltitkos kulcsot) és a Live Services webhelyről beszerzett csomag **biztonsági azonosítóját.** Ezután válassza a **Mentés gombot.**

    ![A WNS-kulcs beállítása a portálon](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

A háttérfiók most úgy van beállítva, hogy a WNS használatával küldjön leküldéses értesítéseket.
