---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 75bcb9d27ee6f66a1d9c15093d9f933a3ad25881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179616"
---
1. A Visual Studio Solution Explorer programban kattintson a jobb gombbal a Windows Áruházbeli alkalmazásprojektre. Ezután válassza az **Áruházbeli** > **alkalmazás és az Áruház társítása lehetőséget.**

    ![Alkalmazás társítása a Windows Áruházhoz](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. A varázslóban válassza a **Tovább gombot.** Ezután jelentkezzen be Microsoft-fiókjával. Az **Új alkalmazás nevének lefoglalása**mezőbe írja be az alkalmazás nevét, majd válassza a **Foglalás**lehetőséget.
3. Az alkalmazás regisztrációjának sikeres létrehozása után válassza ki az új alkalmazás nevét. Válassza a **Tovább**lehetőséget, majd a **Társítás**lehetőséget. Ez a folyamat hozzáadja a windows Áruház beli regisztrációs adatokat az alkalmazásjegyzékhez.
4. Ismételje meg az 1.  
5. Nyissa meg a [Windows fejlesztői központot,](https://dev.windows.com/en-us/overview)és jelentkezzen be Microsoft-fiókjával. A **Saját alkalmazások**alkalmazásban válassza ki az új alkalmazásregisztrációt. Ezután bontsa ki **a Szolgáltatások** > **leküldéses értesítéseit**.
6. A **Leküldéses értesítések** lap **Windows leküldéses értesítési szolgáltatások (WNS) és a Microsoft Azure Mobile Apps csoportban**válassza a **Live Services webhely lehetőséget.**  Jegyezze fel a **csomag biztonsági azonosítójának** és az **alkalmazástitkos**kiszolgáló *aktuális* értékének értékét. 

    ![Alkalmazásbeállítás a fejlesztői központban](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > Az alkalmazáskulcs és a csomag biztonsági azonosítója fontos biztonsági hitelesítő adatok. Ne ossza meg ezeket az értékeket senkivel, és ne ossza szét őket az alkalmazással.
   >
   >
