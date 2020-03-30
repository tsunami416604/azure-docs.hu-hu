---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 51a75ee7bf87c38e3916bdbc8d85abcfb14dca8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179624"
---
1. Az [Azure Portalon](https://portal.azure.com/)kattintson az > Összes**alkalmazásszolgáltatás** **tallózása**elemre, majd a mobilalkalmazások háttértartalékára. A **Beállítások csoportban**kattintson **az App Service Leküldéses**gombra, majd az értesítési központ nevére.
2. Nyissa meg a **Google (GCM)** lapot, adja meg az előző eljárásban a Firebase-től kapott **kiszolgálókulcs** értékét, majd kattintson a **Mentés gombra.**

    ![Az API-kulcs beállítása a portálon](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

A mobilalkalmazások háttértartaléka most antól a Firebase Cloud Messaging használatára van konfigurálva. Ez lehetővé teszi, hogy leküldéses értesítéseket küldjön az Android-eszközön futó alkalmazásnak az értesítési központ használatával.
