---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 79459be30a5a2018dc82486a84895b1a954941bc
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114333"
---
1. Az a [az Azure portal](https://portal.azure.com/)válassza **összes tallózása** > **App Services**. Ezután válassza ki a Mobile Apps háttérrendszerre. A **beállítások**válassza **App Service Push**. Ezután válassza ki az értesítési központ nevére.
2. Lépjen a **Windows (WNS)**. Majd adja meg a **biztonsági kulcs** (ügyfélkulcs) és **csomag biztonsági azonosítója** a Live Services webhely beszerzett. Majd **mentése**.

    ![Állítsa be a WNS-kulcs a portálon](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

A háttérrendszer konfigurálva van leküldéses értesítések küldése a WNS használatával.
