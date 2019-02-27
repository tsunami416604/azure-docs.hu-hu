---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 8146489a913ce863cee7534331231a248a3ea7ac
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890987"
---
1. Látogasson el az [Azure Portalra] Mac gépéről. Kattintson az **Összes szolgáltatás** > **Alkalmazásszolgáltatások** lehetőségre, majd az imént létrehozott háttérrendszerre. A mobilalkalmazás beállításaiban válassza ki a kívánt nyelvet:

    - Objective-C&ndash; **Gyorsindítás** > **iOS (Objective-C)**
    - Swift&ndash; **Gyorsindítás** > **iOS (Swift)**

    A **3. Ügyfélalkalmazás konfigurálása** menüben kattintson a **Letöltés** elemre. Ez letölt egy teljes Xcode-projektet, amely előre konfigurálva van a háttérrendszerhez való csatlakozáshoz. Nyissa meg a projektet az Xcode használatával.

1. Nyomja le a **Run** (Futtatás) gombot a projekt felépítéséhez és az alkalmazás iOS-szimulátorban történő elindításához.

1. Az alkalmazásban kattintson a plusz (**+**) ikonra, írjon be egy értelmes szöveget, például *az oktatóanyag elvégzéséhez*, majd kattintson a Mentés gombra. Ez egy POST kérést küld a korábban telepített Azure-háttérrendszernek. A háttérrendszer beilleszti az adatokat a kérésből a TodoItem SQL táblázatba, majd visszaküldi az újonnan eltárolt adatokra vonatkozó információkat a mobilalkalmazásnak. A mobilalkalmazás megjeleníti ezeket az adatokat a listában.

   ![iOS rendszeren futó gyorsindítási alkalmazás](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure Portalra]: https://portal.azure.com/
