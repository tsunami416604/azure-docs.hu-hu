---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: 8d7731480b6239c572d39f52b6a0217d2ac48d25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240237"
---
1. Lépjen a megoldásfájlhoz (.sln) ügyfél-projektben, és nyissa meg a Visual Studióval.

2. A Visual Studióban válassza ki a megoldás platformját (Android, iOS vagy Windows) a start nyíl melletti legördülő menüből. Válassza ki a konkrét eszközt vagy emulátort a zöld nyílon található legördülő menüre kattintva. Használhatja az alapértelmezett Android platformot és a Ripple emulátort. Az összetettebb oktatóanyagok (pl. leküldéses értesítések) esetén egy támogatott eszközt és emulátort kell majd választania.

3. Nyissa meg a fájlt `ToDoActivity.java` ebben a mappában - ZUMOAPPNAME/app/src/main/java/com/példa/zumoappname. Az alkalmazás neve `ZUMOAPPNAME`.

4. Nyissa meg a [az Azure portal](https://portal.azure.com/) , és keresse meg a mobilalkalmazás, amelyet Ön hozott létre. Az a `Overview` panelen keresse meg az URL-címet, amely a nyilvános végpont számára. Például: a sitename for my app name "test123" lesz https://test123.azurewebsites.net.

5. Nyissa meg a `index.js` ZUMOAPPNAME/www/js/index.js, majd a fájl `onDeviceReady()` metódus, cserélje le `ZUMOAPPURL` nyilvános végpontot a fenti paraméterrel.

    `client = new WindowsAzure.MobileServiceClient('ZUMOAPPURL');`
    
    válik
    
    `client = new WindowsAzure.MobileServiceClient('https://test123.azurewebsites.net');`
    
6. A Cordova alkalmazás létrehozásához és futtatásához nyomja le az F5 billentyűt, vagy kattintson a zöld nyílra. Ha az emulátor egy biztonsági párbeszédablakot jelenít meg, amelyben hozzáférést kér a hálózathoz, fogadja el.

7. Után az alkalmazás elindult az eszközön vagy az emulátoron, írjon be egy értelmes szöveget az **új szöveg megadása**, például *az oktatóanyag elvégzéséhez* és kattintson a **Hozzáadás** gombra.

A háttérrendszer beszúrja az adatokat a kérésből az SQL Database-ben található TodoItem táblába, majd visszaküldi az újonnan tárolt elemekre vonatkozó információkat a mobilalkalmazásnak. A mobilalkalmazás megjeleníti ezeket az adatokat a listában.

Más platformok esetén megismételheti a 3–5. lépést.