---
title: Oktatóanyag – hozzon létre egy application gateway webalkalmazási tűzfal – Azure portal |} A Microsoft Docs
description: Ismerje meg, hogyan egy webalkalmazási tűzfallal rendelkező application gateway létrehozása az Azure portal használatával.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/25/2019
ms.author: victorh
ms.openlocfilehash: 1284ddec4cd9cea3ea53c20d437550405dd614d9
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905868"
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Az Azure portal használatával egy webalkalmazási tűzfallal rendelkező application gateway létrehozása

> [!div class="op_single_selector"]
>
> - [Azure Portal](application-gateway-web-application-firewall-portal.md)
> - [PowerShell](tutorial-restrict-web-traffic-powershell.md)
> - [Azure CLI](tutorial-restrict-web-traffic-cli.md)
>
> 

Az oktatóanyag bemutatja, hogyan hozhat létre az Azure portal használatával egy [az application gateway](application-gateway-introduction.md) együtt egy [webalkalmazási tűzfal](application-gateway-web-application-firewall-overview.md) (WAF). A WAF [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project)-szabályokkal védi az alkalmazást. Ezek a szabályok olyan támadások ellen nyújtanak védelmet, mint az SQL-injektálás, a Cross-Site Scripting támadások és a munkamenet-eltérítések. Az application gateway létrehozása után tesztelje, hogy ellenőrizze, hogy azok megfelelően működnek. Az Azure Application Gatewayjel az alkalmazás webes forgalom az erőforrásoknál figyelői hozzárendelése portokat, szabályok és hozzáadunk erőforrásokat egy háttérkészlet közvetlen. Az egyszerűség kedvéért a jelen cikk egy egyszerű beállítás nyilvános előtérbeli IP-cím, egy alapszintű figyelő gazdagéphez az application gateway-en egyetlen hely, a háttérkészlet és a egy egyszerű kérelem-útválasztási szabály használható két virtuális gépet használ.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Alkalmazásátjáró létrehozása engedélyezett WAF-fel
> * A háttér-kiszolgálóként használt virtuális gépek létrehozása
> * Tárfiók létrehozása és diagnosztika konfigurálása

![Példa webalkalmazási tűzfalra](./media/application-gateway-web-application-firewall-portal/scenario-waf.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az Azure-hoz az erőforrások közötti kommunikációt, hogy hozzon létre egy virtuális hálózat szükséges. Hozzon létre egy új virtuális hálózatot, vagy használjon egy meglévőt. Ebben a példában létrehozunk egy új virtuális hálózatot. Virtuális hálózatot az alkalmazásátjáróval együtt is létrehozhat. Application Gateway-példány külön alhálózatra jönnek létre. Ebben a példában két alhálózattal hoz létre: egyet az application gateway, a másik pedig a háttérkiszolgálókhoz.

Válassza ki **erőforrás létrehozása** a az Azure Portal bal oldali menüben. A **új** ablak jelenik meg.

Válassza ki **hálózatkezelés** , majd **Application Gateway** a a **kiemelt** listája.

### <a name="basics-page"></a>Alapvető beállítások lap

1. Az a **alapjai** lap, adja meg ezeket az értékeket a következő application gateway beállításait:
   - **Név**: Adja meg *myAppGateway* az application gateway neve.
   - **Erőforráscsoport**: Válassza ki **myResourceGroupAG** az erőforráscsoportnak. Ha még nem létezik, válassza ki a **új létrehozása** létrehozásához.
   - Válassza ki *WAF* az application gateway szintjéhez.![ Új application gateway létrehozása](./media/application-gateway-web-application-firewall-portal/application-gateway-create.png)

Fogadja el az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.

### <a name="settings-page"></a>Beállítások lap

1. Az a **beállítások** lap **alhálózati konfigurációt**válassza **virtuális hálózat választása**. <br>
2. Az a **válasszon egy virtuális hálózatot** lapon jelölje be **új létrehozása**, majd adja meg az értékeket a következő virtuális hálózati beállítások:
   - **Név**: Adja meg *myVNet* a virtuális hálózat neve.
   - **Címtér**: Adja meg *10.0.0.0/16* számára a virtuális hálózat címterét.
   - **Alhálózat neve**: Adja meg *myAGSubnet* az alhálózat neve.<br>Az application gateway alhálózatának csak az application Gateway-átjárókon is tartalmazhat. Egyéb erőforrások nem engedélyezettek.
   - **Alhálózati címtartomány**: Adja meg *10.0.0.0/24* -alhálózat címtartománya.![ Virtuális hálózat létrehozása](./media/application-gateway-web-application-firewall-portal/application-gateway-vnet.png)
3. A virtuális hálózat és az alhálózat létrehozásához kattintson az **OK** gombra.
4. Válassza ki a **előtérbeli IP-konfiguráció**. A **előtérbeli IP-konfiguráció**, ellenőrizze **IP-cím típusa** értékre van állítva **nyilvános**. A **nyilvános IP-cím**, ellenőrizze **új létrehozása** van kiválasztva. <br>Az előtérbeli IP-cím lehet magán- vagy a használati eset megfelelően konfigurálhatja. Ebben a példában választjuk ki egy nyilvános előtérbeli IP-címet. 
5. Adja meg *myAGPublicIPAddress* számára a nyilvános IP-cím neve. 
6. Az alapértelmezett értékeket a többi beállítás esetében fogadja el, majd **OK**.<br>Az egyszerűség kedvéért ez a cikk az alapértelmezett értékek választjuk ki, de be lehet állítani egyéni értékeket a többi beállítás esetében attól függően, a használati eset 

### <a name="summary-page"></a>Összefoglaló lap

Tekintse át a beállításokat a **összefoglalás** oldalra, és kattintson **OK** a virtuális hálózat, nyilvános IP-cím és az application gateway létrehozásához. Az application gateway létrehozása több percig is eltarthat. Várjon, amíg az üzembe helyezés sikeresen befejeződik, mielőtt továbblép a következő szakaszban.

## <a name="add-backend-pool"></a>Háttérkészlet hozzáadása

A háttérkészlet irányíthatja a kérelmeket a háttérkiszolgálók, amely a kérés kiszolgálása lesz használatos. Háttérkészletek összeállítható a hálózati adapterek, a virtual machine scale sets, nyilvános IP-címek, belső IP-címek, teljesen minősített neve (FQDN), és több-bérlős háttéralkalmazások hasonlóan az Azure App Service-ben. Adja hozzá a háttér-tárolók háttérkészletre kell.

Ebben a példában használjuk virtuális gépek, a cél-háttérrendszert. Azt is vagy használjon létező virtuális gépeket, vagy újakat hoz létre. Ebben a példában hozunk létre két virtuális gépet használó Azure háttérkiszolgálóiként az application gateway számára. Ehhez a következő történik:

1. Hozzon létre egy új alhálózatot *myBackendSubnet*, az új virtuális gép létrejön. 
2. 2 új virtuális Gépeket létre *myVM* és *myVM2*háttérkiszolgálóiként használandó.
3. IIS telepítése a virtuális gépeket, ellenőrizze, hogy az application gateway létrehozása sikeres volt.
4. A háttérkiszolgálók hozzáadásához a háttérkészlethez.

### <a name="add-a-subnet"></a>Alhálózat hozzáadása

Adjon hozzá egy alhálózatot a következő lépések során létrehozott virtuális hálózat:

1. Válassza ki **összes erőforrás** adja meg az Azure portal bal oldali menüben *myVNet* a keresési mezőbe, és válassza ki a **myVNet** a keresési eredmények közül.

2. Válassza ki **alhálózatok** a bal oldali menüben, és válassza ki a **+ alhálózat**. 

   ![Alhálózat létrehozása](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Az a **alhálózat hozzáadása** lap, adja meg *myBackendSubnet* számára a **neve** az alhálózatot, és válassza ki a **OK**.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Az Azure Portalon, válassza ki a **erőforrás létrehozása**. A **új** ablak jelenik meg.
2. Válassza ki **számítási** majd **Windows Server 2016 Datacenter** a a **kiemelt** listája. A **hozzon létre egy virtuális gépet** lap jelenik meg.<br>Az Application Gateway is irányítják a forgalmat a virtuális gép a háttérkészletben használni bármilyen típusú. Ebben a példában egy Windows Server 2016 Datacenter használja.
3. Adja meg ezeket az értékeket a **alapjai** lapon találja a következő virtuális gép beállításait:
   - **Erőforráscsoport**: Válassza ki **myResourceGroupAG** az erőforráscsoport neveként.
   - **A virtuális gép neve**: Adja meg *myVM* a virtuális gép neve.
   - **Felhasználónév**: Adja meg *azureuser* az rendszergazdájának felhasználóneve.
   - **Jelszó**: Adja meg *Azure123456!* a rendszergazdai jelszót.
4. Elfogadhatja az alapértelmezett beállításokat, majd **tovább: Lemezek**.  
5. Fogadja el a **lemezek** alapértelmezett lapot, majd **tovább: Hálózatkezelés**.
6. Az a **hálózatkezelés** lapon ellenőrizze, hogy **myVNet** van kiválasztva a **virtuális hálózati** és a **alhálózati** értékre van állítva  **myBackendSubnet**. Elfogadhatja az alapértelmezett beállításokat, majd **tovább: Felügyeleti**.<br>Az Application Gateway képes kommunikálni az, hogy a virtuális hálózaton kívüli példányok, de annak biztosítására, IP-kapcsolat van szükségünk. 
7. Az a **felügyeleti** lapra, és állítsa **rendszerindítási diagnosztika** való **ki**. Elfogadhatja az alapértelmezett beállításokat, majd **felülvizsgálat + létrehozása**.
8. Az a **tekintse át + létrehozása** lapon tekintse át a beállításokat, javíthatja az esetleges érvényesítési hibákat, és válassza ki **létrehozás**.
9. Várjon, amíg a virtuális gép létrehozásának befejeződését a továbblépés előtt.

### <a name="install-iis-for-testing"></a>Az IIS telepítése teszteléshez

Ebben a példában azt telepíti az IIS a virtuális gépek csak az Azure application gateway létrehozása sikerült ellenőrzése céljából. 

1. Nyissa meg [az Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Ehhez válassza ki a **Cloud Shell** az Azure Portalon, és ezután válassza a felső navigációs sávon **PowerShell** a legördülő listából. 

   ![Egyéni bővítmény telepítése](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

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

3. Hozzon létre egy második virtuális gépet, és az IIS telepítése a korábban végrehajtott lépések segítségével. Használat *myVM2* a virtuális gép nevét, valamint a a **VMName** beállításaként a **Set-AzVMExtension** parancsmagot.

### <a name="add-backend-servers-to-backend-pool"></a>Háttérkiszolgálók háttérkészlet hozzáadása

1. Válassza ki **összes erőforrás**, majd válassza ki **myAppGateway**.

2. Válassza ki **háttérkészletek** a bal oldali menüből. Az Azure automatikusan létrejön egy alapértelmezett címkészlet **appGatewayBackendPool**, az application gateway létrehozása során. 

3. Válassza ki **appGatewayBackendPool**.

4. A **célok**válassza **virtuális gép** a legördülő listából.

5. A **virtuális gép** és **hálózati adapterek**, jelölje be a **myVM** és **myVM2** virtuális gépek és a kapcsolódó hálózati a legördülő listákból felületek.

   ![Háttérkiszolgálók hozzáadása](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Kattintson a **Mentés** gombra.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Tárfiók létrehozása és diagnosztika konfigurálása

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Ebben az oktatóanyagban az alkalmazásátjáró tárfiókban tárolja az adatokat észlelési és megelőzési célra. Jegyezze fel az adatokat az Azure Monitor naplóira vagy akár Eseményközpontba is használhatja.

1. Kattintson a **új** az Azure portal bal felső sarkában található.
2. Válassza a **Storage** elemet, majd a **Tárfiók – blob, fájl, tábla, üzenetsor** lehetőséget.
3. Adja meg a storage-fiók, jelölje be a nevét **meglévő használata** az erőforráscsoportot, és válassza ki a **myResourceGroupAG**. Ebben a példában a tárfiók neve van *myagstore1*. Az alapértelmezett értékeket a többi beállítás esetében fogadja el, és kattintson a **létrehozás**.

### <a name="configure-diagnostics"></a>Diagnosztika konfigurálása

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

Bár az application gateway létrehozásához az IIS nem szükséges, telepítette azt ebben a rövid, ellenőrizze, hogy az Azure sikeresen létrejött-e az application gateway. Az IIS használatával az application gateway teszteléséhez:

1. Az application gateway a nyilvános IP-cím keresése a **áttekintése** lap.![ Jegyezze fel az application gateway nyilvános IP-cím](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)választhatja azt is megteheti, **összes erőforrás**, adja meg *myAGPublicIPAddress* keresési mezőbe, majd kattintson a keresés eredmények. Az Azure nyilvános IP-címét jeleníti meg a **áttekintése** lapot.
2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.
3. Ellenőrizze a választ. Érvényes válasz ellenőrzi, hogy az application gateway létrehozása sikeresen megtörtént, és képes sikeresen csatlakozott a háttérszolgáltatás használatára.![Az alkalmazásátjáró tesztelése](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az application gateway segítségével létrehozott erőforrásokat, távolítsa el az erőforráscsoportot. Az erőforráscsoport eltávolításával is eltávolítja az application gateway és az összes kapcsolódó erőforrás. 

Az erőforráscsoport eltávolítása:

1. Az Azure portal bal oldali menüben válassza ki **erőforráscsoportok**.
2. Az a **erőforráscsoportok** lapon, keressen rá a **myResourceGroupAG** a listában, majd válassza ki azt.
3. Az a **erőforráscsoport oldalán**válassza **erőforráscsoport törlése**.
4. Adja meg *myResourceGroupAG* a **írja be az ERŐFORRÁSCSOPORT nevét** majd **törlése**

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan lehet:

> [!div class="checklist"]
> * Alkalmazásátjáró létrehozása engedélyezett WAF-fel
> * A háttér-kiszolgálóként használt virtuális gépek létrehozása
> * Tárfiók létrehozása és diagnosztika konfigurálása

Az application Gateway átjárók és a kapcsolódó erőforrásokkal kapcsolatos további információkért folytassa az útmutató cikkekre.
