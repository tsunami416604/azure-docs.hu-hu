---
title: Hozzon létre egy application gateway URL-cím-alapú útválasztási szabályok – Azure portal
description: Megtudhatja, hogyan URL-cím-alapú útválasztási szabályok létrehozása egy application gateway és a virtuálisgép-méretezési csoportot, az Azure portal használatával.
services: application-gateway
author: vhorne
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 3/26/2018
ms.author: victorh
ms.openlocfilehash: 5bec7be5f7ad744960d2602aaf24fec51d869267
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056249"
---
# <a name="create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Hozzon létre egy application gateway-alapú útválasztási szabályok az Azure portal használatával

Az Azure Portalon való konfigurálásához használható [URL-cím-alapú útválasztási szabályok](application-gateway-url-route-overview.md) létrehozásakor egy [az application gateway](application-gateway-introduction.md). Ebben az oktatóanyagban háttérkészletek virtual machines használatával hoz létre. Ezután hozzon létre útválasztási szabályokat, hogy ellenőrizze, hogy a webes forgalom érkezik meg a megfelelő kiszolgálókat a készletekben.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Application Gateway létrehozása
> * Virtuális gépek a háttérkiszolgálók létrehozása
> * Háttérkészletek a háttérkiszolgálók létrehozása
> * Hozzon létre egy háttér-figyelő
> * Útválasztó-alapú szabály létrehozása

![URL-útválasztási példa](./media/application-gateway-create-url-route-portal/scenario.png)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a [http://portal.azure.com](http://portal.azure.com) címen.

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az Ön által létrehozott erőforrások közti kommunikációt egy virtuális hálózaton van szükség. Ebben a példában két alhálózatot hozunk létre: egyet az alkalmazásátjáróhoz, egy másikat pedig a háttérkiszolgálókhoz. Virtuális hálózatot az alkalmazásátjáróval együtt is létrehozhat.

1. Kattintson a **új** az Azure portal bal felső sarkában található.
2. Válassza a **Hálózatkezelés**, majd az **Application Gateway** elemet a Kiemeltek listából.
3. Adja meg a következő értékeket az alkalmazásátjáróhoz:

    - Az alkalmazásátjáró neve *myAppGateway*.
    - Az új erőforráscsoport *myResourceGroupAG*.

    ![Új alkalmazásátjáró létrehozása](./media/application-gateway-create-url-route-portal/application-gateway-create.png)

4. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.
5. Kattintson a **virtuális hálózat választása**, kattintson a **új létrehozása**, majd adja meg ezeket az értékeket a virtuális hálózat:

    - A virtuális hálózat neve *myVNet*.
    - A virtuális hálózat címtere *10.0.0.0/16*.
    - Az alhálózat neve *myAGSubnet*.
    - Az alhálózat címtere *10.0.0.0/24*.

    ![Virtuális hálózat létrehozása](./media/application-gateway-create-url-route-portal/application-gateway-vnet.png)

6. A virtuális hálózat és az alhálózat létrehozásához kattintson az **OK** gombra.
7. Kattintson a **egy nyilvános IP-cím választása**, kattintson a **új létrehozása**, majd adja meg a nyilvános IP-cím nevére. Ebben a példában a nyilvános IP-cím neve *myAGPublicIPAddress*. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.
8. Fogadja el az alapértelmezett értékeket, a figyelőt a konfigurációhoz, hagyja meg a webalkalmazás-tűzfal le van tiltva, és kattintson **OK**.
9. Tekintse át a beállításokat az Összegzés lapon, és kattintson **OK** a hálózati erőforrások és az application gateway létrehozásához. Az application gateway hozhatók létre, várjon, amíg az üzembe helyezés sikeresen befejeződik, mielőtt továbblép a következő szakaszban több percig is eltarthat.

### <a name="add-a-subnet"></a>Alhálózat hozzáadása

1. Kattintson a **Minden erőforrás** elemre a bal oldali menüben, majd kattintson a **myVNet** lehetőségre az erőforráslistában.
2. Kattintson a **alhálózatok**, és kattintson a **alhálózati**.

    ![Alhálózat létrehozása](./media/application-gateway-create-url-route-portal/application-gateway-subnet.png)

3. Adja meg a *myBackendSubnet* nevet az alhálózat neveként, majd kattintson az **OK** gombra.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Ebben a példában az application gateway használható háttérkiszolgálóiként három virtuális gépet hoz létre. A virtuális gépeken emellett telepíti az IIS-t annak ellenőrzéséhez, hogy az alkalmazásátjáró sikeresen létrejött-e.

1. Kattintson az **Új** lehetőségre.
2. Kattintson a **számítási** majd **Windows Server 2016 Datacenter** a kiemelt lista.
3. Adja meg a következő értékeket a virtuális gép számára:

    - A virtuális gép neve: *myVM1*.
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

    ![Egyéni bővítmény telepítése](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. Futtassa a következő parancsot az IIS a virtuális gépen való telepítéséhez: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName myVM1 `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Két további virtuális gépek létrehozása és az IIS telepítése a lépéseket, amelyek csak végzett használatával. Adja meg a nevei *myVM2* és *myVM3* a neveket és a Set-AzureRmVMExtension VMName értékeit.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Háttérkészletek a virtuális gépek létrehozása

1. Kattintson a **összes erőforrás** majd **myAppGateway**.
2. Kattintson a **Háttérkészletek** lehetőségre. Az alapértelmezett készlet automatikusan létrejött az alkalmazásátjáróval együtt. Kattintson az **appGatewayBackendPool** lehetőségre.
3. Kattintson a **Hozzáadás cél** hozzáadása *myVM1* appGatewayBackendPool való.

    ![Háttérkiszolgálók hozzáadása](./media/application-gateway-create-url-route-portal/application-gateway-backend.png)

4. Kattintson a **Save** (Mentés) gombra.
5. Kattintson a **háttérkészletek** majd **Hozzáadás**.
6. Adjon meg egy *imagesBackendPool* , és adja hozzá *myVM2* használatával **Hozzáadás cél**.
7. Kattintson az **OK** gombra.
8. Kattintson a **Hozzáadás** újra hozzáadni a nevű másik háttérkészletnek *videoBackendPool* , és adja hozzá *myVM3* rá.

## <a name="create-a-backend-listener"></a>Hozzon létre egy háttér-figyelő

1. Kattintson a **figyelői** , és kattintson a **alapszintű**.
2. Adja meg *myBackendListener* névként *myFrontendPort* a frontend-port neve, majd *8080-as* , a portot a figyelőhöz.
3. Kattintson az **OK** gombra.

## <a name="create-a-path-based-routing-rule"></a>Útválasztó-alapú szabály létrehozása

1. Kattintson a **szabályok** majd **-alapú**.
2. Adja meg *felhasználóval a rule2* neve.
3. Adja meg *lemezképek* az első elérési út neve. Adja meg */images/* \* elérési úthoz. Válassza ki **imagesBackendPool** háttérkészlete számára.
4. Adja meg *videó* a második elérési út neve. Adja meg */video/* \* elérési úthoz. Válassza ki **videoBackendPool** háttérkészlete számára.

    ![Útvonalalapú szabály létrehozása](./media/application-gateway-create-url-route-portal/application-gateway-route-rule.png)

5. Kattintson az **OK** gombra.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

1. Kattintson a **összes erőforrás**, és kattintson a **myAGPublicIPAddress**.

    ![Alkalmazásátjáró nyilvános IP-címének rögzítése](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Például: http://http://40.121.222.19.

    ![Az alap URL-cím tesztelése az alkalmazásátjáróban](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

3. Módosítsa az URL-CÍMÉT a http://&lt;ip-cím&gt;: 8080/images/test.htm, és cserélje le &lt;ip-cím&gt; az IP-címet, és alábbihoz az alábbi példához hasonlóan:

    ![Képek URL-címének tesztelése az alkalmazásátjáróban](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

4. Módosítsa az URL-CÍMÉT a http://&lt;ip-cím&gt;: 8080/video/test.htm, és cserélje le &lt;ip-cím&gt; az IP-címet, és alábbihoz az alábbi példához hasonlóan:

    ![Videók URL-címének tesztelése az alkalmazásátjáróban](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan lehet

> [!div class="checklist"]
> * Application Gateway létrehozása
> * Virtuális gépek a háttérkiszolgálók létrehozása
> * Háttérkészletek a háttérkiszolgálók létrehozása
> * Hozzon létre egy háttér-figyelő
> * Útválasztó-alapú szabály létrehozása

Az application Gateway átjárók és a kapcsolódó erőforrásokkal kapcsolatos további információkért folytassa az útmutató cikkekre.
