---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: bb03e4b5b04a0272d8fa9b032da5adb50878b620
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42811620"
---
1. Az a [az Azure portal](https://portal.azure.com/), kattintson a **összes tallózása** > **App Services**, majd kattintson a Mobile Apps-háttéralkalmazást. A **beállítások**, kattintson a **App Service Push**, majd kattintson az értesítési központ nevére.
2. Lépjen a **Google (GCM)**, adja meg a **Kiszolgálókulcs** érték, amely az előző eljárásban Firebase szerzett, és kattintson a **mentése**.

    ![Állítsa be az API-kulcsot a portálon](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

A Mobile Apps-háttéralkalmazás most Firebase Cloud Messaging használatára van konfigurálva. Ez lehetővé teszi, hogy az alkalmazás Android-eszközön fut, az értesítési központ használatával leküldéses értesítések küldéséhez.
