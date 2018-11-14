---
title: Hozzon létre egy Application Gateway – Azure Portal
description: Megtudhatja, hogyan hozhat létre egy Application Gateway az Azure portal használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2018
ms.author: victorh
ms.openlocfilehash: ba4ab7e488de4a8d3c9fa702c6b33f9ea31b2c2a
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615959"
---
# <a name="create-an-application-gateway-using-the-azure-portal"></a>Hozzon létre egy application gateway az Azure portal használatával

Az Azure portal használatával létrehozhat, vagy az application Gateway átjárók kezelése. Ez a rövid útmutató bemutatja, hogyan hozhat létre hálózati erőforrásokat, a háttérkiszolgálók és a egy application gateway.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [http://portal.azure.com](http://portal.azure.com) webhelyen

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az Ön által létrehozott erőforrások közti kommunikációt egy virtuális hálózaton van szükség. Ebben a példában két alhálózatot hozunk létre: egyet az alkalmazásátjáróhoz, egy másikat pedig a háttérkiszolgálókhoz. Virtuális hálózatot az alkalmazásátjáróval együtt is létrehozhat.

1. Kattintson a **új** az Azure portal bal felső sarkában található.
2. Válassza a **Hálózatkezelés**, majd az **Application Gateway** elemet a Kiemeltek listából.
3. Adja meg a következő értékeket az alkalmazásátjáróhoz:

    - Az alkalmazásátjáró neve *myAppGateway*.
    - Az új erőforráscsoport *myResourceGroupAG*.

    ![Új alkalmazásátjáró létrehozása](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

4. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.
5. Kattintson a **virtuális hálózat választása**, kattintson a **új létrehozása**, majd adja meg ezeket az értékeket a virtuális hálózat:

    - A virtuális hálózat neve *myVNet*.
    - A virtuális hálózat címtere *10.0.0.0/16*.
    - Az alhálózat neve *myAGSubnet*.
    - Az alhálózat címtere *10.0.0.0/24*.

    ![Virtuális hálózat létrehozása](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

6. A virtuális hálózat és az alhálózat létrehozásához kattintson az **OK** gombra.
6. Kattintson a **egy nyilvános IP-cím választása**, kattintson a **új létrehozása**, majd adja meg a nyilvános IP-cím nevére. Ebben a példában a nyilvános IP-cím neve *myAGPublicIPAddress*. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.
8. Fogadja el az alapértelmezett értékeket, a figyelőt a konfigurációhoz, hagyja meg a webalkalmazás-tűzfal le van tiltva, és kattintson **OK**.
9. Az összefoglaló lapon ellenőrizze a beállításokat, majd kattintson az **OK** gombra a virtuális hálózat, a nyilvános IP-cím és az alkalmazásátjáró létrehozásához. Az application gateway hozhatók létre, várjon, amíg az üzembe helyezés sikeresen befejeződik, mielőtt továbblép a következő szakaszban több percig is eltarthat.

### <a name="add-a-subnet"></a>Alhálózat hozzáadása

1. Kattintson a **Minden erőforrás** elemre a bal oldali menüben, majd kattintson a **myVNet** lehetőségre az erőforráslistában.
2. Kattintson a **alhálózatok**, és kattintson a **alhálózati**.

    ![Alhálózat létrehozása](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Adja meg a *myBackendSubnet* nevet az alhálózat neveként, majd kattintson az **OK** gombra.

## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a példában két virtuális gépet, az application gateway háttérkiszolgálóiként használt hoz létre. A virtuális gépeken emellett telepíti az IIS-t annak ellenőrzéséhez, hogy az alkalmazásátjáró sikeresen létrejött-e.

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

1. Nyissa meg az interaktív shell, és győződjön meg arról, hogy van-e állítva **PowerShell**.

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

3. Hozzon létre egy második virtuális gépet, és telepítse az IIS-t az imént befejezett lépésekkel. Adja meg a *myVM2* nevet a virtuális gép neveként és a Set-AzureRmVMExtension parancs VMName paramétereként.

### <a name="add-backend-servers"></a>Háttérkiszolgálók hozzáadása

3. Kattintson a **összes erőforrás**, és kattintson a **myAppGateway**.
4. Kattintson a **Háttérkészletek** lehetőségre. Az alapértelmezett készlet automatikusan létrejött az alkalmazásátjáróval együtt. Kattintson az **appGatewayBackendPool** lehetőségre.
5. Kattintson a **Hozzáadás cél** minden Ön által létrehozott virtuális gép hozzáadásához a háttérkészlethez.

    ![Háttérkiszolgálók hozzáadása](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Kattintson a **Save** (Mentés) gombra.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

1. Keresse meg az alkalmazásátjáró nyilvános IP-címét az Áttekintés képernyőn. Kattintson a **összes erőforrás** majd **myAGPublicIPAddress**.

    ![Alkalmazásátjáró nyilvános IP-címének rögzítése](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

    ![Az alkalmazásátjáró tesztelése](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, az application gateway és minden kapcsolódó erőforrás. Ehhez válassza ki az alkalmazásátjárót tartalmazó erőforráscsoportot, és kattintson a **Törlés** elemre.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy erőforráscsoportot, hálózati erőforrásokat és háttérkiszolgálókat hozott létre. Egy application gateway létrehozásához használt erőforrások majd. Az application Gateway átjárók és a kapcsolódó erőforrásokkal kapcsolatos további információkért folytassa az útmutató cikkekre.
