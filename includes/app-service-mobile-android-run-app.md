---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 63c54f8af91b6b4a76ba49d5e6fc7b3cda9f5b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240310"
---
1. Nyissa meg a projektet az **Android Studio****Import project (Eclipse ADT, Gradle, etc.)** (Projekt importálása (Eclipse ADT, Gradle stb.)) lehetőségével. A JDK-hibák elkerülése érdekében győződjön meg arról, hogy ez az importálási lehetőség van kiválasztva.

2. Nyissa meg `ToDoActivity.java` a fájlt ebben a mappában - ZUMOAPPNAME/app/src/main/java/com/example/zumoappname. Az alkalmazás `ZUMOAPPNAME`neve .

3. Nyissa meg az [Azure Portalon,](https://portal.azure.com/) és keresse meg a létrehozott mobilalkalmazást. A `Overview` panelen keresse meg a mobilalkalmazás nyilvános végpontját képező URL-címet. Példa - a sitename az én app neve https://test123.azurewebsites.net"test123" lesz .

4. A `onCreate()` metódusban `ZUMOAPPURL` cserélje le a paramétert a fenti nyilvános végpontra.
    
    `new MobileServiceClient("ZUMOAPPURL", this).withFilter(new ProgressFilter());` 
    
    Lesz
    
    `new MobileServiceClient("https://test123.azurewebsites.net", this).withFilter(new ProgressFilter());`
    
5. Nyomja le a **Run 'app'** („Alkalmazás” futtatása) gombot a projekt felépítéséhez és az alkalmazás Android-szimulátorban történő elindításához.

4. Az alkalmazásban írjon be egy értelmes szöveget, például *Az oktatóanyag befejezése*, majd kattintson a Hozzáadás gombra. Ez egy POST kérést küld a korábban telepített Azure-háttérrendszernek. A háttérrendszer beilleszti az adatokat a kérésből a TodoItem SQL táblázatba, majd visszaküldi az újonnan eltárolt adatokra vonatkozó információkat a mobilalkalmazásnak. A mobilalkalmazás megjeleníti ezeket az adatokat a listában.
    ![Gyors indítás Android](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)