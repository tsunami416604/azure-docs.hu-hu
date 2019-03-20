---
title: Tároló több web sites – az Azure portal application gateway létrehozása
description: Ismerje meg, hogyan hozhat létre egy application gateway, amelyen az Azure portal használatával több webhely.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 2/20/2019
ms.author: victorh
ms.openlocfilehash: 86be94404e7ab492beeebd6a467d23e68e7bce6b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58080167"
---
# <a name="create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>Hozzon létre, és több webhely üzemeltetése az Azure Portalon az application gateway konfigurálása

Használhatja az Azure Portalon [konfigurálása a több webhely üzemeltetése](multiple-site-overview.md) létrehozásakor egy [az application gateway](overview.md). Ez a cikk a háttér-címkészletet a virtuális gépek határozhatók meg. Ezután az Ön tulajdonában lévő tartományok alapján konfigurálhat figyelőket és szabályokat a webes forgalom a készletekben lévő megfelelő kiszolgálókra irányításához. Ez a cikk feltételezi, hogy a saját több tartományok és felhasználási mintái *www.contoso.com* és *www.fabrikam.com*.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Application Gateway létrehozása
> * Virtuális gépek a háttérkiszolgálók létrehozása
> * Háttérkészletek a háttérkiszolgálók létrehozása
> * Háttérbeli figyelők létrehozása
> * Útválasztási szabályok létrehozása
> * CNAME rekord létrehozása a tartományban

![Többhelyes útválasztási példa](./media/create-multiple-sites-portal/scenario.png)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az Ön által létrehozott erőforrások közti kommunikációt egy virtuális hálózaton van szükség. Ebben a példában két alhálózatot hozunk létre: egyet az alkalmazásátjáróhoz, egy másikat pedig a háttérkiszolgálókhoz. Virtuális hálózatot az alkalmazásátjáróval együtt is létrehozhat.

1. Kattintson a **új** az Azure portal bal felső sarkában található.
2. Válassza a **Hálózatkezelés**, majd az **Application Gateway** elemet a Kiemeltek listából.
3. Adja meg a következő értékeket az alkalmazásátjáróhoz:

   - Az alkalmazásátjáró neve *myAppGateway*.
   - Az új erőforráscsoport *myResourceGroupAG*.

     ![Új alkalmazásátjáró létrehozása](./media/create-multiple-sites-portal/application-gateway-create.png)

4. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.
5. Kattintson a **virtuális hálózat választása**, kattintson a **új létrehozása**, majd adja meg ezeket az értékeket a virtuális hálózat:

   - A virtuális hálózat neve *myVNet*.
   - A virtuális hálózat címtere *10.0.0.0/16*.
   - Az alhálózat neve *myAGSubnet*.
   - Az alhálózat címtere *10.0.0.0/24*.

     ![Virtuális hálózat létrehozása](./media/create-multiple-sites-portal/application-gateway-vnet.png)

6. A virtuális hálózat és az alhálózat létrehozásához kattintson az **OK** gombra.
7. Kattintson a **egy nyilvános IP-cím választása**, kattintson a **új létrehozása**, majd adja meg a nyilvános IP-cím nevére. Ebben a példában a nyilvános IP-cím neve *myAGPublicIPAddress*. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.
8. Fogadja el az alapértelmezett értékeket, a figyelőt a konfigurációhoz, hagyja meg a webalkalmazás-tűzfal le van tiltva, és kattintson **OK**.
9. Tekintse át a beállításokat az Összegzés lapon, és kattintson **OK** a hálózati erőforrások és az application gateway létrehozásához. Az application gateway hozhatók létre, várjon, amíg az üzembe helyezés sikeresen befejeződik, mielőtt továbblép a következő szakaszban több percig is eltarthat.

### <a name="add-a-subnet"></a>Alhálózat hozzáadása

1. Kattintson a **Minden erőforrás** elemre a bal oldali menüben, majd kattintson a **myVNet** lehetőségre az erőforráslistában.
2. Kattintson a **alhálózatok**, és kattintson a **alhálózati**.

    ![Alhálózat létrehozása](./media/create-multiple-sites-portal/application-gateway-subnet.png)

3. Adja meg a *myBackendSubnet* nevet az alhálózat neveként, majd kattintson az **OK** gombra.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Ebben a példában két virtuális gépet hozunk létre, amelyeket az alkalmazásátjáró háttérkiszolgálóiként fogunk használni. Az IIS-et is telepíti a virtuális gépeket, ellenőrizze, hogy a forgalom megfelelő útválasztási-e.

1. Kattintson az **Új** lehetőségre.
2. Kattintson a **számítási** majd **Windows Server 2016 Datacenter** a kiemelt lista.
3. Adja meg a következő értékeket a virtuális gép számára:

    - *contosoVM* – a virtuális gép neve.
    - A rendszergazda felhasználóneve: *azureuser*.
    - A jelszó *Azure123456!* .
    - Válassza a **Meglévő használata**, majd a *myResourceGroupAG* lehetőséget.

4. Kattintson az **OK** gombra.
5. A virtuális gép méreténél válassza a **DS1_V2** lehetőséget, majd kattintson a **Kiválasztás** gombra.
6. Győződjön meg róla, hogy virtuális hálózatként a **myVNet**, alhálózatként pedig a **myBackendSubnet** van kiválasztva. 
7. A rendszerindítási diagnosztika letiltásához kattintson a **Letiltva** elemre.
8. Kattintson az **OK** gombra, majd az összefoglaló lapon ellenőrizze a beállításokat, és kattintson a **Létrehozás** gombra.

### <a name="install-iis"></a>Az IIS telepítése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Nyissa meg az interaktív felületet, és győződjön meg róla, hogy a **PowerShell** van beállítva.

    ![Egyéni bővítmény telepítése](./media/create-multiple-sites-portal/application-gateway-extension.png)

2. Futtassa a következő parancsot az IIS a virtuális gépen való telepítéséhez: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. A második virtuális gép létrehozása és az IIS telepítése a lépéseket, amelyek csak végzett használatával. Adja meg a nevei *fabrikamVM* nevét és a Set-AzVMExtension VMName értékét.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Háttérkészletek a virtuális gépek létrehozása

1. Kattintson a **összes erőforrás** majd **myAppGateway**.
2. Kattintson a **háttérkészletek**, és kattintson a **Hozzáadás**.
3. Adjon meg egy *contosoPool* , és adja hozzá *contosoVM* használatával **Hozzáadás cél**.

    ![Háttérkiszolgálók hozzáadása](./media/create-multiple-sites-portal/application-gateway-multisite-backendpool.png)

4. Kattintson az **OK** gombra.
5. Kattintson a **háttérkészletek** majd **Hozzáadás**.
6. Hozzon létre a *fabrikamPool* együtt a *fabrikamVM* használatával a lépéseket, amelyek az imént fejeződött be.

## <a name="create-backend-listeners"></a>Háttérbeli figyelők létrehozása

1. Kattintson a **figyelői** majd **többhelyes**.
2. Adja meg a figyelő ezeket az értékeket:
    
   - *contosoListener* - a figyelő nevét.
   - *www.contoso.com* -cserélje le a gazdagép neve példa a tartomány nevére.

3. Kattintson az **OK** gombra.
4. Hozzon létre egy második figyelő neve használatával *fabrikamListener* és használja a második tartomány nevét. Ebben a példában *www.fabrikam.com* szolgál.

![mult – hely figyelője](media/create-multiple-sites-portal/be-listeners.png)

## <a name="create-routing-rules"></a>Útválasztási szabályok létrehozása

A szabályok a felsorolásuk sorrendjében vannak feldolgozva, és a forgalom az első egyező szabály alapján lesz átirányítva, függetlenül annak sajátlagosságától. Ha például egy adott porton egy alapszintű figyelőt használó és egy többhelyes figyelőt használó szabály is aktív, a többhelyes figyelővel rendelkező szabályt az alapszintű figyelővel rendelkező elé kell venni, hogy a többhelyes szabály a várakozásnak megfelelően működjön. 

Ebben a példában két új szabályt hoz létre, és törli az alkalmazásátjáró létrehozásakor létrehozott alapértelmezett szabályt. 

1. Kattintson a **szabályok** majd **alapszintű**.
2. Adja meg *contosoRule* neve.
3. Válassza ki *contosoListener* a figyelőhöz.
4. Válassza ki *contosoPool* háttérkészlete számára.

    ![Útvonalalapú szabály létrehozása](./media/create-multiple-sites-portal/application-gateway-multisite-rule.png)

5. Kattintson az **OK** gombra.
6. Hozzon létre egy második szabályt, a neveket *fabrikamRule*, *fabrikamListener*, és *fabrikamPool*.
7. Törölje az alapértelmezett szabály nevű *felhasználóval a rule1* , majd kattintás a **törlése**.

## <a name="create-a-cname-record-in-your-domain"></a>CNAME rekord létrehozása a tartományban

Az alkalmazásátjáró nyilvános IP-címmel történő létrehozása után lekérheti a DNS-címet, és a segítségével létrehozhat egy CNAME rekordot a tartományban. Az A rekordok használata nem javasolt, mivel a virtuális IP-cím változhat az alkalmazásátjáró újraindításakor.

1. Kattintson a **összes erőforrás**, és kattintson a **myAGPublicIPAddress**.

    ![Rekord application gateway DNS-cím](./media/create-multiple-sites-portal/application-gateway-multisite-dns.png)

2. Másolja a DNS-címet, és használni értékét egy új CNAME-rekordot a tartományban.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

1. Adja meg a tartománya nevét a böngésző címsorában. Például: http://www.contoso.com.

    ![Contoso webhely tesztelése az alkalmazásátjáróban](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. Változtassa meg a címet a másik tartományára. Ekkor az eredmény a következő példához hasonló:

    ![Fabrikam webhely tesztelése az alkalmazásátjáróban](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="next-steps"></a>További lépések

[Az App Service az Application Gateway konfigurálása](create-web-app.md)