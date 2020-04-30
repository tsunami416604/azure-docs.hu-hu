---
title: 'Gyors útmutató: magas rendelkezésre állás beállítása az Azure bejárati ajtó szolgáltatásával'
description: Ez a rövid útmutató azt ismerteti, hogyan használható az Azure bejárati ajtó szolgáltatás a magas rendelkezésre állású és nagy teljesítményű globális webalkalmazásokhoz.
services: front-door
documentationcenter: ''
author: sharad4u
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2020
ms.author: sharadag
ms.openlocfilehash: c1ce34bb7fc851d3f763241c9e92371b43ed1861
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82133447"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Rövid útmutató: Front Door létrehozása magas rendelkezésre állású globális webalkalmazásokhoz

A webalkalmazások magas rendelkezésre állásának beállításához a Azure Portal használatával megismerheti az Azure bejárati ajtót.

Ebben a rövid útmutatóban az Azure bejárati ajtó két különböző Azure-régióban futó webalkalmazást kínál. A bejárati ajtó konfigurációját az azonos súlyozású és azonos prioritású háttérrendszer alapján hozhatja létre. Ez a konfiguráció az alkalmazást futtató legközelebbi helyre irányítja a forgalmat. Az Azure bejárati ajtaja folyamatosan figyeli a webalkalmazást. A szolgáltatás automatikus feladatátvételt biztosít a következő elérhető helyre, ha a legközelebbi hely nem érhető el.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-two-instances-of-a-web-app"></a>Webalkalmazás két példányának létrehozása

Ez a rövid útmutató egy olyan webalkalmazás két példányát igényli, amely különböző Azure-régiókban fut. Mind a webalkalmazás-példányok *aktív/aktív* módban futnak, így akár egy forgalom is elvégezhető. Ez a konfiguráció különbözik az *aktív/készenléti* konfigurációtól, ahol az egyik feladatátvételként működik.

Ha még nem rendelkezik webalkalmazással, a következő lépésekkel állíthatja be például a webes alkalmazásokat.

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

1. A Kezdőlap vagy az Azure menüben válassza az **erőforrás létrehozása**lehetőséget.

1.  > **Web App**Válassza **a webalkalmazás lehetőséget**.

   ![Webalkalmazás létrehozása az Azure Portal-on](media/quickstart-create-front-door/create-web-app-azure-front-door.png)

1. A **Web App (webalkalmazás**) területen válassza ki a használni kívánt **előfizetést** .

1. Az **erőforráscsoport**területen válassza az **új létrehozása**lehetőséget. Adja meg *FrontDoorQS_rg1* **nevét** , majd kattintson **az OK gombra**.

1. A **példány részletei**területen adjon meg egy egyedi **nevet** a webalkalmazás számára. Ez a példa a *WebAppContoso-1-* et használja.

1. Válasszon ki egy **futásidejű veremet**, ebben a példában a *.net Core 2,1 (LTS)* elemet.

1. Válasszon ki egy régiót, például az *USA középső*régióját.

1. **Windows-csomag**esetén válassza az **új létrehozása**lehetőséget. Adja meg a *MyAppServicePlanCentralUS* **nevet** , majd kattintson **az OK gombra**.

1. Győződjön meg arról, hogy az **SKU és a méret** **standard S1 100 total ACU, 1,75 GB memória**.

1. Válassza a **felülvizsgálat + létrehozás**lehetőséget, tekintse át az **összegzést**, majd kattintson a **Létrehozás**gombra. A telepítés befejezéséhez több percet is igénybe vehet.

   ![Webalkalmazás összegzésének áttekintése](media/quickstart-create-front-door/web-app-summary-azure-front-door.png)

A telepítés befejezése után hozzon létre egy második webalkalmazást. Ugyanazt az eljárást használja ugyanazokkal az értékekkel, az alábbi értékek kivételével:

| Beállítás          | Érték     |
| ---              | ---  |
| **Erőforráscsoport**   | Válassza az **új** lehetőséget, majd írja be a *FrontDoorQS_rg2* |
| **Név**             | Adjon egyedi nevet a webalkalmazásnak, ebben a példában a *WebAppContoso-2*  |
| **Régió**           | Egy másik régió, ebben a példában az *USA déli középső* régiója |
| **App Service terv** > **Windows-csomag**         | Válassza az **új** lehetőséget, és adja meg a *myAppServicePlanSouthCentralUS*, majd kattintson **az OK gombra** . |

## <a name="create-a-front-door-for-your-application"></a>Front Door létrehozása az alkalmazáshoz

Konfigurálja az Azure bejárati ajtaját a felhasználói forgalom közvetlen elérésére a két webalkalmazás-kiszolgáló közötti legalacsonyabb késés alapján. Első lépésként vegyen fel egy előtér-gazdagépet az Azure-ba.

1. A Kezdőlap vagy az Azure menüben válassza az **erőforrás létrehozása**lehetőséget. Válassza ki a **hálózati** > **bejárati ajtót**.

1. A **bejárati ajtó létrehozása**lapon válasszon ki egy **előfizetést**.

1. Az **erőforráscsoport**területen válassza az **új**lehetőséget, majd írja be *FrontDoorQS_rg0* , majd kattintson **az OK gombra**.  Ehelyett használhat meglévő erőforráscsoportot.

1. Ha létrehozott egy erőforráscsoportot, válassza ki az **erőforráscsoport helyét**, majd válassza a **Tovább: konfigurálás**lehetőséget.

1. A **frontendek/tartományok**területen válassza **+** a előtér- **gazdagép hozzáadása**lehetőséget.

1. A **gazdagép neve**mezőben adjon meg egy globálisan egyedi állomásnevet. Ez a példa a *contoso-frontend felületet*használja. Válassza a **Hozzáadás** lehetőséget.

   ![Előtér-gazdagép hozzáadása az Azure bejárati ajtóhoz](media/quickstart-create-front-door/add-frontend-host-azure-front-door.png)

Ezután hozzon létre egy háttér-készletet, amely tartalmazza a két webalkalmazást.

1. Még mindig **hozzon létre egy bejárati ajtót**, a **+** **háttérbeli készletek**területen válassza a **háttérbeli készlet hozzáadása**lehetőséget.

1. A **név**mezőbe írja be a következőt: *myBackendPool*.

1. Válassza **a háttér hozzáadása**lehetőséget. A **háttérbeli gazdagép típusa**beállításnál válassza a *app Service*lehetőséget.

1. Válassza ki az előfizetését, majd válassza ki a **háttérbeli állomásnév**alapján létrehozott első webalkalmazást. Ebben a példában a webalkalmazás *WebAppContoso-1*volt. Válassza a **Hozzáadás** lehetőséget.

1. Válassza **a háttér hozzáadása** elemet. A **háttérbeli gazdagép típusa**beállításnál válassza a *app Service*lehetőséget.

1. Válassza ki az előfizetést, majd válassza ki a **háttérbeli állomásnév**alapján létrehozott második webalkalmazást. Válassza a **Hozzáadás** lehetőséget.

   ![Háttérbeli gazdagép hozzáadása a bejárati ajtóhoz](media/quickstart-create-front-door/add-backend-host-pool-azure-front-door.png)

Végül adjon hozzá egy útválasztási szabályt. Egy útválasztási szabály leképezi az előtér-gazdagépet a háttér-készletre. A szabály továbbítja a kérést `contoso-frontend.azurefd.net` a **myBackendPool**.

1. Még mindig **hozzon létre egy bejárati ajtót**, az **+** **útválasztási szabályok**területen válassza az útválasztási szabály konfigurálását.

1. A **szabály hozzáadása**területen a **név**mezőbe írja be a következőt: *LocationRule*. Fogadja el az összes alapértelmezett értéket, majd válassza a **Hozzáadás** lehetőséget az útválasztási szabály hozzáadásához.

   >[!WARNING]
   > Győződjön **meg arról, hogy** a bevezető ajtajában lévő összes előtér-gazdagéphez tartozik egy alapértelmezett elérési úttal`\*`() rendelkező útválasztási szabály. Vagyis az összes útválasztási szabályhoz az alapértelmezett elérési úton (`\*`) megadott előtér-gazdagépekhez legalább egy útválasztási szabálynak kell tartoznia. Ha ezt elmulasztja, akkor a végfelhasználói forgalom nem lesz megfelelően átirányítva.

1. Válassza a **felülvizsgálat + létrehozás**, majd a **Létrehozás**lehetőséget.

   ![Azure-beli bejárati ajtó konfigurálva](media/quickstart-create-front-door/configuration-azure-front-door.png)

## <a name="view-azure-front-door-in-action"></a>Azure-beli bejárati ajtó megtekintése működés közben

Miután létrehozta a bejárati ajtót, eltarthat néhány percig, amíg a konfiguráció globálisan üzembe helyezhető. Ha elkészült, nyissa meg a létrehozott előtér-gazdagépet. A böngészőben nyissa meg `contoso-frontend.azurefd.net`a következőt:. A rendszer automatikusan átirányítja a kérést a legközelebbi kiszolgálóra a háttér-készletben lévő megadott kiszolgálókról.

Ha létrehozta ezeket az alkalmazásokat ebben a rövid útmutatóban, megjelenik egy információs oldal.

Az azonnali globális feladatátvétel teszteléséhez hajtsa végre a következő lépéseket:

1. Nyisson meg egy böngészőt a fentiekben leírtak szerint, és nyissa meg a frontend címe: `contoso-frontend.azurefd.net`.

1. A Azure Portal keresse meg és válassza ki az *app Services*elemet. Görgessen le, és keresse meg a webalkalmazások egyikét, a **WebAppContoso-1** ebben a példában.

1. Válassza ki a webalkalmazást, majd kattintson a **Leállítás**, majd az **Igen** gombra az ellenőrzéshez.

1. Frissítse a böngészőjét. Ugyanezt az információs oldalt kell látnia.

   >[!TIP]
   >Ezekhez a műveletekhez kevés a késés. Előfordulhat, hogy újra kell frissítenie.

1. Keresse meg a másik webalkalmazást, és állítsa le is.

1. Frissítse a böngészőjét. Ekkor egy hibaüzenet jelenik meg.

   ![A webalkalmazás mindkét példánya leállt](media/quickstart-create-front-door/web-app-stopped-message.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután elkészült, eltávolíthatja az összes létrehozott elemet. Egy erőforráscsoport törlésekor a tartalma is törlődik. Ha nem kívánja ezt a bejárati ajtót használni, távolítsa el az erőforrásokat a szükségtelen költségek elkerülése érdekében.

1. A Azure Portal keresse meg és válassza ki az **erőforráscsoportot**, vagy válassza az **erőforráscsoportok** lehetőséget a Azure Portal menüből.

1. Szűrje vagy görgessen lefelé, és keresse meg az erőforráscsoportot, például **FrontDoorQS_rg0**.

1. Válassza ki az erőforráscsoportot, majd válassza az **erőforráscsoport törlése**elemet.

   >[!WARNING]
   >Ez a művelet Irreversable.

1. Írja be az erőforráscsoport nevét az ellenőrzéshez, majd válassza a **Törlés**lehetőséget.

Ismételje meg a másik két csoport eljárását.

## <a name="next-steps"></a>További lépések

A következő cikkből megtudhatja, hogyan adhat hozzá egyéni tartományt az előtérben.
> [!div class="nextstepaction"]
> [Egyéni tartomány hozzáadása](front-door-custom-domain.md)

További információ az útválasztási forgalomról: a [bejárati ajtó útválasztási módszerei](front-door-routing-methods.md).
