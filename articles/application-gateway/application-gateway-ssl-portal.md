---
title: Hozzon létre egy alkalmazás SSL-lezárást - Azure-portál |} Microsoft Docs
description: Megtudhatja, hogyan Alkalmazásátjáró létrehozása és hozzáadása egy tanúsítványt az SSL-lezárást az Azure portál használatával.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: 10796000f913428e39a0ffbd0aa2cbe0c515eb7a
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>Hozzon létre egy alkalmazást az Azure portál használatával SSL-lezárást

Az Azure-portálon hozhat létre egy [Alkalmazásátjáró](application-gateway-introduction.md) egy SSL-lezárást használ a virtuális gépek háttérkiszolgálók tanúsítványával.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Önaláírt tanúsítvány létrehozása
> * Hozzon létre egy alkalmazást a tanúsítvány
> * A háttérkiszolgálók használt virtuális gépek létrehozása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-portálon: [http://portal.azure.com](http://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Ebben a szakaszban használhatja [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) az Azure-portálon történő létrehozásakor a figyelő az alkalmazás átjáró feltöltött önaláírt tanúsítvány létrehozása.

A helyi számítógépen nyisson meg egy Windows PowerShell ablakot rendszergazdaként. A következő parancsot a tanúsítvány létrehozásához:

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

Egy virtuális hálózatot az Ön által létrehozott erőforrások közötti kommunikációra van szükség. Két alhálózat ebben a példában jönnek létre: egyet az Alkalmazásátjáró, míg a másik a háttérkiszolgálókhoz. Az Alkalmazásátjáró létrehozott egyszerre egy virtuális hálózatot is létrehozhat.

1. Kattintson a **új** az Azure portál bal felső sarkában található.
2. Válassza ki **hálózati** majd **Application Gateway** kiemelt listájában.
3. Adja meg *myAppGateway* az Alkalmazásátjáró nevét és *myResourceGroupAG* az új erőforráscsoport.
4. Fogadja el a további beállításoknál az alapértelmezett értékeket, és kattintson a **OK**.
5. Kattintson a **virtuális hálózatot választ**, kattintson a **hozzon létre új**, és ezekkel az értékekkel adja meg a virtuális hálózat:

    - *myVNet* – a virtuális hálózat nevét.
    - *10.0.0.0/16* – a virtuális hálózat címtere.
    - *myAGSubnet* – az alhálózati név.
    - *10.0.0.0/24* – az alhálózati címtartományt.

    ![Virtuális hálózat létrehozása](./media/application-gateway-ssl-portal/application-gateway-vnet.png)

6. Kattintson a **OK** a virtuális hálózati és alhálózati létrehozásához.
7. Kattintson a **egy nyilvános IP-cím kiválasztása**, kattintson a **hozzon létre új**, és írja be a nyilvános IP-cím neve. Ebben a példában a nyilvános IP-cím neve *myAGPublicIPAddress*. Fogadja el a további beállításoknál az alapértelmezett értékeket, és kattintson a **OK**.
8. Kattintson a **HTTPS** a figyelő a protokollhoz, és győződjön meg arról, hogy a port típusúként van definiálva **443-as**.
9. A mappa ikonra, és keresse meg a *appgwcert.pfx* töltse fel az előzőleg létrehozott tanúsítványt.
10. Adja meg *mycert1* a tanúsítvány nevét és *Azure123456!* a jelszó, és kattintson a **OK**.

    ![Új Alkalmazásátjáró létrehozása](./media/application-gateway-ssl-portal/application-gateway-create.png)

11. Tekintse át a beállításokat az Összegzés lapon, és kattintson **OK** a hálózati erőforrások és az Alkalmazásátjáró létrehozása. Az alkalmazás-átjáró hozható létre, várjon, amíg a telepítés sikeresen befejeződik, mielőtt továbblép a következő szakaszban több percig is eltarthat.

### <a name="add-a-subnet"></a>Adjon hozzá egy alhálózatot

1. Kattintson a **összes erőforrás** a bal oldali menüből, majd **myVNet** erőforrások listából.
2. Kattintson a **alhálózatok**, és kattintson a **alhálózati**.

    ![Alhálózat létrehozása](./media/application-gateway-ssl-portal/application-gateway-subnet.png)

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

    ![Egyéni kiterjesztés telepítése](./media/application-gateway-ssl-portal/application-gateway-extension.png)

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
4. Kattintson a **háttérkészletek**. Alapértelmezett címkészlet automatikusan jött létre az Alkalmazásátjáró. Kattintson a **appGateayBackendPool**.
5. Kattintson a **Hozzáadás cél** minden létrehozott virtuális gép hozzáadása a háttérkészlet.

    ![Adja hozzá a háttérkiszolgálókon](./media/application-gateway-ssl-portal/application-gateway-backend.png)

6. Kattintson a **Save** (Mentés) gombra.

## <a name="test-the-application-gateway"></a>Az Alkalmazásátjáró tesztelése

1. Kattintson a **összes erőforrás**, és kattintson a **myAGPublicIPAddress**.

    ![Rekord alkalmazás átjáró nyilvános IP-címe](./media/application-gateway-ssl-portal/application-gateway-ag-address.png)

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Fogadja el a biztonsági figyelmeztetést, ha önaláírt tanúsítványt használt jelölje ki a részleteket, majd nyissa meg a képernyőn látható weblapon:

    ![Biztonságos figyelmeztetés](./media/application-gateway-ssl-portal/application-gateway-secure.png)

    Ekkor a biztonságos IIS-webhely a következő példához hasonlóan jelenik meg:

    ![Az alkalmazás átjáró alap URL-cím tesztelése](./media/application-gateway-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Önaláírt tanúsítvány létrehozása
> * Hozzon létre egy alkalmazást a tanúsítvány
> * A háttérkiszolgálók használt virtuális gépek létrehozása

További információt a alkalmazásátjárót és a kapcsolódó erőforrások, továbbra is a útmutatókat.