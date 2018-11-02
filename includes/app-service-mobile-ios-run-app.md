---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 1d1d593b7305e0cd9899f4ec388cb441ced90b10
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133620"
---
1. Látogasson el az [Azure Portalra] Mac gépéről. Kattintson az **Összes szolgáltatás** > **Alkalmazásszolgáltatások** lehetőségre, majd az imént létrehozott háttérrendszerre. A mobilalkalmazás beállításaiban válassza ki a kívánt nyelvet:

    - Objective-C&ndash; **Gyorsindítás** > **iOS (Objective-C)**
    - Swift&ndash; **Gyorsindítás** > **iOS (Swift)**

    A **3. Ügyfélalkalmazás konfigurálása** menüben kattintson a **Letöltés** elemre. Ez letölt egy teljes Xcode-projektet, amely előre konfigurálva van a háttérrendszerhez való csatlakozáshoz. Nyissa meg a projektet az Xcode használatával.

1. Nyomja le a **Run** (Futtatás) gombot a projekt felépítéséhez és az alkalmazás iOS-szimulátorban történő elindításához.

1. Az alkalmazásban írjon be egy jelentéssel bíró szöveget, például *Az oktatóanyag befejezése*, majd kattintson a plusz (**+**) ikonra. Ez egy POST kérést küld a korábban telepített Azure-háttérrendszernek. A háttérrendszer beilleszti az adatokat a kérésből a TodoItem SQL táblázatba, majd visszaküldi az újonnan eltárolt adatokra vonatkozó információkat a mobilalkalmazásnak. A mobilalkalmazás megjeleníti ezeket az adatokat a listában.

   ![iOS rendszeren futó gyorsindítási alkalmazás](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure Portalra]: https://portal.azure.com/
