---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: ddeb46a2c7bc7f24f55c22f446926529cee7b598
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059690"
---
A tartománynév rekordjainak propagálása után a böngészővel ellenőrizheti, hogy az egyéni tartományneve használható-e a webalkalmazás eléréséhez Azure App Service.

> [!NOTE]
> Eltarthat egy ideig, amíg a CNAME-fájl propagálni tudja a DNS-rendszerét. Használhat olyan szolgáltatást <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> , amellyel ellenőrizheti, hogy a CNAME elérhető-e.
> 
> 

Ha még nem adta hozzá a webalkalmazást Traffic Manager-végpontként, ezt a névfeloldás működésének megkezdése előtt kell végrehajtania, mivel az Egyéni tartománynév Traffic Manager. Traffic Manager majd útvonalakat a webalkalmazáshoz. A következő témakörben található információk használatával adhatja hozzá a webalkalmazást végpontként a Traffic Manager profiljában: [Add vagy DELETE végpontok](../articles/traffic-manager/traffic-manager-endpoints.md) .

> [!NOTE]
> Ha a webalkalmazás nem szerepel a végpont hozzáadásakor, ellenőrizze, hogy az a **Standard** app Service csomag üzemmódra van-e konfigurálva. A webalkalmazáshoz **szabványos** módot kell használnia, hogy működjön a Traffic Managerokkal.
> 
> 

1. A böngészőben nyissa meg az [Azure Portalt](https://portal.azure.com).
2. A **Web Apps** lapon kattintson a webalkalmazás nevére, válassza a **Beállítások**, majd az **Egyéni tartományok** elemet.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. Az **Egyéni tartományok** panelen kattintson az **állomásnév hozzáadása**elemre.
4. Az **állomásnév** szövegmezők használatával adja meg a webalkalmazáshoz társítandó egyéni tartománynevet.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)
5. A tartománynév-konfiguráció mentéséhez kattintson az **Érvényesítés** elemre.
6. Az Azure **ellenőrzése** lehetőségre kattintva elindíthatja a tartomány-ellenőrzési munkafolyamatot. Ezzel a művelettel megkeresheti a tartomány tulajdonjogát, valamint az állomásnév rendelkezésre állását, valamint a sikeres vagy részletes hibajelentést a hiba elhárításához szükséges útmutatással.    
7. Sikeres ellenőrzés esetén a **hostname (állomásnév hozzáadása** ) gomb aktívvá válik, és az állomásnév kiosztása is megtörténik. Most nyissa meg az egyéni tartománynevet egy böngészőben. Ekkor az Egyéni tartománynév használatával kell megjelennie az alkalmazásnak. 
   
   A konfiguráció befejezése után az Egyéni tartománynév a webalkalmazás tartománynevek szakaszában jelenik meg.

Ekkor meg kell tudnia adni a Traffic Manager tartománynevet a böngészőben, és láthatja, hogy a webalkalmazás sikeresen elvégezhető.

