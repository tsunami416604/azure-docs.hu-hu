---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 63c54f8af91b6b4a76ba49d5e6fc7b3cda9f5b98
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240310"
---
1. Nyissa meg a projektet az **Android Studio** **Import project (Eclipse ADT, Gradle, etc.)** (Projekt importálása (Eclipse ADT, Gradle stb.)) lehetőségével. A JDK-hibák elkerülése érdekében győződjön meg arról, hogy ez az importálási lehetőség van kiválasztva.

2. Nyissa meg a fájlt `ToDoActivity.java` ebben a mappában - ZUMOAPPNAME/app/src/main/java/com/példa/zumoappname. Az alkalmazás neve `ZUMOAPPNAME`.

3. Nyissa meg a [az Azure portal](https://portal.azure.com/) , és keresse meg a mobilalkalmazás, amelyet Ön hozott létre. Az a `Overview` panelen keresse meg az URL-címet, amely a nyilvános végpont számára. Például: a sitename for my app name "test123" lesz https://test123.azurewebsites.net.

4. A `onCreate()` metódus, cserélje le `ZUMOAPPURL` nyilvános végpontot a fenti paraméterrel.
    
    `new MobileServiceClient("ZUMOAPPURL", this).withFilter(new ProgressFilter());` 
    
    válik
    
    `new MobileServiceClient("https://test123.azurewebsites.net", this).withFilter(new ProgressFilter());`
    
5. Nyomja le a **Run 'app'** („Alkalmazás” futtatása) gombot a projekt felépítéséhez és az alkalmazás Android-szimulátorban történő elindításához.

4. Az alkalmazásban írjon be egy értelmes szöveget, például *Az oktatóanyag befejezése*, majd kattintson a Hozzáadás gombra. Ez egy POST kérést küld a korábban telepített Azure-háttérrendszernek. A háttérrendszer beilleszti az adatokat a kérésből a TodoItem SQL táblázatba, majd visszaküldi az újonnan eltárolt adatokra vonatkozó információkat a mobilalkalmazásnak. A mobilalkalmazás megjeleníti ezeket az adatokat a listában.
    ![A rövid útmutató Android](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)