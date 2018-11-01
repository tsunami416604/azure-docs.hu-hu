---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 505eac0996129a17b6b68e8ab4ea2d4fc80fd473
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134422"
---
1. Látogasson el az [Azure Portal]. Kattintson az **Összes tallózása** > **Mobilalkalmazások** lehetőségre > az imént létrehozott háttérrendszerre. A mobilalkalmazás beállításaiban kattintson az **Első lépések** > **Android** elemre. Az **Ügyfélalkalmazás konfigurálása** menüben kattintson a **Letöltés** elemre. Ez letölti egy olyan alkalmazás teljes Android-projektjét, amely előre konfigurálva van a háttérkészlethez való csatlakozáshoz. 
2. Nyissa meg a projektet az **Android Studio** **Import project (Eclipse ADT, Gradle, etc.)** (Projekt importálása (Eclipse ADT, Gradle stb.)) lehetőségével. A JDK-hibák elkerülése érdekében győződjön meg arról, hogy ez az importálási lehetőség van kiválasztva.
3. Nyomja le a **Run 'app'** („Alkalmazás” futtatása) gombot a projekt felépítéséhez és az alkalmazás Android-szimulátorban történő elindításához.
4. Az alkalmazásban írjon be egy értelmes szöveget, például *Az oktatóanyag befejezése*, majd kattintson a Hozzáadás gombra. Ez egy POST kérést küld a korábban telepített Azure-háttérrendszernek. A háttérrendszer beilleszti az adatokat a kérésből a TodoItem SQL táblázatba, majd visszaküldi az újonnan eltárolt adatokra vonatkozó információkat a mobilalkalmazásnak. A mobilalkalmazás megjeleníti ezeket az adatokat a listában. 
   
    ![](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)

[Azure Portal]: https://portal.azure.com/
