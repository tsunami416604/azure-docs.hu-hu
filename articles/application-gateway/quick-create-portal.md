---
title: Rövid útmutató – Webes forgalom irányítása az Azure Application Gatewayjel – Azure Portal | Microsoft Docs
description: Megismerheti, hogyan hozhat létre az Azure Portallal egy olyan Azure Application Gatewayt, amely a háttérkészletben lévő virtuális gépekhez irányítja a webes forgalmat.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 12/21/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: f9bae7ec39b8c9d52feb846360deda5a6367e3e6
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54038568"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Gyors útmutató: A közvetlen webes forgalom az Azure Application Gatewayjel – Azure portal

Ez rövid útmutató bemutatja, hogyan hozhat létre gyorsan az Azure Portallal egy olyan alkalmazásátjárót, amelynek háttérkészletében két virtuális gép található. Ezután tesztelheti, hogy megfelelően működik-e. Az Azure Application Gatewayjel, az alkalmazás webes forgalom meghatározott forrásokhoz való közvetlen: figyelői portok, szabályok létrehozásával és hozzárendelésével hozzáadunk erőforrásokat egy háttérkészlet.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az Azure-hoz az erőforrások közötti kommunikációt, hogy hozzon létre egy virtuális hálózat szükséges. Ebben a példában két alhálózattal hoz létre: egyet az application gateway, a másik pedig a háttérkiszolgálókhoz. Virtuális hálózatot az alkalmazásátjáróval együtt is létrehozhat.

1. Válassza ki **erőforrás létrehozása** a az Azure Portal bal oldali menüben. A **új** ablak jelenik meg.

2. Válassza ki **hálózatkezelés** , majd **Application Gateway** a a **kiemelt** listája.

### <a name="basics-page"></a>Alapvető beállítások lap

1. Az a **alapjai** lap, adja meg ezeket az értékeket a következő application gateway beállításait:

    - **Név**: Adja meg *myAppGateway* az application gateway neve.
    - **Erőforráscsoport**: Válassza ki **myResourceGroupAG** az erőforráscsoportnak. Ha még nem létezik, válassza ki a **új létrehozása** létrehozásához.

    ![Új alkalmazásátjáró létrehozása](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

2. Az alapértelmezett értékeket a többi beállítás esetében fogadja el, majd **OK**.

### <a name="settings-page"></a>Beállítások lap

1. Az a **beállítások** lap **alhálózati konfigurációt**válassza **virtuális hálózat választása**.

2. Az a **válasszon egy virtuális hálózatot** lapon jelölje be **új létrehozása**, majd adja meg az értékeket a következő virtuális hálózati beállítások:

    - **Név**: Adja meg *myVNet* a virtuális hálózat neve.

    - **Címtér**: Adja meg *10.0.0.0/16* számára a virtuális hálózat címterét.

    - **Alhálózat neve**: Adja meg *myAGSubnet* az alhálózat neve.

    - **Alhálózati címtartomány**: Adja meg *10.0.0.0/24* -alhálózat címtartománya.

    ![Virtuális hálózat létrehozása](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

3. Válassza ki **OK** térjen vissza a **beállítások** lapot.

4. A **előtérbeli IP-konfiguráció**, ellenőrizze **IP-cím típusa** értékre van állítva **nyilvános**. A **nyilvános IP-cím**, ellenőrizze **új létrehozása** van kiválasztva. 

5. Adja meg *myAGPublicIPAddress* számára a nyilvános IP-cím neve. 

6. Az alapértelmezett értékeket a többi beállítás esetében fogadja el, majd **OK**.

### <a name="summary-page"></a>Összefoglaló lap

Tekintse át a beállításokat a **összefoglalás** oldalra, és kattintson **OK** a virtuális hálózat, nyilvános IP-cím és az application gateway létrehozásához. Az application gateway létrehozása több percig is eltarthat. Várjon, amíg az üzembe helyezés sikeresen befejeződik, mielőtt továbblép a következő szakaszban.

## <a name="add-a-subnet"></a>Alhálózat hozzáadása

Adjon hozzá egy alhálózatot a következő lépések során létrehozott virtuális hálózat:

1. Válassza ki **összes erőforrás** adja meg az Azure portal bal oldali menüben *myVNet* a keresési mezőbe, és válassza ki a **myVNet** a keresési eredmények közül.

2. Válassza ki **alhálózatok** a bal oldali menüben, és válassza ki a **+ alhálózat**. 

    ![Alhálózat létrehozása](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Az a **alhálózat hozzáadása** lap, adja meg *myBackendSubnet* számára a **neve** az alhálózatot, és válassza ki a **OK**.

## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a példában két virtuális gépet használó Azure háttérkiszolgálóiként az application gateway számára hoz létre. Az IIS-et is telepíti a virtuális gépek ellenőrzése az Azure application gateway létrehozása sikerült.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Az Azure Portalon, válassza ki a **erőforrás létrehozása**. A **új** ablak jelenik meg.

2. Válassza ki **számítási** majd **Windows Server 2016 Datacenter** a a **kiemelt** listája. A **hozzon létre egy virtuális gépet** lap jelenik meg.

3. Adja meg ezeket az értékeket a **alapjai** lapon találja a következő virtuális gép beállításait:

    - **Erőforráscsoport**: Válassza ki **myResourceGroupAG** az erőforráscsoport neveként.
    - **A virtuális gép neve**: Adja meg *myVM* a virtuális gép neve.
    - **Felhasználónév**: Adja meg *azureuser* az rendszergazdájának felhasználóneve.
    - **Jelszó**: Adja meg *Azure123456!* a rendszergazdai jelszót.

4. Elfogadhatja az alapértelmezett beállításokat, majd **tovább: Lemezek**.  

5. Fogadja el a **lemezek** alapértelmezett lapot, majd **tovább: Hálózatkezelés**.

6. Az a **hálózatkezelés** lapon ellenőrizze, hogy **myVNet** van kiválasztva a **virtuális hálózati** és a **alhálózati** értékre van állítva  **myBackendSubnet**. Elfogadhatja az alapértelmezett beállításokat, majd **tovább: Felügyeleti**.

8. Az a **felügyeleti** lapra, és állítsa **rendszerindítási diagnosztika** való **ki**. Elfogadhatja az alapértelmezett beállításokat, majd **felülvizsgálat + létrehozása**.

9. Az a **tekintse át + létrehozása** lapon tekintse át a beállításokat, javíthatja az esetleges érvényesítési hibákat, és válassza ki **létrehozás**.

10. Várjon, amíg a virtuális gép létrehozásának befejeződését a továbblépés előtt.

### <a name="install-iis"></a>Az IIS telepítése

1. Nyissa meg [az Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Ehhez válassza ki a **Cloud Shell** az Azure Portalon, és ezután válassza a felső navigációs sávon **PowerShell** a legördülő listából. 

    ![Egyéni bővítmény telepítése](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Futtassa a következő parancsot az IIS a virtuális gépen való telepítéséhez: 

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

3. Hozzon létre egy második virtuális gépet, és az IIS telepítése a korábban végrehajtott lépések segítségével. Használat *myVM2* a virtuális gép nevét, valamint a a **VMName** beállításaként a **Set-AzureRmVMExtension** parancsmagot.

### <a name="add-backend-servers"></a>Háttérkiszolgálók hozzáadása

1. Válassza ki **összes erőforrás**, majd válassza ki **myAppGateway**.

2. Válassza ki **háttérkészletek** a bal oldali menüből. Az Azure automatikusan létrejön egy alapértelmezett címkészlet **appGatewayBackendPool**, az application gateway létrehozása során. 

3. Válassza ki **appGatewayBackendPool**.

4. A **célok**válassza **virtuális gép** a legördülő listából.

5. A **virtuális gép** és **hálózati adapterek**, jelölje be a **myVM** és **myVM2** virtuális gépek és a kapcsolódó hálózati a legördülő listákból felületek.

    ![Háttérkiszolgálók hozzáadása](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Kattintson a **Mentés** gombra.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

1. Az application gateway a nyilvános IP-cím keresése a **áttekintése** lapot.

    ![Alkalmazásátjáró nyilvános IP-címének rögzítése](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)

    Másik lehetőségként kiválaszthatja **összes erőforrás**, adja meg *myAGPublicIPAddress* keresési mezőbe, majd kattintson a keresési eredmények között. Az Azure nyilvános IP-címét jeleníti meg a **áttekintése** lapot.

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

    ![Az alkalmazásátjáró tesztelése](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az application gateway segítségével létrehozott erőforrásokat, távolítsa el az erőforráscsoportot. Az erőforráscsoport eltávolításával is eltávolítja az application gateway és az összes kapcsolódó erőforrás. 

Az erőforráscsoport eltávolítása:
1. Az Azure portal bal oldali menüben válassza ki **erőforráscsoportok**.
2. Az a **erőforráscsoportok** lapon, keressen rá a **myResourceGroupAG** a listában, majd válassza ki azt.
3. Az a **erőforráscsoport oldalán**válassza **erőforráscsoport törlése**.
4. Adja meg *myResourceGroupAG* a **írja be az ERŐFORRÁSCSOPORT nevét** majd **törlése**

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes forgalom kezelése alkalmazásátjáróval az Azure CLI használatával](./tutorial-manage-web-traffic-cli.md)
