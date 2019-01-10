---
title: Egy application gateway konfigurálása SSL-lezárást – Azure portal |} A Microsoft Docs
description: Ismerje meg, hogyan kell application gateway konfigurálása és a egy tanúsítványt az SSL-lezárást az Azure portal használatával.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.date: 5/15/2018
ms.author: victorh
ms.openlocfilehash: 2ae8c14b40fa13a1aa8008588fb0efb1b1d2c3f6
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159417"
---
# <a name="configure-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>Egy application gateway konfigurálása SSL-lezárást az Azure portal használatával

Az Azure portal segítségével konfigurálhatja egy [az application gateway](overview.md) az SSL-lezárást használó virtuális gépek háttérkiszolgálókhoz tanúsítvánnyal.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Önaláírt tanúsítvány létrehozása
> * Alkalmazásátjáró létrehozása a tanúsítvánnyal
> * A háttér-kiszolgálóként használt virtuális gépek létrehozása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) címen.

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Ebben a szakaszban használhatja [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) hozhat létre, amely feltölti az Azure Portalon a figyelő az application gateway számára létrehozott önaláírt tanúsítványt.

A helyi számítógépen nyisson meg egy Windows PowerShell-ablakot rendszergazdaként. Futtassa a következő parancsot a tanúsítvány létrehozásához:

```powershell
New-SelfSignedCertificate \
  -certstorelocation cert:\localmachine\my \
  -dnsname www.contoso.com
```

Ez a válasz hasonlót kell megjelennie:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com

Use [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) with the Thumbprint that was returned to export a pfx file from the certificate:
```

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate \
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 \
  -FilePath c:\appgwcert.pfx \
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az Ön által létrehozott erőforrások közti kommunikációt egy virtuális hálózaton van szükség. Ebben a példában két alhálózatot hozunk létre: egyet az alkalmazásátjáróhoz, egy másikat pedig a háttérkiszolgálókhoz. Virtuális hálózatot az alkalmazásátjáróval együtt is létrehozhat.

1. Kattintson a **új** az Azure portal bal felső sarkában található.
2. Válassza a **Hálózatkezelés**, majd az **Application Gateway** elemet a Kiemeltek listából.
3. Adja meg *myAppGateway* az application gateway neve és *myResourceGroupAG* az új erőforráscsoportnak.
4. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.
5. Kattintson a **virtuális hálózat választása**, kattintson a **új létrehozása**, majd adja meg ezeket az értékeket a virtuális hálózat:

    - A virtuális hálózat neve *myVNet*.
    - A virtuális hálózat címtere *10.0.0.0/16*.
    - Az alhálózat neve *myAGSubnet*.
    - Az alhálózat címtere *10.0.0.0/24*.

    ![Virtuális hálózat létrehozása](./media/create-ssl-portal/application-gateway-vnet.png)

6. A virtuális hálózat és az alhálózat létrehozásához kattintson az **OK** gombra.
7. Kattintson a **egy nyilvános IP-cím választása**, kattintson a **új létrehozása**, majd adja meg a nyilvános IP-cím nevére. Ebben a példában a nyilvános IP-cím neve *myAGPublicIPAddress*. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.
8. Kattintson a **HTTPS** a protokoll, a figyelőt, és győződjön meg arról, hogy a port számít, ha **443-as**.
9. A mappa ikonra, és keresse meg a *appgwcert.pfx* tanúsítványt, amelyet korábban létrehozott töltse fel.
10. Adja meg *mycert1* a tanúsítvány neve és *Azure123456!* a jelszót, és kattintson a **OK**.

    ![Új alkalmazásátjáró létrehozása](./media/create-ssl-portal/application-gateway-create.png)

11. Tekintse át a beállításokat az Összegzés lapon, és kattintson **OK** a hálózati erőforrások és az application gateway létrehozásához. Az application gateway hozhatók létre, várjon, amíg az üzembe helyezés sikeresen befejeződik, mielőtt továbblép a következő szakaszban több percig is eltarthat.

### <a name="add-a-subnet"></a>Alhálózat hozzáadása

1. Kattintson a **Minden erőforrás** elemre a bal oldali menüben, majd kattintson a **myVNet** lehetőségre az erőforráslistában.
2. Kattintson a **alhálózatok**, és kattintson a **alhálózati**.

    ![Alhálózat létrehozása](./media/create-ssl-portal/application-gateway-subnet.png)

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

    ![Egyéni bővítmény telepítése](./media/create-ssl-portal/application-gateway-extension.png)

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

    ![Háttérkiszolgálók hozzáadása](./media/create-ssl-portal/application-gateway-backend.png)

6. Kattintson a **Save** (Mentés) gombra.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

1. Kattintson a **összes erőforrás**, és kattintson a **myAGPublicIPAddress**.

    ![Alkalmazásátjáró nyilvános IP-címének rögzítése](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Fogadja el a biztonsági figyelmeztetést, ha önaláírt tanúsítványt használt, válassza ki a részleteket, és keresse a képernyőn látható weblapon:

    ![Biztonsági figyelmeztetés](./media/create-ssl-portal/application-gateway-secure.png)

    Ekkor a biztonságos IIS-webhely a következő példához hasonlóan jelenik meg:

    ![Az alap URL-cím tesztelése az alkalmazásátjáróban](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Önaláírt tanúsítvány létrehozása
> * Alkalmazásátjáró létrehozása a tanúsítvánnyal
> * A háttér-kiszolgálóként használt virtuális gépek létrehozása

Az application Gateway átjárók és a kapcsolódó erőforrásokkal kapcsolatos további információkért folytassa az útmutató cikkekre.