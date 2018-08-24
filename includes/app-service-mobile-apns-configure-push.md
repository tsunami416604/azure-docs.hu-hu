---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 25bb5cfb0073f7533faddec43b38fb5031b82a43
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42811637"
---
1. A Mac számítógépén indítsa el a **Keychain Access**. A bal oldali navigációs sávon a **kategória**, nyissa meg **saját tanúsítványok**. Keresse meg az SSL-tanúsítványt, amelyet az előző szakaszban töltött le, és majd kiadni a tartalmát. Válassza ki a tanúsítványt csak (ne válassza a titkos kulcsot). Ezután [exportálja](https://support.apple.com/kb/PH20122?locale=en_US).
2. Az a [az Azure portal](https://portal.azure.com/)válassza **összes tallózása** > **App Services**. Ezután válassza ki a Mobile Apps háttérrendszerre. 
3. A **beállítások**válassza **App Service Push**. Ezután válassza ki az értesítési központ nevére. 
4. Lépjen a **Apple leküldéses értesítési szolgáltatások** > **-tanúsítvány feltöltése**. Töltse fel a .p12 fájlt, és kiválasztja a megfelelő **mód** (attól függően, hogy az SSL-ügyféltanúsítvány a korábban az éles, akár a védőfal). Mentse a módosításokat.

A szolgáltatás most már konfigurálva van leküldéses értesítések IOS-eszközökön.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
