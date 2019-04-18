---
title: Oktatóanyag – az application gateway konfigurálása SSL-lezárást – Azure portal
description: Ebben az oktatóanyagban megismerheti, hogyan application gateway konfigurálása és a egy tanúsítványt az SSL-lezárást az Azure portal használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 4/17/2019
ms.author: victorh
ms.openlocfilehash: f3ba3eb12dc85a72c4e49c374e62209b83400d33
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59677850"
---
# <a name="tutorial-configure-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>Oktatóanyag: Egy application gateway konfigurálása SSL-lezárást az Azure portal használatával

Az Azure portal segítségével konfigurálhatja egy [az application gateway](overview.md) az SSL-lezárást használó virtuális gépek háttérkiszolgálókhoz tanúsítvánnyal.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Önaláírt tanúsítvány létrehozása
> * Alkalmazásátjáró létrehozása a tanúsítvánnyal
> * A háttér-kiszolgálóként használt virtuális gépek létrehozása
> * Az alkalmazásátjáró tesztelése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Ebben a szakaszban használhatja [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) önaláírt tanúsítvány létrehozása. A tanúsítvány feltöltése az Azure Portalra a figyelő az application Gateway létrehozásakor.

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

1. Válassza ki **új** az Azure portal bal felső sarkában található.
2. Válassza a **Hálózatkezelés**, majd az **Application Gateway** elemet a Kiemeltek listából.
3. Adja meg *myAppGateway* az application gateway neve és *myResourceGroupAG* az új erőforráscsoportnak.
4. Az alapértelmezett értékeket a többi beállítás esetében fogadja el, majd **OK**.
5. Válassza ki **virtuális hálózat választása**válassza **új létrehozása**, majd adja meg ezeket az értékeket a virtuális hálózat:

   - A virtuális hálózat neve *myVNet*.
   - A virtuális hálózat címtere *10.0.0.0/16*.
   - Az alhálózat neve *myAGSubnet*.
   - Az alhálózat címtere *10.0.0.0/24*.

     ![Virtuális hálózat létrehozása](./media/create-ssl-portal/application-gateway-vnet.png)

6. Válassza ki **OK** hozhat létre a virtuális hálózatot és alhálózatot.
7. Válassza ki **egy nyilvános IP-cím választása**, jelölje be **új létrehozása**, majd adja meg a nyilvános IP-cím nevére. Ebben a példában a nyilvános IP-cím neve *myAGPublicIPAddress*. Az alapértelmezett értékeket a többi beállítás esetében fogadja el, majd **OK**.
8. Válassza ki **HTTPS** a protokoll, a figyelőt, és győződjön meg arról, hogy a port számít, ha **443-as**.
9. Válassza ki a mappa ikont, és keresse meg a *appgwcert.pfx* tanúsítványt, amelyet korábban létrehozott töltse fel.
10. Adja meg *mycert1* a tanúsítvány neve és *Azure123456!* a jelszót, és válassza ki a **OK**.

    ![Új alkalmazásátjáró létrehozása](./media/create-ssl-portal/application-gateway-create.png)

11. Tekintse át a beállításokat az Összegzés lapon, és válassza ki **OK** a hálózati erőforrások és az application gateway létrehozásához. Az application gateway hozhatók létre, várjon, amíg az üzembe helyezés sikeresen befejeződik, mielőtt továbblép a következő szakaszban több percig is eltarthat.

### <a name="add-a-subnet"></a>Alhálózat hozzáadása

1. Válassza ki **összes erőforrás** a bal oldali menüben, és válassza ki a **myVNet** az erőforrások listájában.
2. Válassza ki **alhálózatok**, majd válassza ki **alhálózati**.

    ![Alhálózat létrehozása](./media/create-ssl-portal/application-gateway-subnet.png)

3. Adja meg *myBackendSubnet* nevét az alhálózat és válassza ki a **OK**.

## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a példában az application gateway háttérkiszolgálóiként használt két virtuális gépet hoz létre. Az IIS-et is telepíti az application gateway sikeres létrehozásának ellenőrzéséhez a virtuális gépeket.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Válassza az **Új** lehetőséget.
2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet a Kiemeltek listából.
3. Adja meg a következő értékeket a virtuális gép számára:

    - A virtuális gép neve *myVM*.
    - A rendszergazda felhasználóneve: *azureuser*.
    - A jelszó *Azure123456!* .
    - Válassza a **Meglévő használata**, majd a *myResourceGroupAG* lehetőséget.

4. Kattintson az **OK** gombra.
5. Válassza ki **DS1_V2** méretét, a virtuális gépet, és válassza **kiválasztása**.
6. Győződjön meg róla, hogy virtuális hálózatként a **myVNet**, alhálózatként pedig a **myBackendSubnet** van kiválasztva. 
7. A rendszerindítási diagnosztika letiltásához kattintson a **Letiltva** elemre.
8. Kattintson az **OK** gombra, majd az összefoglaló lapon ellenőrizze a beállításokat, és kattintson a **Létrehozás** gombra.

### <a name="install-iis"></a>Az IIS telepítése

1. Nyissa meg az interaktív shell, és győződjön meg arról, hogy van-e állítva **PowerShell**.

    ![Egyéni bővítmény telepítése](./media/create-ssl-portal/application-gateway-extension.png)

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

1. Válassza ki **összes erőforrás**, majd válassza ki **myAppGateway**.
1. Válassza ki **háttérkészletek**. Az alapértelmezett készlet automatikusan létrejött az alkalmazásátjáróval együtt. Válassza ki **appGatewayBackendPool**.
1. Válassza ki **Hozzáadás cél** minden Ön által létrehozott virtuális gép hozzáadásához a háttérkészlethez.

    ![Háttérkiszolgálók hozzáadása](./media/create-ssl-portal/application-gateway-backend.png)

1. Kattintson a **Mentés** gombra.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

1. Válassza ki **összes erőforrás**, majd válassza ki **myAGPublicIPAddress**.

    ![Alkalmazásátjáró nyilvános IP-címének rögzítése](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Fogadja el a biztonsági figyelmeztetést, ha önaláírt tanúsítványt használt, válassza ki a részleteket, és keresse a képernyőn látható weblapon:

    ![Biztonsági figyelmeztetés](./media/create-ssl-portal/application-gateway-secure.png)

    Ekkor a biztonságos IIS-webhely a következő példához hasonlóan jelenik meg:

    ![Az alap URL-cím tesztelése az alkalmazásátjáróban](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók a mi mindent az Azure Application Gateway segítségével](application-gateway-introduction.md)
