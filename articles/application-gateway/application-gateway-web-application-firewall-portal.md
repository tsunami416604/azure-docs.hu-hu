---
title: "Hozzon létre egy alkalmazás webalkalmazási tűzfal - Azure-portál |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy alkalmazás webalkalmazási tűzfal az Azure portál használatával."
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: davidmu
ms.openlocfilehash: d2b8fc65e6cd03f61151dbae66bb89821cdab13b
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Hozzon létre egy alkalmazást az Azure portál használatával webalkalmazási tűzfal

Az Azure-portálon hozhat létre egy [Alkalmazásátjáró](application-gateway-introduction.md) rendelkező egy [webalkalmazási tűzfal](application-gateway-web-application-firewall-overview.md) (waf-ot). A WAF használatát [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) szabályokat, hogy az alkalmazás védelme. Ezek a szabályok közé tartoznak például az SQL-injektálás támadások, a többhelyes parancsfájlok futtatására és a munkamenet kihasználásának elleni védelem.

Ebből a cikkből megismerheti, hogyan:

> [!div class="checklist"]
> * Hozzon létre egy alkalmazás WAF engedélyezve
> * A háttérkiszolgálók használt virtuális gépek létrehozása
> * Hozzon létre egy tárfiókot, és diagnosztika konfigurálása

![Webes alkalmazás tűzfal – példa](./media/application-gateway-web-application-firewall-portal/scenario-waf.png)

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure portálon, a [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Egy virtuális hálózatot az Ön által létrehozott erőforrások közötti kommunikációra van szükség. Két alhálózat ebben a példában jönnek létre: egyet az Alkalmazásátjáró, míg a másik a háttérkiszolgálókhoz. Az Alkalmazásátjáró létrehozott egyszerre egy virtuális hálózatot is létrehozhat.

1. Kattintson a **új** az Azure portál bal felső sarkában található.
2. Válassza ki **hálózati** majd **Application Gateway** kiemelt listájában.
3. Adja meg ezeket az értékeket az Alkalmazásátjáró:

    - *myAppGateway* – az Alkalmazásátjáró nevét.
    - *myResourceGroupAG* – az új erőforráscsoport.
    - Válassza ki *WAF* tartozó a szint az Alkalmazásátjáró.

    ![Új Alkalmazásátjáró létrehozása](./media/application-gateway-web-application-firewall-portal/application-gateway-create.png)

4. Fogadja el a további beállításoknál az alapértelmezett értékeket, és kattintson a **OK**.
5. Kattintson a **virtuális hálózatot választ**, kattintson a **hozzon létre új**, és ezekkel az értékekkel adja meg a virtuális hálózat:

    - *myVNet* – a virtuális hálózat nevét.
    - *10.0.0.0/16* – a virtuális hálózat címtere.
    - *myAGSubnet* – az alhálózati név.
    - *10.0.0.0/24* – az alhálózati címtartományt.

    ![Virtuális hálózat létrehozása](./media/application-gateway-web-application-firewall-portal/application-gateway-vnet.png)

6. Kattintson a **OK** a virtuális hálózati és alhálózati létrehozásához.
7. Kattintson a **egy nyilvános IP-cím kiválasztása**, kattintson a **hozzon létre új**, és írja be a nyilvános IP-cím neve. Ebben a példában a nyilvános IP-cím neve *myAGPublicIPAddress*. Fogadja el a további beállításoknál az alapértelmezett értékeket, és kattintson a **OK**.
8. Fogadja el az alapértelmezett értékeket, a figyelő a konfigurációhoz, hagyja a webalkalmazási tűzfal le van tiltva, és kattintson **OK**.
9. Tekintse át a beállításokat az Összegzés lapon, és kattintson **OK** hálózati erőforrások és az Alkalmazásátjáró létrehozása. Az alkalmazás-átjáró hozható létre, várjon, amíg a telepítés sikeresen befejeződik, mielőtt továbblép a következő szakaszban több percig is eltarthat.

### <a name="add-a-subnet"></a>Adjon hozzá egy alhálózatot

1. Kattintson a **összes erőforrás** a bal oldali menüből, majd **myVNet** erőforrások listából.
2. Kattintson a **alhálózatok**, és kattintson a **alhálózati**.

    ![Hozzon létre az alhálózatot](./media/application-gateway-web-application-firewall-portal/application-gateway-subnet.png)

3. Adja meg *myBackendSubnet* neveként az alhálózati majd **OK**.

## <a name="create-backend-servers"></a>Háttér-kiszolgálókat hoz létre

Ebben a példában két virtuális gép az Alkalmazásátjáró háttér-kiszolgálóként használandó hoz létre. Is telepíteni az IIS ellenőrizze, hogy az Alkalmazásátjáró sikeresen létrejött-e a virtuális gépeken.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Kattintson az **Új** lehetőségre.
2. Kattintson a **számítási** majd **Windows Server 2016 Datacenter** kiemelt listájában.
3. Adja meg a virtuális gép ezeket az értékeket:

    - *myVM* – a virtuális gép nevét.
    - *azureuser* – a rendszergazdai felhasználónevet.
    - *Azure123456!* a jelszó.
    - Válassza ki **meglévő**, majd válassza ki *myResourceGroupAG*.

4. Kattintson az **OK** gombra.
5. Válassza ki **DS1_V2** a virtuális gépet, majd kattintson a méretét **válasszon**.
6. Győződjön meg arról, hogy **myVNet** van kiválasztva a virtuális hálózat és az alhálózat van **myBackendSubnet**. 
7. Kattintson a **letiltott** letiltani a rendszerindítási diagnosztika.
8. Kattintson a **OK**, tekintse át a beállításokat az Összegzés lapon, és kattintson a **létrehozása**.

### <a name="install-iis"></a>Az IIS telepítése

1. Az interaktív rendszerhéjat, és győződjön meg arról, hogy van-e állítva **PowerShell**.

    ![Egyéni kiterjesztés telepítése](./media/application-gateway-web-application-firewall-portal/application-gateway-extension.png)

2. A következő parancsot az IIS telepítése a virtuális gépen: 

    ```azurepowershell-interactive
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Hozzon létre egy második virtuális gépet, és a lépéseket, amelyek az imént befejeződött az IIS telepítése. Adja meg *myVM2* a neve pedig a Set-AzureRmVMExtension VMName.

### <a name="add-backend-servers"></a>Adja hozzá a háttérkiszolgálókon

1. Kattintson a **összes erőforrás**, és kattintson a **myAppGateway**.
2. Kattintson a **háttérkészletek**. Alapértelmezett címkészlet automatikusan jött létre az Alkalmazásátjáró. Kattintson a **appGateayBackendPool**.
3. Kattintson a **Hozzáadás cél** minden létrehozott virtuális gép hozzáadása a háttérkészlet.

    ![Adja hozzá a háttérkiszolgálókon](./media/application-gateway-web-application-firewall-portal/application-gateway-backend.png)

4. Kattintson a **Save** (Mentés) gombra.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Hozzon létre egy tárfiókot, és diagnosztika konfigurálása

## <a name="create-a-storage-account"></a>Create a storage account

Ebben az oktatóanyagban az Alkalmazásátjáró tárfiók adatok használ felderítésére és megelőzésére célokra. Log Analytics vagy az Eseményközpont kiválasztásával adatok rögzítéséhez is használhatja.

1. Kattintson a **új** az Azure portál bal felső sarkában található.
2. Válassza ki **tárolási**, majd válassza ki **tárfiók - blob, a fájl, a tábla, a várólista**.
3. Adja meg a tárfiók, jelölje be a nevét **használata meglévő** az erőforráscsoportot, és válassza a **myResourceGroupAG**. Ebben a példában a tárfiók neve van *myagstore1*. Fogadja el a további beállításoknál az alapértelmezett értékeket, és kattintson a **létrehozása**.

## <a name="configure-diagnostics"></a>Diagnosztika konfigurálása

Erőforrásrekord-adatokat a diagnosztika konfigurálja azokat a ApplicationGatewayAccessLog ApplicationGatewayPerformanceLog és ApplicationGatewayFirewallLog naplók.

1. Kattintson a bal oldali menüből **összes erőforrás**, majd válassza ki *myAppGateway*.
2. Kattintson a figyelés, **diagnosztikai naplók**.
3. Kattintson a **diagnosztika beállítás hozzáadása**.
4. Adja meg *myDiagnosticsSettings* a diagnosztikai beállítások néven.
5. Válassza ki **tárfiókba archív**, és kattintson a **konfigurálása** jelölje be a *myagstore1* korábban létrehozott tárfiókot.
6. Válassza ki az alkalmazásnaplók átjáró gyűjtése és megőrzése mellett.
7. Kattintson a **Save** (Mentés) gombra.

    ![Diagnosztika konfigurálása](./media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="test-the-application-gateway"></a>Az Alkalmazásátjáró tesztelése

1. A nyilvános IP-cím keresése a Áttekintés képernyő az Alkalmazásátjáró. Kattintson a **összes erőforrás** majd **myAGPublicIPAddress**.

    ![Rekord alkalmazás átjáró nyilvános IP-címe](./media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png)

2. Másolja a nyilvános IP-címet, és illessze be a böngésző címsorába.

    ![Alkalmazásátjáró tesztelése](./media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan:

> [!div class="checklist"]
> * Hozzon létre egy alkalmazás WAF engedélyezve
> * A háttérkiszolgálók használt virtuális gépek létrehozása
> * Hozzon létre egy tárfiókot, és diagnosztika konfigurálása

További információt a alkalmazásátjárót és a kapcsolódó erőforrások, továbbra is a útmutatókat.