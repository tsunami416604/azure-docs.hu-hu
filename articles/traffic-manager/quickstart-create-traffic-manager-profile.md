---
title: Rövid útmutató:Profil létrehozása az alkalmazások HA-jéhez - Azure Portal - Azure Traffic Manager
description: A rövid útmutató bemutatja, hogyan hozhat létre Traffic Manager-profilokat magas rendelkezésre állású webalkalmazások készítéséhez.
services: traffic-manager
author: rohinkoul
manager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: rohink
ms.openlocfilehash: 559ed0a134bb6db78d1e89634138b4025e04152b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76934769"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>Rövid útmutató: Traffic Manager-profil létrehozása az Azure Portalon

Ez a rövid útmutató ismerteti, hogyan hozhat létre egy Traffic Manager-profilt, amely magas rendelkezésre állást biztosít a webalkalmazás számára.

Ebben a rövid útmutatóban egy webalkalmazás két példányáról olvashat. Mindegyik egy másik Azure-régióban fut. A [végpontprioritás](traffic-manager-routing-methods.md#priority-traffic-routing-method)alapján hozzon létre egy Traffic Manager-profilt. A profil a felhasználói forgalmat a webalkalmazást futtató elsődleges helyre irányítja. A Traffic Manager folyamatosan figyeli a webalkalmazást. Ha az elsődleges hely nem érhető el, automatikus feladatátvételt biztosít a biztonsági mentési helynek.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz két példányra van szüksége egy webalkalmazáskét különböző Azure-régióban *(USA keleti régióban* és *Nyugat-Európában).* Mindegyik elsődleges és feladatátvételi végpontként fog szolgálni a Traffic Manager számára.

1. A képernyő bal felső részén válassza az **Erőforrás** > létrehozása**Web** > **Web App**lehetőséget.

1. A **Webalkalmazás létrehozása**csoportban írja be vagy jelölje ki a következő értékeket az **Alapok** lapon:

   - **Előfizetési** > **erőforráscsoport**: Válassza **az Új létrehozása lehetőséget,** majd írja be a **myResourceGroupTM1 parancsot.**
   - **Példány részleteinek** > **neve**: Írja be a *myWebAppEastUS*nevet.
   - **Példány részleteinek** > **közzététele:** Válassza a **Kód**lehetőséget.
   - **Példány részletei** > **futásidejű verem**: Válassza **ASP.NET V4.7**
   - **Példány részletei** > **operációs rendszer**: Válassza a **Windows**lehetőséget.
   - **Példány részleteirégió** > **Region**: Válassza az USA **keleti régiója**lehetőséget.
   - **App Service Plan** > **Windows Plan (USA keleti része)**: Válassza **az Új létrehozása lehetőséget,** majd írja be a **myAppServicePlanEastUS parancsot**
   - **App Service Plan** > **Sku és mérete:** Válassza **a Standard S1**lehetőséget.
   
3. Válassza a **Figyelés** lapot, vagy válassza **a Tovább:Figyelés**lehetőséget.  A **Figyelés**csoportban állítsa az **Application Insights** > **Enable Application Insights** -t **nem**beállításra.

4. Válassza a **Véleményezés és létrehozás lehetőséget**

5. Tekintse át a beállításokat, majd kattintson a **Létrehozás gombra.**  Amikor a webalkalmazás sikeresen telepíti, létrehoz egy alapértelmezett webhelyet.

6. A lépéseket követve hozzon létre egy második *webalkalmazást myWebAppWestEurope*néven, *a myResourceGroupTM2* **erőforráscsoport** nevével , *nyugat-európai* **régióval** , a **myAppServicePlanWestEurope** **App Service Plan** nevével , és az összes többi beállítással, mint a *myWebAppEastUS*.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Hozzon létre egy Traffic Manager-profilt, amely a végpontprioritás alapján irányítja a felhasználói forgalmat.

1. A képernyő bal felső részén válassza az **Erőforrás** > **hálózati** > **forgalomkezelő profil**létrehozása lehetőséget.
2. A **Traffic Manager létrehozása profilban**adja meg vagy adja meg a következő beállításokat:

    | Beállítás | Érték |
    | --------| ----- |
    | Név | Adja meg a Traffic Manager-profil egyedi nevét.|
    | Útválasztási metódus | Válassza a **Prioritás lehetőséget.**|
    | Előfizetés | Válassza ki azt az előfizetést, amelyre a forgalomkezelő-profilt alkalmazni szeretné. |
    | Erőforráscsoport | Válassza a *myResourceGroupTM1 lehetőséget.*|
    | Hely |Ez a beállítás az erőforráscsoport helyére vonatkozik. Nincs hatással a Traffic Manager-profilra, amely globálisan lesz telepítve.|

3. Kattintson a **Létrehozás** gombra.

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása

Adja hozzá az *USA keleti régiójában* lévő webhelyt elsődleges végpontként, amelyre az összes felhasználói forgalom át lesz irányítva. Adja hozzá a *nyugat-európai* webhelyet feladatátvételi végpontként. Ha az elsődleges végpont nem érhető el, a forgalom automatikusan a feladatátvételi végpontra vezet.

1. A portál keresősávján adja meg az előző szakaszban létrehozott Traffic Manager-profil nevét.
2. Válassza ki a profilt a keresési eredmények közül.
3. A **Traffic Manager-profilban**a **Beállítások** csoportban válassza a **Végpontok**lehetőséget, majd a **Hozzáadás**lehetőséget.
4. Adja meg vagy válassza ki az alábbi beállításokat:

    | Beállítás | Érték |
    | ------- | ------|
    | Típus | Válassza az **Azure-végpont lehetőséget.** |
    | Név | Adja meg *a myPrimaryEndpoint billentyűt*. |
    | Célerőforrás típusa | Válassza az **App Service**lehetőséget. |
    | Célerőforrás | Válassza **az Alkalmazásszolgáltatás** > kiválasztása**USA keleti része**lehetőséget. |
    | Prioritás | Válassza az **1**lehetőséget. Minden forgalom megy erre a végpontra, ha az egészséges. |

    ![Képernyőkép abból, hogy hol ad hozzá végpontot a Traffic Manager-profilhoz.](./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png)

5. Válassza **az OK gombot.**
6. Feladatátvételi végpont létrehozásához a második Azure-régióhoz ismételje meg a 3.

    | Beállítás | Érték |
    | ------- | ------|
    | Típus | Válassza az **Azure-végpont lehetőséget.** |
    | Név | Adja meg *a myFailoverEndpoint .* |
    | Célerőforrás típusa | Válassza az **App Service**lehetőséget. |
    | Célerőforrás | Válassza a**Nyugat-Európa** **alkalmazásszolgáltatás** > kiválasztása lehetőséget. |
    | Prioritás | Válassza a **2**lehetőséget. Minden forgalom megy ez a feladatátvételi végpont, ha az elsődleges végpont nem kifogástalan. |

7. Válassza **az OK gombot.**

Ha befejezte a két végpont hozzáadását, azok megjelennek a **Traffic Manager-profilban.** Figyelje meg, hogy a figyelési **állapotuk** online állapot.

## <a name="test-traffic-manager-profile"></a>Traffic Manager-profil tesztelése

Ebben a szakaszban ellenőrizni fogja a Traffic Manager-profil tartománynevét. Azt is konfigurálja, hogy az elsődleges végpont nem érhető el. Végül láthatja, hogy a webalkalmazás továbbra is elérhető. Ennek oka, hogy a Traffic Manager elküldi a forgalmat a feladatátvételi végpontra.

### <a name="check-the-dns-name"></a>A DNS-név ellenőrzése

1. A portál keresősávjában keressen rá az előző szakaszban létrehozott **Traffic Manager-profil** nevére.
2. Válassza ki a forgalomkezelő profilját. Megjelenik **az áttekintés.**
3. A **Traffic Manager-profil** mezőben megjelenik az újonnan létrehozott Traffic Manager-profil DNS-neve.
  
   ![Képernyőkép a Traffic Manager DNS-nevének helyéről](./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>A Traffic Manager megtekintése működés közben

1. A webböngészőben adja meg a Traffic Manager-profil DNS-nevét a WebApp alapértelmezett webhelyének megtekintéséhez.

    > [!NOTE]
    > Ebben a rövid útmutatóban az összes kérelem az elsődleges végpontra irányít. **1-es prioritásra**van állítva.

    ![Képernyőkép a weblapról a Traffic Manager-profil elérhetőségének megerősítésére](./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png)

2. A Traffic Manager feladatátvételének működés közbeni megtekintéséhez tiltsa le az elsődleges helyet:
    1. A Traffic Manager-profil lap **Áttekintés szakaszában** válassza a **myPrimaryEndpoint lehetőséget.**
    2. A *myPrimaryEndpoint alkalmazásban*válassza **a Letiltott** > **mentés lehetőséget.**
    3. Zárja be **a myPrimaryEndpoint ot**. Figyelje meg, hogy az állapot le van **tiltva.**
3. Másolja a Traffic Manager-profil DNS-nevét az előző lépésből, és tekintse meg a webhelyet egy új webböngésző-munkamenetben.
4. Ellenőrizze, hogy a webalkalmazás továbbra is elérhető-e.

Az elsődleges végpont nem érhető el, így a feladatátvételi végpontra irányítva.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült, törölje az erőforráscsoportokat, a webalkalmazásokat és az összes kapcsolódó erőforrást. Ehhez jelölje ki az egyes elemeket az irányítópulton, és válassza a **Törlés** lehetőséget az egyes lapok tetején.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Traffic Manager-profilt. Ez lehetővé teszi, hogy közvetlen felhasználói forgalmat a magas rendelkezésre állású webes alkalmazások. Ha többet szeretne megtudni a forgalom útválasztásáról, folytassa a Traffic Manager oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Traffic Manager-oktatóanyagok](tutorial-traffic-manager-improve-website-response.md)
