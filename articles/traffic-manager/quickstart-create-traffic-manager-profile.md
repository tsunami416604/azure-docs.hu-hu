---
title: Rövid útmutató – Traffic Manager-profil létrehozása az Azure Portal használatával az alkalmazások magas rendelkezésre állásának biztosításához
description: A rövid útmutató bemutatja, hogyan hozhat létre Traffic Manager-profilokat magas rendelkezésre állású webalkalmazások készítéséhez.
services: traffic-manager
author: asudbring
manager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: allensu
ms.openlocfilehash: 1f7fd3398c24eb82b1a2308f3b52df382c0aab7e
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68224671"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>Gyors útmutató: Traffic Manager-profil létrehozása a Azure Portal használatával

Ez a rövid útmutató azt ismerteti, hogyan hozhat létre olyan Traffic Manager-profilt, amely magas rendelkezésre állást biztosít a webalkalmazás számára.

Ebben a rövid útmutatóban egy webalkalmazás két példányát olvashatja. Mindegyik egy másik Azure-régióban fut. A Traffic Manager-profilt a [végpont prioritása](traffic-manager-routing-methods.md#priority)alapján hozza létre. A profil a webes alkalmazást futtató elsődleges helyre irányítja a felhasználói forgalmat. Traffic Manager folyamatosan figyeli a webalkalmazást. Ha az elsődleges hely nem érhető el, automatikus feladatátvételt biztosít a biztonsági mentési helyhez.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="prerequisites"></a>Előfeltételek

Ebben a rövid útmutatóban két különböző Azure-régióban (az*USA keleti* régiójában és *Nyugat-Európában*) üzembe helyezett webalkalmazás két példánya szükséges. Mindegyik a Traffic Manager elsődleges és feladatátvételi végpontjának fogja szolgálni.

1. A képernyő bal felső részén válassza az **erőforrás** > létrehozása**webes** > webes**alkalmazás**lehetőséget.

1. A **Webalkalmazás létrehozása**területen írja be vagy válassza ki a következő értékeket az **alapok** lapon:

   - **Előfizetési** > **erőforráscsoport**: Válassza az **új létrehozása** elemet, majd írja be a **myResourceGroupTM1**.
   - **Példány részleteinek** > **neve**: Írja be a következőt: *myWebAppEastUS*.
   - **Példány részleteinek** > **közzététele**: Válassza a **kód**lehetőséget.
   - **Példány részleteit** > **futtató verem**: **ASP.net-v 4.7** kiválasztása
   - **Példány részletei** > **operációs rendszer**: Válassza a **Windows**lehetőséget.
   - **Példány részleteit tartalmazó** > **régió**:  Válassza az **USA keleti régiója** lehetőséget.
   - **App Service**Windows **-csomag (az USA keleti**régiója): >  Válassza az **új létrehozása** elemet, majd írja be a **myAppServicePlanEastUS**
   - **App Service terv** > **SKU és size**: Válassza a **standard S1**lehetőséget.
   
3. Válassza a **figyelés** fület, vagy válassza a **Tovább: figyelés**lehetőséget.  A **figyelés**területen állítsa be **Application Insights** > az**Application Insights engedélyezése** a **nem**értékre.

4. **Áttekintés és létrehozás** kiválasztása

5. Tekintse át a beállításokat, majd kattintson a **Létrehozás**gombra.  A webalkalmazás sikeres üzembe helyezése után létrejön egy alapértelmezett webhely.

6. Kövesse a következő lépéseket egy *myWebAppWestEurope*nevű második webalkalmazás létrehozásához a *myResourceGroupTM2*nevű **erőforráscsoport** -névvel, amely egy nyugat- *Európai* **régió** , a **app Service terv** neve  **myAppServicePlanWestEurope**, és az összes többi beállítás ugyanaz, mint a *myWebAppEastUS*.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Hozzon létre egy Traffic Manager profilt, amely a végponti prioritás alapján irányítja a felhasználói forgalmat.

1. A képernyő bal felső részén válassza az **erőforrás** > létrehozása**hálózatkezelés** > **Traffic Manager profil**lehetőséget.
2. A **Traffic Manager profil létrehozása**lapon adja meg a következő beállításokat, vagy válassza ki ezeket:

    | Beállítás | Érték |
    | --------| ----- |
    | Name (Név) | Adjon meg egy egyedi nevet a Traffic Manager profilhoz.|
    | Útválasztási metódus | Válassza a **Priority (prioritás**) lehetőséget.|
    | Subscription | Válassza ki azt az előfizetést, amelyre a Traffic Manager-profilt alkalmazni kívánja. |
    | Resource group | Select *myResourceGroupTM1*.|
    | Location |Ez a beállítás az erőforráscsoport helyét jelöli. Nincs hatása a globálisan telepítendő Traffic Manager-profilra.|

3. Kattintson a **Létrehozás** gombra.

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása

Adja hozzá az *USA keleti régiójában* lévő webhelyt elsődleges végpontként, amelyre az összes felhasználói forgalom át lesz irányítva. Adja hozzá a webhelyet a *Nyugat-Európában* feladatátvételi végpontként. Ha az elsődleges végpont nem érhető el, a forgalom automatikusan átirányítja a feladatátvételi végpontot.

1. A portál keresősáv mezőjébe írja be az előző szakaszban létrehozott Traffic Manager profil nevét.
2. Válassza ki a profilt a keresési eredmények közül.
3. **Traffic Manager profilban**a **Beállítások** szakaszban válassza a végpontoklehetőséget, majd válassza a **Hozzáadás**lehetőséget.
4. Adja meg vagy válassza ki a következő beállításokat:

    | Beállítás | Érték |
    | ------- | ------|
    | Type | Válassza ki az **Azure**-végpontot. |
    | Name (Név) | Adja meg a *myPrimaryEndpoint*. |
    | Célerőforrás típusa | Válassza a **app Service**lehetőséget. |
    | Célerőforrás | Válassza **az App Service** > az**USA keleti**régiója lehetőséget. |
    | Priority | Válassza az **1** lehetőséget. Az összes forgalom állapota erre a végpontra kerül, amikor kifogástalan állapotú. |

    ![Képernyőkép arról, hogy hol adhat hozzá végpontot a Traffic Manager profiljához.](./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png)

5. Kattintson az **OK** gombra.
6. Ha feladatátvételi végpontot szeretne létrehozni a második Azure-régióhoz, ismételje meg a 3. és a 4. lépést a következő beállításokkal:

    | Beállítás | Value |
    | ------- | ------|
    | Type | Válassza ki az **Azure**-végpontot. |
    | Name (Név) | Adja meg a *myFailoverEndpoint*. |
    | Célerőforrás típusa | Válassza a **app Service**lehetőséget. |
    | Célerőforrás | Válassza **az App Service** > **Nyugat-Európa**kiválasztása lehetőséget. |
    | Priority | Válassza a **2**lehetőséget. Az összes forgalom erre a feladatátvételi végpontra kerül, ha az elsődleges végpont állapota nem megfelelő. |

7. Kattintson az **OK** gombra.

Ha elkészült a két végpont hozzáadásával, azok **Traffic Manager profilban**jelennek meg. Figyelje meg, hogy a figyelési állapota most **online** állapotú.

## <a name="test-traffic-manager-profile"></a>Traffic Manager-profil tesztelése

Ebben a szakaszban a Traffic Manager profil tartománynevét fogja megtekinteni. Azt is beállíthatja, hogy az elsődleges végpont ne legyen elérhető. Végezetül láthatja, hogy a webalkalmazás továbbra is elérhető. Ennek oka, hogy Traffic Manager továbbítja a forgalmat a feladatátvételi végpontnak.

### <a name="check-the-dns-name"></a>DNS-név keresése

1. A portál keresősávjában keressen rá az előző szakaszban létrehozott **Traffic Manager-profil** nevére.
2. Válassza ki a Traffic Manager-profilt. Megjelenik az **Áttekintés** .
3. A **Traffic Manager-profil** mezőben megjelenik az újonnan létrehozott Traffic Manager-profil DNS-neve.
  
   ![Képernyőkép a Traffic Manager DNS-név helyéről](./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>A Traffic Manager megtekintése működés közben

1. A webböngészőben adja meg Traffic Manager profiljának DNS-nevét a webalkalmazás alapértelmezett webhelyének megtekintéséhez.

    > [!NOTE]
    > Ebben a rövid útmutatóban az összes kérelem az elsődleges végpontra irányítja át. Az **1-es prioritásra**van beállítva.

    ![Képernyőfelvétel a weboldalról Traffic Manager profil rendelkezésre állásának megerősítéséhez](./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png)

2. Ha Traffic Manager feladatátvételt szeretné megtekinteni a működés közben, tiltsa le az elsődleges helyet:
    1. A Traffic Manager profil lapon az **Áttekintés** szakaszban válassza a **myPrimaryEndpoint**lehetőséget.
    2. A *myPrimaryEndpoint*területen válassza > a letiltott**Mentés**lehetőséget.
    3. **MyPrimaryEndpoint**lezárása. Figyelje meg, hogy az állapot jelenleg **le van tiltva** .
3. Másolja a Traffic Manager profiljának DNS-nevét az előző lépésből, hogy megtekintse a webhelyet egy új böngésző-munkamenetben.
4. Ellenőrizze, hogy a webalkalmazás továbbra is elérhető-e.

Az elsődleges végpont nem érhető el, ezért a rendszer átirányítja a feladatátvételi végpontra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült, törölje az erőforráscsoportot, a webalkalmazásokat és az összes kapcsolódó erőforrást. Ehhez jelölje ki az egyes elemeket az irányítópulton, és válassza a **Törlés** lehetőséget az egyes oldalak tetején.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Traffic Manager profilt. Lehetővé teszi a felhasználói forgalom közvetlen használatát a magas rendelkezésre állású webalkalmazásokhoz. Ha többet szeretne megtudni az útválasztási forgalomról, folytassa a Traffic Manager oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Traffic Manager-oktatóanyagok](tutorial-traffic-manager-improve-website-response.md)
