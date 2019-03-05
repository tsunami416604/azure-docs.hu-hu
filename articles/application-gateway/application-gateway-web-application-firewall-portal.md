---
title: Hozzon létre egy application gateway webalkalmazási tűzfal – Azure portal |} A Microsoft Docs
description: Ismerje meg, hogyan egy webalkalmazási tűzfallal rendelkező application gateway létrehozása az Azure portal használatával.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: d89e5d6fd21fdc37bffcc730fb20bae30ecc4f7c
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57315027"
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Az Azure portal használatával egy webalkalmazási tűzfallal rendelkező application gateway létrehozása

Az Azure portal segítségével hozzon létre egy [az application gateway](application-gateway-introduction.md) együtt egy [webalkalmazási tűzfal](application-gateway-web-application-firewall-overview.md) (WAF). A WAF [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project)-szabályokkal védi az alkalmazást. Ezek a szabályok olyan támadások ellen nyújtanak védelmet, mint az SQL-injektálás, a Cross-Site Scripting támadások és a munkamenet-eltérítések.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Alkalmazásátjáró létrehozása engedélyezett WAF-fel
> * A háttér-kiszolgálóként használt virtuális gépek létrehozása
> * Tárfiók létrehozása és diagnosztika konfigurálása

![Példa webalkalmazási tűzfalra](./media/application-gateway-web-application-firewall-portal/scenario-waf.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) címen.

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az Ön által létrehozott erőforrások közti kommunikációt egy virtuális hálózaton van szükség. Ebben a példában két alhálózatot hozunk létre: egyet az alkalmazásátjáróhoz, egy másikat pedig a háttérkiszolgálókhoz. Virtuális hálózatot az alkalmazásátjáróval együtt is létrehozhat.

1. Kattintson a **új** az Azure portal bal felső sarkában található.
2. Válassza a **Hálózatkezelés**, majd az **Application Gateway** elemet a Kiemeltek listából.
3. Adja meg a következő értékeket az alkalmazásátjáróhoz:

    - Az alkalmazásátjáró neve *myAppGateway*.
    - Az új erőforráscsoport *myResourceGroupAG*.
    - Válassza ki *WAF* az application gateway szintjéhez.

    ![Új alkalmazásátjáró létrehozása](./media/application-gateway-web-application-firewall-portal/application-gateway-create.png)

4. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.
5. Kattintson a **virtuális hálózat választása**, kattintson a **új létrehozása**, majd adja meg ezeket az értékeket a virtuális hálózat:

    - A virtuális hálózat neve *myVNet*.
    - A virtuális hálózat címtere *10.0.0.0/16*.
    - Az alhálózat neve *myAGSubnet*.
    - Az alhálózat címtere *10.0.0.0/24*.

    ![Virtuális hálózat létrehozása](./media/application-gateway-web-application-firewall-portal/application-gateway-vnet.png)

6. A virtuális hálózat és az alhálózat létrehozásához kattintson az **OK** gombra.
7. Kattintson a **egy nyilvános IP-cím választása**, kattintson a **új létrehozása**, majd adja meg a nyilvános IP-cím nevére. Ebben a példában a nyilvános IP-cím neve *myAGPublicIPAddress*. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.
8. Fogadja el az alapértelmezett értékeket, a figyelőt a konfigurációhoz, hagyja meg a webalkalmazás-tűzfal le van tiltva, és kattintson **OK**.
9. Tekintse át a beállításokat az Összegzés lapon, és kattintson **OK** hálózati erőforrások és az application gateway létrehozásához. Az application gateway hozhatók létre, várjon, amíg az üzembe helyezés sikeresen befejeződik, mielőtt továbblép a következő szakaszban több percig is eltarthat.

### <a name="add-a-subnet"></a>Alhálózat hozzáadása

1. Kattintson a **Minden erőforrás** elemre a bal oldali menüben, majd kattintson a **myVNet** lehetőségre az erőforráslistában.
2. Kattintson a **alhálózatok**, és kattintson a **alhálózati**.

    ![Alhálózat létrehozása](./media/application-gateway-web-application-firewall-portal/application-gateway-subnet.png)

3. Adja meg a *myBackendSubnet* nevet az alhálózat neveként, majd kattintson az **OK** gombra.

## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a példában két virtuális gépet hozunk létre, amelyeket az alkalmazásátjáró háttérkiszolgálóiként fogunk használni. A virtuális gépeken emellett telepíti az IIS-t annak ellenőrzéséhez, hogy az alkalmazásátjáró sikeresen létrejött-e.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Kattintson az **Új** lehetőségre.
2. Kattintson a **számítási** majd **Windows Server 2016 Datacenter** a kiemelt lista.
3. Adja meg a következő értékeket a virtuális gép számára:

    - A virtuális gép neve *myVM*.
    - A rendszergazda felhasználóneve: *azureuser*.
    - A jelszó *Azure123456!* .
    - Válassza a **Meglévő használata**, majd a *myResourceGroupAG* lehetőséget.

4. Kattintson az **OK** gombra.
5. A virtuális gép méreténél válassza a **DS1_V2** lehetőséget, majd kattintson a **Kiválasztás** gombra.
6. Győződjön meg róla, hogy virtuális hálózatként a **myVNet**, alhálózatként pedig a **myBackendSubnet** van kiválasztva. 
7. A rendszerindítási diagnosztika letiltásához kattintson a **Letiltva** elemre.
8. Kattintson az **OK** gombra, majd az összefoglaló lapon ellenőrizze a beállításokat, és kattintson a **Létrehozás** gombra.

### <a name="install-iis"></a>Az IIS telepítése

1. Nyissa meg az interaktív felületet, és győződjön meg róla, hogy a **PowerShell** van beállítva.

    ![Egyéni bővítmény telepítése](./media/application-gateway-web-application-firewall-portal/application-gateway-extension.png)

2. Futtassa a következő parancsot az IIS a virtuális gépen való telepítéséhez: 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Hozzon létre egy második virtuális gépet, és telepítse az IIS-t az imént befejezett lépésekkel. Adja meg *myVM2* nevéből és a Set-AzVMExtension VMName.

### <a name="add-backend-servers"></a>Háttérkiszolgálók hozzáadása

1. Kattintson a **összes erőforrás**, és kattintson a **myAppGateway**.
2. Kattintson a **Háttérkészletek** lehetőségre. Az alapértelmezett készlet automatikusan létrejött az alkalmazásátjáróval együtt. Kattintson az **appGatewayBackendPool** lehetőségre.
3. Kattintson a **Hozzáadás cél** minden Ön által létrehozott virtuális gép hozzáadásához a háttérkészlethez.

    ![Háttérkiszolgálók hozzáadása](./media/application-gateway-web-application-firewall-portal/application-gateway-backend.png)

4. Kattintson a **Save** (Mentés) gombra.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Tárfiók létrehozása és diagnosztika konfigurálása

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

Ebben az oktatóanyagban az alkalmazásátjáró tárfiókban tárolja az adatokat észlelési és megelőzési célra. Jegyezze fel az adatokat az Azure Monitor naplóira vagy akár Eseményközpontba is használhatja.

1. Kattintson a **új** az Azure portal bal felső sarkában található.
2. Válassza a **Storage** elemet, majd a **Tárfiók – blob, fájl, tábla, üzenetsor** lehetőséget.
3. Adja meg a storage-fiók, jelölje be a nevét **meglévő használata** az erőforráscsoportot, és válassza ki a **myResourceGroupAG**. Ebben a példában a tárfiók neve van *myagstore1*. Az alapértelmezett értékeket a többi beállítás esetében fogadja el, és kattintson a **létrehozás**.

## <a name="configure-diagnostics"></a>Diagnosztika konfigurálása

Konfigurálja a diagnosztikát az adatok az ApplicationGatewayAccessLog, az ApplicationGatewayPerformanceLog és az ApplicationGatewayFirewallLog naplóba rögzítéséhez.

1. A bal oldali menüben kattintson a **összes erőforrás**, majd válassza ki *myAppGateway*.
2. Kattintson a figyelés területen **diagnosztikai naplók**.
3. Kattintson a **diagnosztikai beállítás hozzáadása**.
4. Adja meg *myDiagnosticsSettings* számára a diagnosztikai beállításokat.
5. Válassza ki **archiválás tárfiókba**, és kattintson a **konfigurálása** válassza ki a *myagstore1* tárfiókot, amelyet korábban hozott létre.
6. Válassza ki az application gateway-naplók gyűjtése és megőrzése.
7. Kattintson a **Save** (Mentés) gombra.

    ![Diagnosztika konfigurálása](./media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

1. Keresse meg az alkalmazásátjáró nyilvános IP-címét az Áttekintés képernyőn. Kattintson a **összes erőforrás** majd **myAGPublicIPAddress**.

    ![Alkalmazásátjáró nyilvános IP-címének rögzítése](./media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png)

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

    ![Az alkalmazásátjáró tesztelése](./media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan lehet:

> [!div class="checklist"]
> * Alkalmazásátjáró létrehozása engedélyezett WAF-fel
> * A háttér-kiszolgálóként használt virtuális gépek létrehozása
> * Tárfiók létrehozása és diagnosztika konfigurálása

Az application Gateway átjárók és a kapcsolódó erőforrásokkal kapcsolatos további információkért folytassa az útmutató cikkekre.
