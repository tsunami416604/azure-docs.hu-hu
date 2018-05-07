---
title: Alkalmazásátjáró - létrehozása az Azure portálon |} Microsoft Docs
description: Megtudhatja, hogyan hozhat létre olyan átjárót az Azure-portál használatával.
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: victorh
ms.openlocfilehash: 0df71c445d2c5fc6827b69f708203a3b3e6e2b53
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-using-the-azure-portal"></a>Az Azure portál használatával Alkalmazásátjáró létrehozása

Az Azure portál segítségével létrehozása vagy kezelése alkalmazásátjárót. A gyors üzembe helyezés bemutatja, hogyan hozzon létre a hálózati erőforrásokhoz, a háttérkiszolgálók, illetve az Alkalmazásátjáró.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-portálon: [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Egy virtuális hálózatot az Ön által létrehozott erőforrások közötti kommunikációra van szükség. Két alhálózat ebben a példában jönnek létre: egyet az Alkalmazásátjáró, míg a másik a háttérkiszolgálókhoz. Az Alkalmazásátjáró létrehozott egyszerre egy virtuális hálózatot is létrehozhat.

1. Kattintson a **új** az Azure portál bal felső sarkában található.
2. Válassza ki **hálózati** majd **Application Gateway** kiemelt listájában.
3. Adja meg ezeket az értékeket az Alkalmazásátjáró:

    - *myAppGateway* – az Alkalmazásátjáró nevét.
    - *myResourceGroupAG* – az új erőforráscsoport.

    ![Új Alkalmazásátjáró létrehozása](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

4. Fogadja el a további beállításoknál az alapértelmezett értékeket, és kattintson a **OK**.
5. Kattintson a **virtuális hálózatot választ**, kattintson a **hozzon létre új**, és ezekkel az értékekkel adja meg a virtuális hálózat:

    - *myVNet* – a virtuális hálózat nevét.
    - *10.0.0.0/16* – a virtuális hálózat címtere.
    - *myAGSubnet* – az alhálózati név.
    - *10.0.0.0/24* – az alhálózati címtartományt.

    ![Virtuális hálózat létrehozása](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

6. Kattintson a **OK** a virtuális hálózati és alhálózati létrehozásához.
6. Kattintson a **egy nyilvános IP-cím kiválasztása**, kattintson a **hozzon létre új**, és írja be a nyilvános IP-cím neve. Ebben a példában a nyilvános IP-cím neve *myAGPublicIPAddress*. Fogadja el a további beállításoknál az alapértelmezett értékeket, és kattintson a **OK**.
8. Fogadja el az alapértelmezett értékeket, a figyelő a konfigurációhoz, hagyja a webalkalmazási tűzfal le van tiltva, és kattintson **OK**.
9. Tekintse át a beállításokat az Összegzés lapon, és kattintson **OK** a virtuális hálózat, a nyilvános IP-cím és az alkalmazás átjáró létrehozásához. Az alkalmazás-átjáró hozható létre, várjon, amíg a telepítés sikeresen befejeződik, mielőtt továbblép a következő szakaszban több percig is eltarthat.

### <a name="add-a-subnet"></a>Adjon hozzá egy alhálózatot

1. Kattintson a **összes erőforrás** a bal oldali menüből, majd **myVNet** erőforrások listából.
2. Kattintson a **alhálózatok**, és kattintson a **alhálózati**.

    ![Alhálózat létrehozása](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Adja meg *myBackendSubnet* neveként az alhálózati majd **OK**.

## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a példában két virtuális gép az Alkalmazásátjáró háttér-kiszolgálóként használandó hoz létre. Is telepíteni az IIS ellenőrizze, hogy az Alkalmazásátjáró sikeresen létrejött-e a virtuális gépeken.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Kattintson az **Új** lehetőségre.
2. Kattintson a **számítási** majd **Windows Server 2016 Datacenter** kiemelt listájában.
3. Adja meg a virtuális gép ezeket az értékeket:

    - *myVM* – a virtuális gép nevét.
    - A rendszergazda felhasználóneve: *azureuser*.
    - *Azure123456!* a jelszó.
    - Válassza ki **meglévő**, majd válassza ki *myResourceGroupAG*.

4. Kattintson az **OK** gombra.
5. A virtuális gép méreténél válassza a **DS1_V2** lehetőséget, majd kattintson a **Kiválasztás** gombra.
6. Győződjön meg arról, hogy **myVNet** van kiválasztva a virtuális hálózat és az alhálózat van **myBackendSubnet**. 
7. A rendszerindítási diagnosztika letiltásához kattintson a **Letiltva** elemre.
8. Kattintson az **OK** gombra, majd az összefoglaló lapon ellenőrizze a beállításokat, és kattintson a **Létrehozás** gombra.

### <a name="install-iis"></a>Az IIS telepítése

1. Az interaktív rendszerhéjat, és győződjön meg arról, hogy van-e állítva **PowerShell**.

    ![Egyéni kiterjesztés telepítése](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

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

3. Kattintson a **összes erőforrás**, és kattintson a **myAppGateway**.
4. Kattintson a **háttérkészletek**. Alapértelmezett címkészlet automatikusan jött létre az Alkalmazásátjáró. Kattintson a **appGatewayBackendPool**.
5. Kattintson a **Hozzáadás cél** minden létrehozott virtuális gép hozzáadása a háttérkészlet.

    ![Adja hozzá a háttérkiszolgálókon](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Kattintson a **Save** (Mentés) gombra.

## <a name="test-the-application-gateway"></a>Az Alkalmazásátjáró tesztelése

1. A nyilvános IP-cím keresése a Áttekintés képernyő az Alkalmazásátjáró. Kattintson a **összes erőforrás** majd **myAGPublicIPAddress**.

    ![Rekord alkalmazás átjáró nyilvános IP-címe](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

    ![Alkalmazásátjáró tesztelése](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, az erőforráscsoport, Alkalmazásátjáró és minden kapcsolódó erőforrás törléséhez. Ehhez az szükséges, válassza ki, amely tartalmazza a Alkalmazásátjáró, majd kattintson az erőforráscsoport **törlése**.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy erőforráscsoportot, hálózati erőforrásokat és háttérkiszolgálókat hozott létre. Alkalmazásátjáró létrehozásához használt erőforrások majd. További információt a alkalmazásátjárót és a kapcsolódó erőforrások, továbbra is a útmutatókat.
