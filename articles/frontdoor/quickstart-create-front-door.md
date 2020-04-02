---
title: 'Rövid útmutató: Bejárati ajtó profil létrehozása az alkalmazások magas rendelkezésre állásához'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Front Doort a magas rendelkezésre állású, nagy teljesítményű globális webalkalmazása számára.
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
ms.date: 08/31/2018
ms.author: sharadag
ms.openlocfilehash: 2b44c0cdbe2d955efe20a5f9473a29bc9f500a07
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521455"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Rövid útmutató: Front Door létrehozása magas rendelkezésre állású globális webalkalmazásokhoz

Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Front Door-profilt, amely magas rendelkezésre állást és nagy teljesítményt biztosít globális webalkalmazása számára. 

A rövid útmutatóban ismertetett forgatókönyvben egy adott webalkalmazás különböző Azure-régiókban futó két példányát használjuk. Létrejön egy Front Door-konfiguráció, amely egyenlő arányban tartalmaz [súlyozott és azonos prioritású háttérrendszereket](front-door-routing-methods.md). Ennek segítségével az alkalmazást futtató legközelebbi helyi háttérrendszerekhez irányítható a felhasználói adatforgalom. A Front Door folyamatosan monitorozza a webalkalmazást, és automatikus feladatátvételt biztosít a következő elérhető háttérrendszerre, ha a legközelebbi helyszín elérhetetlenné válna.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 
Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="prerequisites"></a>Előfeltételek
A rövid útmutatóhoz üzembe kell helyeznie egy webalkalmazás két példányát eltérő Azure-régiókban (*USA keleti régiója* és *Nyugat-Európa*). A webalkalmazás mindkét példányának aktív/aktív módban kell lennie, vagyis mindkettőnek készen kell állnia az adatforgalom fogadására, nem úgy, mint az aktív/készenléti konfiguráció esetében, ahol az egyik feladatátvételre szolgál.

1. A képernyő bal felső részén válassza az > Erőforrás létrehozása**Web** > **Web App** **lehetőséget.**
2. A **Webalkalmazás** mezőben adja meg vagy válassza ki a következő adatokat, illetve az alapértelmezett beállításokat, ahol nem adtunk meg másik lehetőséget:

     | Beállítás         | Érték     |
     | ---              | ---  |
     | Erőforráscsoport          | Válassza az **Új** lehetőséget, majd írja be a *myResourceGroupFD1* nevet. |
     | Név           | Adja meg a webalkalmazás egyedi nevét.  |
     | Futtatókörnyezet verme          | Futásidejű halmok kiválasztása az alkalmazáshoz |
     |      Régió  |   USA nyugati régiója        |
     | App Service-csomag/hely         | Válassza az **Új**lehetőséget.  Az App Service-csomag mezőben adja meg a *myAppServicePlanEastUS* nevet, majd kattintson az **OK** gombra.| 
     |Sku és méret  | Válassza **a Méretváltás** kiválasztása **Standard S1 100 teljes ACU, 1,75 GB memória** |
     
3. Válassza **a Véleményezés + Létrehozás lehetőséget.**
4. Tekintse át a Web App összefoglaló információit. Kattintson a **Létrehozás** gombra.
5. Körülbelül 5 perc elteltével egy alapértelmezett webhely jön létre, amikor a webalkalmazás telepítése sikeresen megtörtént.
6. Az 1–3. lépés ismételt végrehajtásával hozzon létre egy második webhelyet egy másik Azure-régióban a következő beállításokkal:

     | Beállítás         | Érték     |
     | ---              | ---  |
     | Erőforráscsoport          | Válassza az **Új** lehetőséget, majd írja be a *myResourceGroupFD2* nevet. |
     | Név           | Adja meg a webalkalmazás egyedi nevét.  |
     | Futtatókörnyezet verme          | Futásidejű halmok kiválasztása az alkalmazáshoz |
     |      Régió  |   Nyugat-Európa      |
     | App Service-csomag/hely         | Válassza az **Új**lehetőséget.  Az App Service-csomag mezőben adja meg a *myAppServicePlanWestEurope* nevet, majd kattintson az **OK** gombra.|   
     |Sku és méret  | Válassza **a Méretváltás** kiválasztása **Standard S1 100 teljes ACU, 1,75 GB memória** |
    
## <a name="create-a-front-door-for-your-application"></a>Front Door létrehozása az alkalmazáshoz
### <a name="a-add-a-frontend-host-for-front-door"></a>A. Előtérbeli gazdagép hozzáadása a Front Doorhoz
Létrehozunk egy Front Door-konfigurációt, amely a kettő közül a kisebb késésű háttérrendszerre irányítja a felhasználói adatforgalmat.

1. A képernyő bal felső részén válassza az **Erőforrás** > létrehozása**hálózati** > **bejárati ajtó**lehetőséget.
2. A **Bejárati ajtó létrehozása mezőbe**írja be vagy jelölje ki a következő adatokat, és adja meg az alapértelmezett beállításokat, ha nincs megadva:

     | Beállítás         | Érték     |
     | ---              | ---  |
     |Előfizetés  | Válassza ki azt az előfizetést, amelyben létre szeretné hozni a Bejárati ajtót.|
     | Erőforráscsoport          | Válassza az **Új**lehetőséget, majd írja be a *myResourceGroupFD0 parancsot.* |
     | Erőforráscsoport helye  |   USA középső régiója        |
     
     > [!NOTE]
     > Nem kell létrehozni egy új erőforráscsoport üzembe helyezése bejárati ajtaját.  Ha meglévő erőforráscsoportot is ki választhat.
     
3. Kattintson a **Tovább gombra: Konfiguráció**.
4. Kattintson a '+' ikonra az előtér-/tartományok kartonon.  Az Állomásnév `<Your Initials>frontend`mezőbe írja be a **mezőbe.** Ennek a állomásnévnek globálisan egyedinek kell lennie, a Bejárati ajtó gondoskodik az érvényesítésről.
5. Kattintson a **Hozzáadás** gombra.

### <a name="b-add-application-backend-and-backend-pools"></a>B. Az alkalmazás háttérrendszerének és háttérkészletének hozzáadása

Ezután konfigurálnia kell az előtér-rendszerek/tartományok és az alkalmazás háttérrendszer(ek) egy háttérkészlet a Bejárati ajtajához, hogy tudja, hol található az alkalmazás. 

1. Kattintson a háttérmedencék karton "+" ikonjára, ha háttérkészletet szeretne `myBackendPool`hozzáadni a **háttérkészlet nevéhez,** írja be a be című részt.
2. Ezután kattintson a **Háttérüzenet hozzáadása** elemre a korábban létrehozott webhelyek hozzáadásához.
3. Válassza **a Háttér-állomás típusát** "App Service" néven, válassza ki azt az előfizetést, amelyben létrehozta a webhelyet, majd válassza ki az első webhelyet a **háttérbeli gazdagép név** legördülő legördülő menüből.
4. A többi mezőt egyelőre hagyja érintetlenül, és kattintson a **Hozzáadás** gombra.
5. Válassza **a Háttér-állomás típusát** "App Service" néven, válassza ki azt az előfizetést, amelyben létrehozta a webhelyet, majd válassza ki a **második** webhelyet a **háttérbeli gazdagép név** legördülő legördülő menüből.
6. A többi mezőt egyelőre hagyja érintetlenül, és kattintson a **Hozzáadás** gombra.
7. Szükség esetén módosíthatja az állapotminta és a terheléselosztás beállításait a háttérkészlethez, de az alapértelmezett értékeknek is működniük kell. Mindkét esetben kattintson a **Hozzáadás**gombra.


### <a name="c-add-a-routing-rule"></a>C. Útválasztási szabály hozzáadása
1. Végül az Útválasztási szabályok kartonon kattintson a "+" ikonra az útválasztási szabály konfigurálásához. Ez az előtérbeli gazdagépnek a háttérkészletre való leképezéséhez szükséges, ami gyakorlatilag azt jelenti, hogy a gazdagéphez (`myappfrontend.azurefd.net`) érkező kéréseket a háttérkészlethez (`myBackendPool`) irányítja át a rendszer. 
2. A **Név mezőbe** írja be a "LocationRule" értéket.
3. A **Hozzáadás** gombra kattintva adhatja hozzá az útválasztási szabályt a Front Doorhoz. 
4. Kattintson a **Véleményezés és létrehozás gombra.**
5. Tekintse át a bejárati ajtó létrehozásának beállításait. Kattintson a **Létrehozás gombra.**

>[!WARNING]
> **Mindenképpen** gondoskodjon arról, hogy a Front Door minden előtérbeli gazdagépe rendelkezzen egy társított alapértelmezett útvonalat (\*) használó útválasztási szabállyal. Vagyis minden egyes előtérbeli gazdagépnek rendelkeznie kell legalább egy olyan útválasztási szabállyal, amely az alapértelmezett útvonalat (\*) használja. Ha ez a feltétel nem teljesül, akkor a végfelhasználói forgalom útvonalválasztása nem lesz megfelelő.

## <a name="view-front-door-in-action"></a>A Front Door megtekintése működés közben
A Front Door létrehozása után néhány percet vesz igénybe a konfiguráció globális telepítése. Ha befejeződött a folyamat, indítsa el a létrehozott előtérbeli gazdagépet, vagyis a webböngészőben nyissa meg a `myappfrontend.azurefd.net` URL-címet. A kérés automatikusan át lesz irányítva a háttérkészlet Önhöz legközelebbi háttérrendszerére. 

### <a name="view-front-door-handle-application-failover"></a>A Front Door alkalmazás-feladatátvételének megtekintése
Ha a gyakorlatban is látni szeretné a Front Door azonnali globális feladatátvételi funkcióját, keresse fel és állítsa le az egyik korábban létrehozott webhelyet. A háttérkészletben megadott Állapotadat-mintavétel beállítás alapján azonnal megtörténik az adatforgalom átirányítása a másik üzembe helyezett webhelyre. Ezt úgy is tesztelheti, hogy letiltja a háttérrendszert a Front Door háttérkészlet-konfigurációjában. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje a **myResourceGroupFD1**, **myResourceGroupFD2**és **myResourceGroupFD0** erőforráscsoportokat:

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban egy Front Door-profilt hoztunk létre, amellyel átirányíthattuk egy magas rendelkezésre állást és maximális teljesítményt igénylő webalkalmazás adatforgalmát. Az adatforgalom átirányításáról a Front Door által használt [Útválasztási módszereket](front-door-routing-methods.md) ismertető dokumentumban olvashat.
