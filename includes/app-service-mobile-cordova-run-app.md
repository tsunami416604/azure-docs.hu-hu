---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: 8d7731480b6239c572d39f52b6a0217d2ac48d25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240237"
---
1. Nyissa meg a megoldásfájlt az ügyfélprojektben (.sln), és nyissa meg a Visual Studio használatával.

2. A Visual Studióban válassza ki a megoldás platformját (Android, iOS vagy Windows) a start nyíl melletti legördülő menüből. Válassza ki a konkrét eszközt vagy emulátort a zöld nyílon található legördülő menüre kattintva. Használhatja az alapértelmezett Android platformot és a Ripple emulátort. Az összetettebb oktatóanyagok (pl. leküldéses értesítések) esetén egy támogatott eszközt és emulátort kell majd választania.

3. Nyissa meg `ToDoActivity.java` a fájlt ebben a mappában - ZUMOAPPNAME/app/src/main/java/com/example/zumoappname. Az alkalmazás `ZUMOAPPNAME`neve .

4. Nyissa meg az [Azure Portalon,](https://portal.azure.com/) és keresse meg a létrehozott mobilalkalmazást. A `Overview` panelen keresse meg a mobilalkalmazás nyilvános végpontját képező URL-címet. Példa - a sitename az én app neve https://test123.azurewebsites.net"test123" lesz .

5. Nyissa meg `index.js` a ZUMOAPPNAME/www/js/index.js `onDeviceReady()` fájlfájlját, és a metódusban cserélje le `ZUMOAPPURL` a paramétert a fenti nyilvános végpontra.

    `client = new WindowsAzure.MobileServiceClient('ZUMOAPPURL');`
    
    Lesz
    
    `client = new WindowsAzure.MobileServiceClient('https://test123.azurewebsites.net');`
    
6. A Cordova alkalmazás létrehozásához és futtatásához nyomja le az F5 billentyűt, vagy kattintson a zöld nyílra. Ha az emulátor egy biztonsági párbeszédablakot jelenít meg, amelyben hozzáférést kér a hálózathoz, fogadja el.

7. Miután az alkalmazás elindult az eszközön vagy az emulátoron, írja be az értelmes szöveget **az Új szöveg beírásába**, például *Fejezze be az oktatóanyagot,* majd kattintson a **Hozzáadás** gombra.

A háttérrendszer beszúrja az adatokat a kérésből az SQL Database-ben található TodoItem táblába, majd visszaküldi az újonnan tárolt elemekre vonatkozó információkat a mobilalkalmazásnak. A mobilalkalmazás megjeleníti ezeket az adatokat a listában.

Más platformok esetén megismételheti a 3–5. lépést.