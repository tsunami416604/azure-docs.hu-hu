---
title: Konfigurálja az alhálózat forgalom-útválasztási módszer az Azure Traffic Managerrel |} A Microsoft Docs
description: Ez a cikk ismerteti a Traffic Manager konfigurálása a forgalom irányítására az adott alhálózatok.
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: jpconnock
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: kumud
ms.openlocfilehash: 6e5e6008741306d322ebd07bcbf144133ca4e632
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46131279"
---
# <a name="direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Közvetlen forgalom a Traffic Managert használva a felhasználó alhálózat alapján meghatározott végpontokhoz

Ez a cikk ismerteti az alhálózat forgalom-útválasztási módszer konfigurálása. A **alhálózati** forgalom-útválasztási módszer lehetővé teszi az IP-címtartományok készletét leképezése az eszközspecifikus végpontokat, és amikor kérelem érkezik, a Traffic Manager által, a forrás IP-címét a kérelmet megvizsgálja, és adja vissza a hozzá társított végpontot. 

Az alhálózat útválasztási, attól függően, a felhasználó lekérdezése, IP-címének használatával ebben a cikkben ismertetett forgatókönyvben vagy adatforgalmat egy belső webhelyen vagy éles webhely.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek
Annak érdekében, hogy a Traffic Manager működés közben látni, ehhez az oktatóanyaghoz központi telepítését a következő:
- két alapvető webhely különböző Azure-régióban – **USA keleti Régiójában** (mint belső webhelyet szolgálja ki), és **Nyugat-Európa** (éles webhely szolgál).
- a Traffic Manager - egy virtuális gép a tesztelési virtuális gépek két tesztelése **USA keleti Régiójában** és a második virtuális Gépet a **Nyugat-Európa**. 

A teszt virtuális gépek segítségével bemutatják, hogyan Traffic Manager felé irányítja a felhasználói forgalmat a belső vagy az éles webhely, ahonnan a felhasználó lekérdezése származik alhálózat alapján.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

### <a name="create-websites"></a>Webhelyek létrehozása

Ebben a szakaszban két webhelypéldányokat, amelyek a két Szolgáltatásvégpontok két Azure-régióban a Traffic Manager-profil létrehozásához. A két webhelyek létrehozása a következő lépésekből áll:
1. Hozzon létre két virtuális gép futtatásához egy egyszerű webhely - közül az egyik **USA keleti Régiójában**, és a másik **Nyugat-Európa**.
2. Telepítse az IIS-kiszolgálón az egyes virtuális Gépeken, és frissítse az alapértelmezett webhely-oldal leírja, hogy egy felhasználó csatlakozik a webhely meglátogatása során a virtuális gép neve.

#### <a name="create-vms-for-running-websites"></a>A webhelyek futó virtuális gépek létrehozása
Ebben a szakaszban két virtuális gép létrehozása *myEndpointVMEastUS* és *myEndpointVMWEurope* a a **USA keleti Régiójában** és **Nyugat-Európa** Azure régiók.

1. A felső, jelölje be az Azure portál felső részén található left **erőforrás létrehozása** > **számítási** > **Windows Server 2016 virtuális gép**.
2. Adja meg vagy válassza ki az alábbi adatokat az **Alapvető beállítások** területen, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza a **Létrehozás** elemet:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myIISVMEastUS|
    |Felhasználónév| Adjon meg egy tetszőleges felhasználónevet.|
    |Jelszó| Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Erőforráscsoport| Válassza ki **új** és írja be *myResourceGroupTM1*.|
    |Hely| Válassza az **USA keleti régiója** lehetőséget.|
    |||
4. Válasszon egy virtuálisgép-méretet a **Méret kiválasztása** alatt.
5. Válassza ki az alábbi értékeket a **Beállítások** területen, majd kattintson az **OK** gombra:
    
    |Beállítás|Érték|
    |---|---|
    |Virtuális hálózat| Válassza ki **virtuális hálózati**, a **virtuális hálózat létrehozása**, a **neve**, adja meg *myVNet1*, alhálózat, adja meg a  *mySubnet*.|
    |Hálózati biztonsági csoport|Válassza ki **alapszintű**, majd a **nyilvános bejövő portok kiválasztása** legördülő menüben válassza **HTTP** és **RDP** |
    |Rendszerindítási diagnosztika|Válassza ki **letiltott**.|
    |||
6. Az **Összegzés** lap **Létrehozás** területén kattintson a **Létrehozás** elemre a virtuális gép üzembe helyezésének megkezdéséhez.

7. Végezze el ismét az 1–6. lépést az alábbi módosításokkal:

    |Beállítás|Érték|
    |---|---|
    |Erőforráscsoport | Válassza az **Új** lehetőséget, majd írja be a *myResourceGroupTM2* nevet.|
    |Hely|Nyugat-Európa|
    |Virtuális gép neve | myIISVMWEurope|
    |Virtuális hálózat | Válassza ki **virtuális hálózati**, a **virtuális hálózat létrehozása**, a **neve**, adja meg *myVNet2*, alhálózat, adja meg a  *mySubnet*.|
    |||
8. A virtuális gépek létrehozása néhány percet vesz igénybe. Csak akkor folytassa a következő lépésekkel, ha már mindkét virtuális gép létrejött.

   ![Virtuális gép létrehozása](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Az IIS telepítése és az alapértelmezett webhely testreszabása

Ebben a szakaszban az IIS-kiszolgáló telepíthető a két virtuális gép – *myIISVMEastUS*  & *myIISVMWEurope*, majd ezt követően az alapértelmezett webhely oldalt. Az egyéni webhely oldal a virtuális gép, amely csatlakozik egy webböngészőben a webhely megnyitásakor nevét jeleníti meg.

1. Válassza ki **összes erőforrás** a bal oldali menüben, majd az erőforrások listájában kattintson *myIISVMEastUS* található a *myResourceGroupTM1* erőforráscsoportot.
2. Az a **áttekintése** kattintson **Connect**, majd **csatlakozhat a virtuális gép**, jelölje be **töltse le az RDP-fájl**. 
3. Nyissa meg a letöltött rdp-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek**, majd a **Másik fiók használata** lehetőségre kell kattintania. 
4. Kattintson az **OK** gombra.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához.
6. A kiszolgáló asztalán lépjen a **Windows felügyeleti eszközök**>**Kiszolgálókezelő** elemre.
7. Launch-Windows PowerShell a *myIISVMEastUS* és telepítése az IIS-kiszolgálón, és frissíti az alapértelmezett htm-fájl az alábbi parancsok használatával.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my test website server - " + $env:computername)
    ```
8. Zárja be a RDP-kapcsolatot *myIISVMEastUS*.
9. Ismételje meg az 1 – 6 a lépéseket és a virtuális gép RDP-kapcsolatok létrehozásával *myIISVMWEurope* belül a *myResourceGroupTM2* erőforráscsoportot, amelybe az IIS telepítése és testre szabhatja a saját alapértelmezett weblapot.
10. Launch-Windows PowerShell a *myIISVMWEurope* és telepítése az IIS-kiszolgálón, és frissíti az alapértelmezett htm-fájl az alábbi parancsok használatával.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my production website server - " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurálja az IIS-alapú virtuális gépek DNS-nevek

A TRAFFIC Manager átirányítja a felhasználói forgalom a Szolgáltatásvégpontok DNS-neve alapján. Ebben a szakaszban konfigurálni az IIS-kiszolgálók – DNS-neveit *myIISVMEastUS* és *myIISVMWEurope*.

1. Kattintson a **összes erőforrás** válassza a bal oldali menüben, majd az erőforrások listájában, *myIISVMEastUS* található a *myResourceGroupTM1* erőforráscsoportot.
2. Az a **áttekintése** lap **DNS-név**válassza **konfigurálása**.
3. Az a **konfigurációs** oldalon, a DNS-névcímke, adjon hozzá egy egyedi nevet, és válassza ki **mentése**.
4. Ismételje meg az 1-3 lépéseket nevű virtuális gép *myIISVMWEurope* található a *myResourceGroupTM1* erőforráscsoportot.

### <a name="create-test-vms"></a>Hozzon létre virtuális gépek tesztelése

Ebben a szakaszban létrehoz egy virtuális Gépet (*mVMEastUS* és *myVMWestEurope*) minden egyes Azure-régióban (**USA keleti Régiójában** és **Nyugat-Európa**. Ezek a virtuális gépek használatával hogyan Traffic Manager átirányítja a forgalmat a legközelebbi IIS-kiszolgáló a webhely böngészése során tesztelheti.

1. A felső, jelölje be az Azure portál felső részén található left **erőforrás létrehozása** > **számítási** > **Windows Server 2016 virtuális gép**.
2. Adja meg vagy válassza ki az alábbi adatokat az **Alapvető beállítások** területen, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza a **Létrehozás** elemet:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVMEastUS|
    |Felhasználónév| Adjon meg egy tetszőleges felhasználónevet.|
    |Jelszó| Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Erőforráscsoport| Válassza a **Meglévő**, majd a *myResourceGroupTM1* lehetőséget.|
    |||

4. Válasszon egy virtuálisgép-méretet a **Méret kiválasztása** alatt.
5. Válassza ki az alábbi értékeket a **Beállítások** területen, majd kattintson az **OK** gombra:
    |Beállítás|Érték|
    |---|---|
    |Virtuális hálózat| Válassza ki **virtuális hálózati**, a **virtuális hálózat létrehozása**, a **neve**, adja meg *myVNet3*, alhálózat, adja meg a  *mySubnet3*.|
    |Hálózati biztonsági csoport|Válassza ki **alapszintű**, majd a **nyilvános bejövő portok kiválasztása** legördülő menüben válassza **HTTP** és **RDP** |
    |Rendszerindítási diagnosztika|Válassza ki **letiltott**.|
    |||

6. Az **Összegzés** lap **Létrehozás** területén kattintson a **Létrehozás** elemre a virtuális gép üzembe helyezésének megkezdéséhez.

7. Végezze el ismét az 1 – 5. lépéseket a következő módosításokat:

    |Beállítás|Érték|
    |---|---|
    |Virtuális gép neve | *myVMWEurope*|
    |Erőforráscsoport | Válassza ki **meglévő**, majd írja be *myResourceGroupTM2*|
    |Virtuális hálózat | Válassza ki **virtuális hálózati**, a **virtuális hálózat létrehozása**, a **neve**, adja meg *myVNet4*, alhálózat, adja meg a  *mySubnet4*.|
    |||

8. A virtuális gépek létrehozása néhány percet vesz igénybe. Csak akkor folytassa a következő lépésekkel, ha már mindkét virtuális gép létrejött.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása
Hozzon létre egy Traffic Manager-profilt, amely lehetővé teszi, hogy a forrás IP-címét a kérelem alapján meghatározott végpontokhoz adja vissza.

1. A képernyő bal felső részén válassza az **Erőforrás létrehozása** > **Hálózat** > **Traffic Manager-profil** > **Létrehozás** elemet.
2. A **Traffic Manager-profil létrehozása** területen adja meg vagy válassza ki az alábbi adatokat, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza a **Létrehozás** elemet:
    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Name (Név)                   | Ez a név egyedinek kell lennie a trafficmanager.net zónában és a Traffic Manager-profil eléréséhez használt trafficmanager.net DNS-nevet eredményezi.                                   |
    | Útválasztási metódus          | Válassza ki a **alhálózati** esetén használt útválasztási módszer.                                       |
    | Előfizetés            | Válassza ki előfizetését.                          |
    | Erőforráscsoport          | Válassza ki **meglévő** , és adja meg *myResourceGroupTM1*. |
    | |                              |
    |
  
    ![Traffic Manager-profil létrehozása](./media/traffic-manager-subnet-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása

Adja hozzá a két virtuális gép fut az IIS-kiszolgálók – *myIISVMEastUS*  & *myIISVMWEurope* felhasználói forgalom irányítására az alhálózat, a felhasználó lekérdezés alapján.

1. A portál keresősávjában keressen rá az előző szakaszban létrehozott Traffic Manager-profil nevére, majd válassza ki a profilt a megjelenített eredmények között.
2. A **Traffic Manager-profil** panel **Beállítások** szakaszában kattintson a **Végpontok**, majd a **Hozzáadás** elemre.
3. Adja meg vagy válassza ki az alábbi adatokat, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza az **OK** elemet:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Típus                    | Azure-végpont                                   |
    | Name (Név)           | myTestWebSiteEndpoint                                        |
    | Célerőforrás típusa           | Nyilvános IP-cím                          |
    | Célerőforrás          | **Nyilvános IP-cím kiválasztása** azonos előfizetéshez tartozó nyilvános IP-címekkel rendelkező erőforrások listája megjelenítéséhez. A **erőforrás**, válassza ki a nyilvános IP-címet *myIISVMEastUS-ip*. Ez a virtuális gép az USA keleti régiójában IIS-kiszolgáló nyilvános IP-címét.|
    |  Útválasztási alhálózat-beállítások    |   Az IP-cím hozzáadásához *myVMEastUS* tesztelése a virtuális gép. A virtuális gépről származó felhasználói lekérdezés a rendszer átirányítja a *myTestWebSiteEndpoint*.    |

4. Ismételje meg a 2. és 3-nevű másik végpont hozzáadása *myProductionEndpoint* a nyilvános IP-cím *myIISVMWEurope-ip* az IIS-kiszolgálón nevű virtuális Géphez társított *myIISVMWEurope* . A **alhálózat útválasztási beállítások**, a teszt virtuális gép – IP-cím hozzáadásához *myVMWestEurope*. Ez a teszt virtuális Gépet a rendszer átirányítja a végpont - bármely felhasználó lekérdezése *myProductionWebsiteEndpoint*.
5.  Miután mindkét végpontot hozzáadta, azok megjelennek a **Traffic Manager-profil** panelen, **Online** figyelési állapottal.

    ![Traffic Manager-végpont hozzáadása](./media/traffic-manager-subnet-routing-method/customize-endpoint-with-subnet-routing-eastus.png)
  
## <a name="test-traffic-manager-profile"></a>Traffic Manager-profil tesztelése
Ebben a szakaszban tesztelni, hogyan a Traffic Manager átirányítja a felhasználói forgalmat egy adott alhálózatról egy adott végpontnak. A Traffic Manager megtekintése működés közben, kövesse az alábbi lépéseket:
1. A Traffic Manager-profil DNS-nevét határozza meg.
2. A Traffic Manager a következő művelet megjelenítése:
    - A teszt virtuális gép a (*myVMEastUS*) található a **USA keleti Régiójában** régió, a webböngésző, keresse meg a DNS-nevével a Traffic Manager-profil.
    - A teszt virtuális gép a (*myVMEastUS*) található a **Nyugat-Európa** régió, a webböngésző, keresse meg a DNS-nevével a Traffic Manager-profil.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Határozza meg a Traffic Manager-profil DNS-neve
Ebben az oktatóanyagban az egyszerűség kedvéért használja DNS-nevével a Traffic Manager-profil használatával keresse fel a webhelyek. 

Megadhatja, hogy a Traffic Manager-profil DNS-nevét a következő:

1.  A portál keresősávjában keressen rá az előző szakaszban létrehozott **Traffic Manager-profil** nevére. Kattintson a Traffic Manager-profilra a megjelenített eredmények között.
1. Kattintson az **Áttekintés** elemre.
2. A **Traffic Manager-profil** mezőben megjelenik az újonnan létrehozott Traffic Manager-profil DNS-neve. Az éles környezetekben, hogy személyes tartománynév beállítása átirányítása a Traffic Manager szolgáltatásbeli tartománynévre, egy DNS CNAME rekord használatával.

   ![Traffic Manager DNS-neve](./media/traffic-manager-subnet-routing-method/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>A Traffic Manager megtekintése működés közben
Ebben a szakaszban láthatja a Traffic Manager a művelet. 

1. Válassza ki **összes erőforrás** a bal oldali menüben, majd az erőforrások listájában kattintson *myVMEastUS* található a *myResourceGroupTM1* erőforráscsoportot.
2. Az a **áttekintése** kattintson **Connect**, majd **csatlakozhat a virtuális gép**, jelölje be **töltse le az RDP-fájl**. 
3. Nyissa meg a letöltött rdp-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek**, majd a **Másik fiók használata** lehetőségre kell kattintania. 
4. Kattintson az **OK** gombra.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához. 
1. Egy webböngészőben a virtuális gép *myVMEastUS*, írja be a Traffic Manager-profil megtekintéséhez a webhely DNS-nevét. Mivel a virtuális gép *myVMEastUS* IP-cím társítva a végpont *myIISVMEastUS*, a böngésző elindítja a Test-webhely kiszolgálójának - *myIISVMEastUS*.

   ![Traffic Manager-profil tesztelése](./media/traffic-manager-subnet-routing-method/test-traffic-manager.png)

2. Ezután csatlakozhat a virtuális gép *myVMWestEurope* található **Nyugat-Európa** lépések használatával 1 – 5, és keresse meg a Traffic Manager-profil tartománynévvel a virtuális gépről. Mivel a virtuális gép *myVMWestEurope* IP-cím társítva a végpont *myIISVMEastUS*, a böngésző elindítja a Test-webhely kiszolgálójának - *myIISVMWEurope*. 
  
## <a name="delete-the-traffic-manager-profile"></a>A Traffic Manager-profil törlése
Ha már nincs rá szükség, törölje az erőforráscsoportot (**ResourceGroupTM1** és **ResourceGroupTM2**). Ehhez válassza ki az erőforráscsoportot (**ResourceGroupTM1** vagy **ResourceGroupTM2**), majd válassza ki **törlése**.

## <a name="next-steps"></a>További lépések

- Ismerje meg [forgalom-útválasztási módszer súlyozott](traffic-manager-configure-weighted-routing-method.md).
- Ismerje meg [prioritásos útválasztási mód](traffic-manager-configure-priority-routing-method.md).
- Ismerje meg [földrajzi útválasztási mód](traffic-manager-configure-geographic-routing-method.md).


