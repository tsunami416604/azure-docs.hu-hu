---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 852a3b00e8513f9ce68c5aae54c974505d0c9af6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179617"
---
1. Macen indítsa el a **Kulcskarika-hozzáférést.** A bal oldali navigációs sáv **Kategória területén**nyissa meg a Saját **tanúsítványok ablakot.** Keresse meg az előző szakaszban letöltött SSL-tanúsítványt, majd tegye közzé annak tartalmát. Csak a tanúsítványt jelölje ki (ne jelölje ki a személyes kulcsot). Ezután [exportálja](https://support.apple.com/kb/PH20122?locale=en_US).
2. Az [Azure Portalon](https://portal.azure.com/)válassza az > Összes**alkalmazásszolgáltatás** **tallózása**lehetőséget. Ezután válassza ki a mobilalkalmazások háttértartalékát. 
3. A **Beállítások csoportban**válassza **az App Service Push**lehetőséget. Ezután válassza ki az értesítési központ nevét. 
4. Nyissa meg az **Apple leküldéses értesítési szolgáltatások** > **feltöltési tanúsítványát.** Töltse fel a .p12 fájlt, és válassza ki a megfelelő **módot** (attól függően, hogy az ügyfél korábbi SSL-tanúsítványa éles vagy homokozó). Mentse a módosításokat.

A szolgáltatás most úgy van beállítva, hogy az iOS leküldéses értesítéseivel működjön.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
