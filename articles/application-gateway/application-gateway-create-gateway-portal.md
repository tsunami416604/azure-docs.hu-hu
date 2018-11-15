---
title: Hozzon létre egy Application Gateway – Azure Portal
description: Megtudhatja, hogyan hozhat létre egy Application Gateway az Azure portal használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2018
ms.author: victorh
ms.openlocfilehash: d8ab49bc988060533bc5ff65d414dcba6245be48
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51631909"
---
# <a name="create-an-application-gateway-using-the-azure-portal"></a>Hozzon létre egy application gateway az Azure portal használatával

Az Azure portal használatával létrehozhat, vagy az application Gateway átjárók kezelése. Ez a cikk bemutatja, hogyan hozhat létre hálózati erőforrásokat, a háttérkiszolgálók és a egy application gateway.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [http://portal.azure.com](http://portal.azure.com) webhelyen

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az Ön által létrehozott erőforrások közti kommunikációt egy virtuális hálózaton van szükség. Ebben a példában két alhálózatot hozunk létre: egyet az alkalmazásátjáróhoz, egy másikat pedig a háttérkiszolgálókhoz. Virtuális hálózatot az alkalmazásátjáróval együtt is létrehozhat.

1. Az Azure Portal bal felső sarkában kattintson az **Erőforrás létrehozása** gombra.
2. Kattintson a **hálózatkezelés** majd **Application Gateway** a kiemelt lista.

### <a name="basics"></a>Alapvető beállítások

1. Adja meg a következő értékeket az alkalmazásátjáróhoz:

    - Az alkalmazásátjáró neve *myAppGateway*.
    - Az új erőforráscsoport *myResourceGroupAG*.

    ![Új alkalmazásátjáró létrehozása](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

2. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.

### <a name="settings"></a>Beállítások

1. Kattintson a **virtuális hálózat választása**, kattintson a **új létrehozása**, majd adja meg ezeket az értékeket a virtuális hálózat:

    - A virtuális hálózat neve *myVNet*.
    - A virtuális hálózat címtere *10.0.0.0/16*.
    - Az alhálózat neve *myAGSubnet*.
    - *10.0.0.0/24* – az alhálózati címtartományt.

    ![Virtuális hálózat létrehozása](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

6. Kattintson a **OK** a beállítások lapra való visszatéréshez.
7. Alatt **előtérbeli IP-konfiguráció** biztosítása **IP-cím típusa** értékre van állítva **nyilvános**, majd a **nyilvános IP-cím**, biztosítása**Új létrehozása** van kiválasztva. Típus *myAGPublicIPAddress* számára a nyilvános IP-cím neve. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.

### <a name="summary"></a>Összegzés

Az összefoglaló lapon ellenőrizze a beállításokat, majd kattintson az **OK** gombra a virtuális hálózat, a nyilvános IP-cím és az alkalmazásátjáró létrehozásához. Az alkalmazásátjáró létrehozása néhány percig is eltarthat. Várjon, amíg az üzembe helyezés sikeresen befejeződik, mielőtt továbblép a következő szakaszban.

## <a name="add-a-subnet"></a>Alhálózat hozzáadása

1. Kattintson a **Minden erőforrás** elemre a bal oldali menüben, majd kattintson a **myVNet** lehetőségre az erőforráslistában.
2. Kattintson a **alhálózatok**, és kattintson a **+ alhálózat**.

    ![Alhálózat létrehozása](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Adja meg a *myBackendSubnet* nevet az alhálózat neveként, majd kattintson az **OK** gombra.

## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a példában két virtuális gépet, az application gateway háttérkiszolgálóiként használt hoz létre. A virtuális gépeken emellett telepíti az IIS-t annak ellenőrzéséhez, hogy az alkalmazásátjáró sikeresen létrejött-e.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Az Azure Portalon kattintson az **Erőforrás létrehozása** gombra.
2. Kattintson a **számítási** majd **Windows Server 2016 Datacenter** a kiemelt lista.
3. Adja meg a következő értékeket a virtuális gép számára:

    - *myResourceGroupAG* az erőforráscsoportnak.
    - A virtuális gép neve *myVM*.
    - A rendszergazda felhasználóneve: *azureuser*.
    - A jelszó *Azure123456!* .

   Elfogadhatja az alapértelmezett beállításokat, és kattintson a **tovább: lemezek**.
4. Fogadja el a lemez alapértelmezett beállításokat, és kattintson a **tovább: hálózatkezelés**.
5. Győződjön meg róla, hogy virtuális hálózatként a **myVNet**, alhálózatként pedig a **myBackendSubnet** van kiválasztva.
6. Elfogadhatja az alapértelmezett beállításokat, és kattintson a **tovább: felügyeleti**.
7. Kattintson a **ki** a rendszerindítási diagnosztika letiltásához. Elfogadhatja az alapértelmezett beállításokat, és kattintson a **felülvizsgálat + létrehozása**.
8. Tekintse át a beállításokat az Összegzés lapon, és kattintson **létrehozás**.
9. Várjon, amíg a virtuális gép létrehozásának befejeződését a továbblépés előtt.

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

1. Kattintson a **összes erőforrás**, és kattintson a **myAppGateway**.
4. Kattintson a **Háttérkészletek** lehetőségre. Az alapértelmezett készlet automatikusan létrejött az alkalmazásátjáróval együtt. Kattintson az **appGatewayBackendPool** lehetőségre.
5. Alatt **célok**, kattintson a **IP-cím vagy FQDN** kiválasztása **virtuális gép**.
6. A **virtuális gép**, adja hozzá a virtuális gépek myVM és myVM2 és a társított hálózati adapterek.

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

Ebben a cikkben létrehozott egy erőforráscsoportot, hálózati erőforrásokat és háttérkiszolgálókat. Egy application gateway létrehozásához használt erőforrások majd. Az application Gateway átjárók és a kapcsolódó erőforrásokkal kapcsolatos további információkért lásd: [Mi az Azure Application Gateway?](overview.md)
