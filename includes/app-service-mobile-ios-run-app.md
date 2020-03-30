---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a5bde1a56bf6a1f5fca4b775c7c8e9bb7477eb6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240279"
---
1. Nyissa meg a letöltött ügyfélprojektet az Xcode használatával.

2. Nyissa meg az [Azure Portalon,](https://portal.azure.com/) és keresse meg a létrehozott mobilalkalmazást. A `Overview` panelen keresse meg a mobilalkalmazás nyilvános végpontját képező URL-címet. Példa - a sitename az én app neve https://test123.azurewebsites.net"test123" lesz .

3. A Swift projekt, `ToDoTableViewController.swift` nyissa meg a fájlt ebben a mappában - ZUMOAPPNAME/ZUMOAPPNAME/ToDoTableViewController.swift. Az alkalmazás `ZUMOAPPNAME`neve .

4. A `viewDidLoad()` metódusban `ZUMOAPPURL` cserélje le a paramétert a fenti nyilvános végpontra.

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    Lesz
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. Az Objective-C projekt esetében `QSTodoService.m` nyissa meg a mappában lévő fájlt - ZUMOAPPNAME/ZUMOAPPNAME. Az alkalmazás `ZUMOAPPNAME`neve .

6. A `init` metódusban `ZUMOAPPURL` cserélje le a paramétert a fenti nyilvános végpontra.

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    Lesz
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. Nyomja le a **Run** (Futtatás) gombot a projekt felépítéséhez és az alkalmazás iOS-szimulátorban történő elindításához.

8. Az alkalmazásban kattintson a**+** plusz ( ) ikonra, írja be az értelmes szöveget, például *fejezze be az oktatóanyagot*, majd kattintson a mentés gombra. Ez egy POST kérést küld a korábban telepített Azure-háttérrendszernek. A háttérrendszer beilleszti az adatokat a kérésből a TodoItem SQL táblázatba, majd visszaküldi az újonnan eltárolt adatokra vonatkozó információkat a mobilalkalmazásnak. A mobilalkalmazás megjeleníti ezeket az adatokat a listában.

   ![iOS rendszeren futó gyorsindítási alkalmazás](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
