---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a5bde1a56bf6a1f5fca4b775c7c8e9bb7477eb6b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240279"
---
1. Nyissa meg a letöltött ügyfél projektet az Xcode használatával.

2. Nyissa meg a [az Azure portal](https://portal.azure.com/) , és keresse meg a mobilalkalmazás, amelyet Ön hozott létre. Az a `Overview` panelen keresse meg az URL-címet, amely a nyilvános végpont számára. Például: a sitename for my app name "test123" lesz https://test123.azurewebsites.net.

3. A Swift-projektben nyissa meg a fájlt `ToDoTableViewController.swift` ebben a mappában - ZUMOAPPNAME/ZUMOAPPNAME/ToDoTableViewController.swift. Az alkalmazás neve `ZUMOAPPNAME`.

4. A `viewDidLoad()` metódus, cserélje le `ZUMOAPPURL` nyilvános végpontot a fenti paraméterrel.

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    válik
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. Az Objective-C-projektben nyissa meg a fájlt `QSTodoService.m` ebben a mappában - ZUMOAPPNAME/ZUMOAPPNAME. Az alkalmazás neve `ZUMOAPPNAME`.

6. A `init` metódus, cserélje le `ZUMOAPPURL` nyilvános végpontot a fenti paraméterrel.

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    válik
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. Nyomja le a **Run** (Futtatás) gombot a projekt felépítéséhez és az alkalmazás iOS-szimulátorban történő elindításához.

8. Az alkalmazásban kattintson a plusz ( **+** ) ikonra, írjon be egy értelmes szöveget, például *az oktatóanyag elvégzéséhez*, majd kattintson a Mentés gombra. Ez egy POST kérést küld a korábban telepített Azure-háttérrendszernek. A háttérrendszer beilleszti az adatokat a kérésből a TodoItem SQL táblázatba, majd visszaküldi az újonnan eltárolt adatokra vonatkozó információkat a mobilalkalmazásnak. A mobilalkalmazás megjeleníti ezeket az adatokat a listában.

   ![iOS rendszeren futó gyorsindítási alkalmazás](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
