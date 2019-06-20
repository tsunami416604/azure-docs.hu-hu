---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 51a75ee7bf87c38e3916bdbc8d85abcfb14dca8b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179624"
---
1. Az a [az Azure portal](https://portal.azure.com/), kattintson a **összes tallózása** > **App Services**, majd kattintson a Mobile Apps-háttéralkalmazást. A **beállítások**, kattintson a **App Service Push**, majd kattintson az értesítési központ nevére.
2. Lépjen a **Google (GCM)** , adja meg a **Kiszolgálókulcs** érték, amely az előző eljárásban Firebase szerzett, és kattintson a **mentése**.

    ![Állítsa be az API-kulcsot a portálon](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

A Mobile Apps-háttéralkalmazás most Firebase Cloud Messaging használatára van konfigurálva. Ez lehetővé teszi, hogy az alkalmazás Android-eszközön fut, az értesítési központ használatával leküldéses értesítések küldéséhez.
