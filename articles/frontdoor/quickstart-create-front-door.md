---
title: Azure rövid útmutató – Front Door-profil létrehozása az Azure Portal használatával az alkalmazások magas rendelkezésre állásának biztosításához
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
ms.openlocfilehash: 39e7626e6b4c545649e39ff2120d1f1fd105d764
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994666"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Rövid útmutató: Front Door létrehozása magas rendelkezésre állású globális webalkalmazásokhoz

Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Front Door-profilt, amely magas rendelkezésre állást és nagy teljesítményt biztosít globális webalkalmazása számára. 

A rövid útmutatóban ismertetett forgatókönyvben egy adott webalkalmazás különböző Azure-régiókban futó két példányát használjuk. Létrejön egy Front Door-konfiguráció, amely egyenlő arányban tartalmaz [súlyozott és azonos prioritású háttérrendszereket](front-door-routing-methods.md). Ennek segítségével az alkalmazást futtató legközelebbi helyi háttérrendszerekhez irányítható a felhasználói adatforgalom. A Front Door folyamatosan monitorozza a webalkalmazást, és automatikus feladatátvételt biztosít a következő elérhető háttérrendszerre, ha a legközelebbi helyszín elérhetetlenné válna.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 
Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="prerequisites"></a>Előfeltételek
A rövid útmutatóhoz üzembe kell helyeznie egy webalkalmazás két példányát eltérő Azure-régiókban (*USA keleti régiója* és *Nyugat-Európa*). A webalkalmazás mindkét példányának aktív/aktív módban kell lennie, vagyis mindkettőnek készen kell állnia az adatforgalom fogadására, nem úgy, mint az aktív/készenléti konfiguráció esetében, ahol az egyik feladatátvételre szolgál.

1. A képernyő bal felső részén válassza az **Erőforrás létrehozása** > **Web** > **Webalkalmazás** > **Létrehozás** elemet.
2. A **Webalkalmazás** mezőben adja meg vagy válassza ki a következő adatokat, illetve az alapértelmezett beállításokat, ahol nem adtunk meg másik lehetőséget:

     | Beállítás         | Érték     |
     | ---              | ---  |
     | Name (Név)           | Adja meg a webalkalmazás egyedi nevét.  |
     | Erőforráscsoport          | Válassza az **Új** lehetőséget, majd írja be a *myResourceGroupFD1* nevet. |
     | App Service-csomag/hely         | Válassza az **Új** lehetőséget.  Az App Service-csomag mezőben adja meg a *myAppServicePlanEastUS* nevet, majd kattintson az **OK** gombra. 
     |      Hely  |   USA keleti régiója        |
    |||

3. Kattintson a **Létrehozás** gombra.
4. Az alapértelmezett webhely létrejön a Web App-alkalmazás sikeres üzembe helyezésekor.
5. Az 1–3. lépés ismételt végrehajtásával hozzon létre egy második webhelyet egy másik Azure-régióban a következő beállításokkal:

     | Beállítás         | Érték     |
     | ---              | ---  |
     | Name (Név)           | Adja meg a webalkalmazás egyedi nevét.  |
     | Erőforráscsoport          | Válassza az **Új** lehetőséget, majd írja be a *myResourceGroupFD2* nevet. |
     | App Service-csomag/hely         | Válassza az **Új** lehetőséget.  Az App Service-csomag mezőben adja meg a *myAppServicePlanWestEurope* nevet, majd kattintson az **OK** gombra. 
     |      Hely  |   Nyugat-Európa      |
    |||


## <a name="create-a-front-door-for-your-application"></a>Front Door létrehozása az alkalmazáshoz
### <a name="a-add-a-frontend-host-for-front-door"></a>A. Előtérbeli gazdagép hozzáadása a Front Doorhoz
Létrehozunk egy Front Door-konfigurációt, amely a kettő közül a kisebb késésű háttérrendszerre irányítja a felhasználói adatforgalmat.

1. A képernyő bal felső részén válassza az **Erőforrás létrehozása** > **Hálózat** > **Front Door** > **Létrehozás** elemet.
2. A **Front Door létrehozása** ablakban először meg kell adnia az alapinformációkat, majd kiválasztania az előfizetést, amelyen konfigurálni kívánja a Front Doort. Mint minden más Azure-erőforrás esetében, meg kell adnia egy erőforráscsoportot, valamint új erőforráscsoport esetén a régióját is. Végül nevet kell adnia a Front Doornak.
3. Az alapinformációk kitöltése után a konfiguráció **előtérbeli gazdagépének** megadása következik. Az eredménynek érvényes tartománynak kell lennie, például: `myappfrontend.azurefd.net`. A gazdagépnévnek globálisan egyedinek kell lennie, de ezt a Front Door ellenőrzi. 

### <a name="b-add-application-backend-and-backend-pools"></a>B. Az alkalmazás háttérrendszerének és háttérkészletének hozzáadása

Ezután az alkalmazás háttérrendszerét/háttérrendszereit kell konfigurálnia egy háttérkészletben, hogy a Front Door tudja, hol található az alkalmazás. 

1. Háttérkészlet hozzáadásához kattintson a + ikonra, adja meg a háttérkészlet **nevét**, például: `myBackendPool`.
2. Ezután kattintson a Háttérkészletek hozzáadása elemre a korábban létrehozott webhelyek hozzáadásához.
3. A **Cél gazdagép típusa** beállításhoz adja meg az App Service értéket. Válassza ki az előfizetést, amelyben létrehozta a webhelyet, majd a **Cél gazdagép neve** listából válassza ki az első webhelyet, amely ebben az esetben *myAppServicePlanEastUS.azurewebsites.net*.
4. A többi mezőt egyelőre hagyja érintetlenül, és kattintson a **Hozzáadás** gombra.
5. Ismételje meg a 2–4. lépést a másik webhely, vagyis a *myAppServicePlanWestEurope.azurewebsites.net* hozzáadásához.
6. Szükség esetén frissítheti a háttérkészlet Állapotadat-mintavételek és Terheléselosztás beállításait, de az alapértelmezett értékek is megfelelnek. Kattintson a **Hozzáadás** parancsra.


### <a name="c-add-a-routing-rule"></a>C. Útválasztási szabály hozzáadása
Végül kattintson a „+” ikonra az Útválasztási szabályok területen egy útválasztási szabály létrehozásához. Ez az előtérbeli gazdagépnek a háttérkészletre való leképezéséhez szükséges, ami gyakorlatilag azt jelenti, hogy a gazdagéphez (`myappfrontend.azurefd.net`) érkező kéréseket a háttérkészlethez (`myBackendPool`) irányítja át a rendszer. A **Hozzáadás** gombra kattintva adhatja hozzá az útválasztási szabályt a Front Doorhoz. Ezzel készen áll a Front Door létrehozására, úgyhogy kattintson az **Ellenőrzés és létrehozás** elemre.

>[!WARNING]
> **Mindenképpen** gondoskodjon arról, hogy a Front Door minden előtérbeli gazdagépe rendelkezzen egy társított alapértelmezett útvonalat (\*) használó útválasztási szabállyal. Vagyis minden egyes előtérbeli gazdagépnek rendelkeznie kell legalább egy olyan útválasztási szabállyal, amely az alapértelmezett útvonalat (\*) használja. Ennek hiányában előfordulhat, hogy a végfelhasználói adatforgalom nem lesz megfelelően átirányítva.

## <a name="view-front-door-in-action"></a>A Front Door megtekintése működés közben
A Front Door létrehozása után néhány percet vesz igénybe a konfiguráció globális telepítése. Ha befejeződött a folyamat, indítsa el a létrehozott előtérbeli gazdagépet, vagyis a webböngészőben nyissa meg a `myappfrontend.azurefd.net` URL-címet. A kérés automatikusan át lesz irányítva a háttérkészlet Önhöz legközelebbi háttérrendszerére. 

### <a name="view-front-door-handle-application-failover"></a>A Front Door alkalmazás-feladatátvételének megtekintése
Ha a gyakorlatban is látni szeretné a Front Door azonnali globális feladatátvételi funkcióját, keresse fel és állítsa le az egyik korábban létrehozott webhelyet. A háttérkészletben megadott Állapotadat-mintavétel beállítás alapján azonnal megtörténik az adatforgalom átirányítása a másik üzembe helyezett webhelyre. Ezt úgy is tesztelheti, hogy letiltja a háttérrendszert a Front Door háttérkészlet-konfigurációjában. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rájuk szüksége, törölje az erőforráscsoportokat, a webalkalmazásokat és az összes kapcsolódó erőforrást.

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban egy Front Door-profilt hoztunk létre, amellyel átirányíthattuk egy magas rendelkezésre állást és maximális teljesítményt igénylő webalkalmazás adatforgalmát. Az adatforgalom átirányításáról a Front Door által használt [Útválasztási módszereket](front-door-routing-methods.md) ismertető dokumentumban olvashat.