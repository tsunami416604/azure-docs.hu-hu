---
title: Oktatóanyag – az URL-cím-alapú útválasztási szabályok – Azure portal application gateway létrehozása
description: Ebben az oktatóanyagban megismerheti, hogyan URL-cím-alapú útválasztási szabályok létrehozása egy application gateway és a virtuálisgép-méretezési csoportot, az Azure portal használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 4/18/2019
ms.author: victorh
ms.openlocfilehash: 5307f7674635fd33241e1faba9bb0b7c0432d10b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66134771"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Oktatóanyag: Hozzon létre egy application gateway-alapú útválasztási szabályok az Azure portal használatával

Az Azure Portalon való konfigurálásához használható [URL-cím-alapú útválasztási szabályok](url-route-overview.md) létrehozásakor egy [az application gateway](overview.md). Ebben az oktatóanyagban háttérkészletek virtual machines használatával hoz létre. Ezután hozzon létre útválasztási szabályokat, hogy ellenőrizze, hogy a webes forgalom érkezik meg a megfelelő kiszolgálókat a készletekben.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Application Gateway létrehozása
> * Virtuális gépek a háttérkiszolgálók létrehozása
> * Háttérkészletek a háttérkiszolgálók létrehozása
> * Hozzon létre egy háttér-figyelő
> * Útválasztó-alapú szabály létrehozása

![URL-útválasztási példa](./media/create-url-route-portal/scenario.png)

Ha szeretné, ez az oktatóanyag segítségével befejezheti [Azure CLI-vel](tutorial-url-route-cli.md) vagy [Azure PowerShell-lel](tutorial-url-route-powershell.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Egy virtuális hálózatot hoz létre az erőforrások közötti kommunikációra van szükség. Ebben a példában két alhálózatot hozunk létre: egyet az alkalmazásátjáróhoz, egy másikat pedig a háttérkiszolgálókhoz. Létrehozhat egy virtuális hálózatot hoz létre az application gateway egy időben.

1. Válassza ki **új** az Azure portal bal felső sarkában található.
2. Válassza a **Hálózatkezelés**, majd az **Application Gateway** elemet a Kiemeltek listából.
3. Adja meg a következő értékeket az alkalmazásátjáróhoz:

   - Az alkalmazásátjáró neve *myAppGateway*.
   - Az új erőforráscsoport *myResourceGroupAG*.

     ![Új alkalmazásátjáró létrehozása](./media/create-url-route-portal/application-gateway-create.png)

4. Az alapértelmezett értékeket a többi beállítás esetében fogadja el, majd **OK**.
5. Válassza ki **virtuális hálózat választása**válassza **új létrehozása**, majd adja meg ezeket az értékeket a virtuális hálózat:

   - A virtuális hálózat neve *myVNet*.
   - A virtuális hálózat címtere *10.0.0.0/16*.
   - Az alhálózat neve *myAGSubnet*.
   - Az alhálózat címtere *10.0.0.0/24*.

     ![Virtuális hálózat létrehozása](./media/create-url-route-portal/application-gateway-vnet.png)

6. Válassza ki **OK** hozhat létre a virtuális hálózatot és alhálózatot.
7. Válassza ki **egy nyilvános IP-cím választása**, jelölje be **új létrehozása**, majd adja meg a nyilvános IP-cím nevére. Ebben a példában a nyilvános IP-cím neve *myAGPublicIPAddress*. Az alapértelmezett értékeket a többi beállítás esetében fogadja el, majd **OK**.
8. Fogadja el az alapértelmezett értékeket, a figyelőt a konfigurációhoz, hagyja meg a webalkalmazás-tűzfal le van tiltva, és válassza **OK**.
9. Tekintse át a beállításokat az Összegzés lapon, és válassza ki **OK** a hálózati erőforrások és az application gateway létrehozásához. Az application gateway hozhatók létre, várjon, amíg az üzembe helyezés sikeresen befejeződik, mielőtt továbblép a következő szakaszban több percig is eltarthat.

### <a name="add-a-subnet"></a>Alhálózat hozzáadása

1. Válassza ki **összes erőforrás** a bal oldali menüben, és válassza ki a **myVNet** az erőforrások listájában.
2. Válassza ki **alhálózatok**, majd válassza ki **alhálózati**.

    ![Alhálózat létrehozása](./media/create-url-route-portal/application-gateway-subnet.png)

3. Adja meg *myBackendSubnet* nevét az alhálózat és válassza ki a **OK**.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Ebben a példában az application gateway használható háttérkiszolgálóiként három virtuális gépet hoz létre. Az IIS-et is telepíti az application gateway sikeres létrehozásának ellenőrzéséhez a virtuális gépeket.

1. Válassza az **Új** lehetőséget.
2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet a Kiemeltek listából.
3. Adja meg a következő értékeket a virtuális gép számára:

    - A virtuális gép neve: *myVM1*.
    - A rendszergazda felhasználóneve: *azureuser*.
    - *Azure123456!* .
    - Válassza a **Meglévő használata**, majd a *myResourceGroupAG* lehetőséget.

4. Kattintson az **OK** gombra.
5. Válassza ki **DS1_V2** méretét, a virtuális gépet, és válassza **kiválasztása**.
6. Győződjön meg róla, hogy virtuális hálózatként a **myVNet**, alhálózatként pedig a **myBackendSubnet** van kiválasztva. 
7. A rendszerindítási diagnosztika letiltásához kattintson a **Letiltva** elemre.
8. Kattintson az **OK** gombra, majd az összefoglaló lapon ellenőrizze a beállításokat, és kattintson a **Létrehozás** gombra.

### <a name="install-iis"></a>Az IIS telepítése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Nyissa meg az interaktív shell, és győződjön meg arról, hogy értékre van állítva, **PowerShell**.

    ![Egyéni bővítmény telepítése](./media/create-url-route-portal/application-gateway-extension.png)

2. Futtassa a következő parancsot az IIS a virtuális gépen való telepítéséhez: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName myVM1 `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Két további virtuális gépek létrehozása és az IIS telepítése a lépéseket, amelyek csak végzett használatával. Adja meg a nevei *myVM2* és *myVM3* a neveket és a Set-AzVMExtension VMName értékeit.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Háttérkészletek a virtuális gépek létrehozása

1. Válassza ki **összes erőforrás** majd **myAppGateway**.
2. Válassza ki **háttérkészletek**. Az alapértelmezett készlet automatikusan létrejött az alkalmazásátjáróval együtt. Válassza ki **appGatewayBackendPool**.
3. Válassza ki **Hozzáadás cél** hozzáadása *myVM1* appGatewayBackendPool való.

    ![Háttérkiszolgálók hozzáadása](./media/create-url-route-portal/application-gateway-backend.png)

4. Kattintson a **Mentés** gombra.
5. Válassza ki **háttérkészletek** majd **Hozzáadás**.
6. Adjon meg egy *imagesBackendPool* , és adja hozzá *myVM2* használatával **Hozzáadás cél**.
7. Kattintson az **OK** gombra.
8. Válassza ki **Hozzáadás** újra hozzáadni a nevű másik háttérkészletnek *videoBackendPool* , és adja hozzá *myVM3* rá.

## <a name="create-a-backend-listener"></a>Hozzon létre egy háttér-figyelő

1. Válassza ki **figyelői** , és válassza a **alapszintű**.
2. Adja meg *myBackendListener* névként *myFrontendPort* a frontend-port neve, majd *8080-as* , a portot a figyelőhöz.
3. Kattintson az **OK** gombra.

## <a name="create-a-path-based-routing-rule"></a>Útválasztó-alapú szabály létrehozása

1. Válassza ki **szabályok** majd **-alapú**.
2. Adja meg *felhasználóval a rule2* neve.
3. Adja meg *lemezképek* az első elérési út neve. Adja meg */images/* \* elérési úthoz. Válassza ki **imagesBackendPool** háttérkészlete számára.
4. Adja meg *videó* a második elérési út neve. Adja meg */video/* \* elérési úthoz. Válassza ki **videoBackendPool** háttérkészlete számára.

    ![Útvonalalapú szabály létrehozása](./media/create-url-route-portal/application-gateway-route-rule.png)

5. Kattintson az **OK** gombra.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

1. Válassza ki **összes erőforrás**, majd válassza ki **myAGPublicIPAddress**.

    ![Alkalmazásátjáró nyilvános IP-címének rögzítése](./media/create-url-route-portal/application-gateway-record-ag-address.png)

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Például: http://40.121.222.19.

    ![Az alap URL-cím tesztelése az alkalmazásátjáróban](./media/create-url-route-portal/application-gateway-iistest.png)

3. Módosítsa az URL-CÍMÉT a http://&lt;ip-cím&gt;: 8080/images/test.htm, és cserélje le &lt;ip-cím&gt; az IP-címet, és alábbihoz az alábbi példához hasonlóan:

    ![Képek URL-címének tesztelése az alkalmazásátjáróban](./media/create-url-route-portal/application-gateway-iistest-images.png)

4. Módosítsa az URL-CÍMÉT a http://&lt;ip-cím&gt;: 8080/video/test.htm, és cserélje le &lt;ip-cím&gt; az IP-címet, és alábbihoz az alábbi példához hasonlóan:

    ![Videók URL-címének tesztelése az alkalmazásátjáróban](./media/create-url-route-portal/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az application gateway segítségével létrehozott erőforrásokat, távolítsa el az erőforráscsoportot. Az erőforráscsoport eltávolításával is eltávolítja az application gateway és az összes kapcsolódó erőforrás. 

Az erőforráscsoport eltávolítása:

1. Az Azure portal bal oldali menüben válassza ki **erőforráscsoportok**.
2. Az a **erőforráscsoportok** lapon, keressen rá a **myResourceGroupAG** a listában, majd válassza ki azt.
3. Az a **erőforráscsoport oldalán**válassza **erőforráscsoport törlése**.
4. Adja meg *myResourceGroupAG* a **írja be az ERŐFORRÁSCSOPORT nevét** majd **törlése**.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók a mi mindent az Azure Application Gateway segítségével](application-gateway-introduction.md)
