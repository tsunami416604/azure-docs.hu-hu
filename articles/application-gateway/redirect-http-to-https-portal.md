---
title: Application gateway létrehozása a HTTP – HTTPS átirányításról, az Azure portal használatával
description: Ismerje meg, hogyan hozhat létre egy application gateway átirányított érkező HTTP – HTTPS az Azure portal használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 12/7/2018
ms.author: victorh
ms.openlocfilehash: 1d30ddfb97b065d0d2fdf3bf91a73d3f7eb1b70f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53110068"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Application gateway létrehozása a HTTP – HTTPS átirányításról, az Azure portal használatával

Az Azure portal segítségével hozzon létre egy [az application gateway](overview.md) az SSL-lezárást tanúsítvánnyal. Átirányítja a HTTPS-portot az application gateway HTTP-forgalom-útválasztási szabály szolgál. Ebben a példában is létrehozhat egy [virtuálisgép-méretezési csoport](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) az application Gateway két virtuálisgép-példányokat tartalmazó háttérkészlete számára.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Önaláírt tanúsítvány létrehozása
> * Hálózat beállítása
> * Alkalmazásátjáró létrehozása a tanúsítvánnyal
> * Egy figyelő és átirányítási szabály hozzáadása
> * Virtuálisgép-méretezési csoport létrehozása az alapértelmezett háttérkészlettel

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Az oktatóanyag elvégzéséhez hozzon létre egy tanúsítványt, és az IIS telepítése az Azure PowerShell modul 3.6-os vagy újabb verziójára. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ebben az oktatóanyagban a parancsok futtatásához is futtatni szeretné `Login-AzureRmAccount` kapcsolat létrehozása az Azure-ral.

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Éles környezetben importálnia kell egy megbízható szolgáltató által aláírt érvényes tanúsítványt. Ebben az oktatóanyagban egy önaláírt tanúsítványt hoz létre a [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) parancsmaggal. Az [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) parancsmagot a visszaadott ujjlenyomattal futtatva egy PFX-fájlt exportálhat a tanúsítványból.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Ennek nagyjából a következőképpen kell kinéznie:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

A PFX-fájl létrehozása az ujjlenyomattal:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az Ön által létrehozott erőforrások közti kommunikációt egy virtuális hálózaton van szükség. Ebben a példában két alhálózatot hozunk létre: egyet az alkalmazásátjáróhoz, egy másikat pedig a háttérkiszolgálókhoz. Virtuális hálózatot az alkalmazásátjáróval együtt is létrehozhat.

1. Jelentkezzen be az Azure Portalra a [http://portal.azure.com](http://portal.azure.com) webhelyen.
2. Az Azure Portal bal felső sarkában kattintson az **Erőforrás létrehozása** gombra.
3. Válassza a **Hálózatkezelés**, majd az **Application Gateway** elemet a Kiemeltek listából.
4. Adja meg a következő értékeket az alkalmazásátjáróhoz:

    - Az alkalmazásátjáró neve *myAppGateway*.
    - Az új erőforráscsoport *myResourceGroupAG*.

    ![Új alkalmazásátjáró létrehozása](./media/create-url-route-portal/application-gateway-create.png)

5. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.
6. Kattintson a **virtuális hálózat választása**, kattintson a **új létrehozása**, majd adja meg ezeket az értékeket a virtuális hálózat:

    - A virtuális hálózat neve *myVNet*.
    - A virtuális hálózat címtere *10.0.0.0/16*.
    - Az alhálózat neve *myAGSubnet*.
    - *10.0.1.0/24* – az alhálózat címtere számára.

    ![Virtuális hálózat létrehozása](./media/create-url-route-portal/application-gateway-vnet.png)

7. A virtuális hálózat és az alhálózat létrehozásához kattintson az **OK** gombra.
8. A **előtérbeli IP-konfiguráció**, győződjön meg, hogy **IP-cím típusa** van **nyilvános**, és **új létrehozása** van kiválasztva. Adja meg *myAGPublicIPAddress* neve. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.
9. Alatt **figyelőkonfiguráció**, jelölje be **HTTPS**, majd **válasszon ki egy fájlt** , és keresse meg a *c:\appgwcert.pfx* fájl és Válassza ki **nyílt**.
10. Típus *appgwcert* a tanúsítvány neve és *Azure123456!* .
11. Hagyja meg a webalkalmazás-tűzfal le van tiltva, és válassza ki **OK**.
12. Tekintse át a beállításokat az Összegzés lapon, és válassza ki **OK** a hálózati erőforrások és az application gateway létrehozásához. Az application gateway hozhatók létre, várjon, amíg az üzembe helyezés sikeresen befejeződik, mielőtt továbblép a következő szakaszban több percig is eltarthat.

### <a name="add-a-subnet"></a>Alhálózat hozzáadása

1. Válassza ki **összes erőforrás** a bal oldali menüben, és válassza ki a **myVNet** az erőforrások listájában.
2. Válassza ki **alhálózatok**, és kattintson a **alhálózati**.

    ![Alhálózat létrehozása](./media/create-url-route-portal/application-gateway-subnet.png)

3. Típus *myBackendSubnet* az alhálózat neve.
4. Típus *10.0.2.0/24* a címtartományt, és válassza ki a **OK**.

## <a name="add-a-listener-and-redirection-rule"></a>Egy figyelő és átirányítási szabály hozzáadása

### <a name="add-the-listener"></a>A figyelő felvétele

Először adja hozzá a nevű figyelő *myListener* a 80-as porton.

1. Nyissa meg a **myResourceGroupAG** erőforrás-csoport, és válassza **myAppGateway**.
2. Válassza ki **figyelői** majd **+ alapszintű**.
3. Típus *MyListener* neve.
4. Típus *httpPort* új előtérbeli port neve és *80-as* a port.
5. Győződjön meg arról, a protokoll értéke **HTTP**, majd válassza ki **OK**.

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>Átirányítási konfigurációval rendelkező útválasztási szabály hozzáadása

1. A **myAppGateway**válassza **szabályok** majd **+ alapszintű**.
2. Az a **neve**, típus *felhasználóval a Rule2*.
3. Győződjön meg, hogy **MyListener** van kiválasztva a figyelőt.
4. Válassza ki a **átirányítás konfigurálása** jelölőnégyzetet.
5. A **átirányítás típusa**válassza **állandó**.
6. A **átirányítás célhelye**válassza **figyelő**.
7. Győződjön meg, hogy a **célfigyelő** értékre van állítva **appGatewayHttpListener**.
8. Válassza ki a **lekérdezési karakterlánc befoglalása** és **Belefoglalás elérési út** jelölőnégyzeteket.
9. Kattintson az **OK** gombra.

## <a name="create-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport létrehozása

Ebben a példában egy olyan virtuálisgép-méretezési csoportot hoz létre, amely kiszolgálókat biztosít a háttérkészlet számára az alkalmazásátjáróban.

1. A portál bal felső sarokban, válassza ki a **+ erőforrás létrehozása**.
2. Válassza ki **számítási**.
3. A Keresés mezőbe írja be a *méretezési* nyomja le az Enter billentyűt.
4. Válassza ki **virtuálisgép-méretezési csoport**, majd válassza ki **létrehozás**.
5. A **virtuálisgép-méretezési készlet neve**, típus *myvmss*.
6. Operációs rendszer lemezképét, az ** biztosítása **Windows Server 2016 Datacenter** van kiválasztva.
7. A **erőforráscsoport**válassza **myResourceGroupAG**.
8. A **felhasználónév**, típus *azureuser*.
9. A **jelszó**, típus *Azure123456!* és erősítse meg a jelszót.
10. A **példányszám**, ellenőrizze, az **2**.
11. A **példány mérete**válassza **D2s_v3**.
12. Alatt **hálózatkezelés**, győződjön meg, hogy **válassza a terheléselosztási beállítások** értékre van állítva **Application Gateway**.
13. Győződjön meg, hogy **az Application gateway** értékre van állítva **myAppGateway**.
14. Győződjön meg, hogy **alhálózati** értékre van állítva **myBackendSubnet**.
15. Kattintson a **Létrehozás** gombra.

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>A méretezési csoportot a megfelelő háttérkészlet társítása

A virtuális gép méretezési készlet portál felhasználói Felületét hoz létre egy új háttérkészletet a méretezési csoport, de a meglévő appGatewayBackendPool társítani szeretné.

1. Nyissa meg a **myResourceGroupAg** erőforráscsoportot.
2. Válassza ki **myAppGateway**.
3. Válassza ki **háttérkészletek**.
4. Válassza ki **myAppGatewaymyvmss**.
5. Válassza ki **távolítsa el az összes cél a háttérkészlet**.
6. Kattintson a **Mentés** gombra.
7. Ez a folyamat befejezése után jelölje ki a **myAppGatewaymyvmss** háttérkészlet, jelölje be **törlése** , majd **OK** megerősítéséhez.
8. Válassza ki **appGatewayBackendPool**.
9. A **célok**válassza **VMSS**.
10. A **VMSS**válassza **myvmss**.
11. A **hálózatiadapter-konfigurációk**válassza **myvmssNic**.
12. Kattintson a **Mentés** gombra.

### <a name="upgrade-the-scale-set"></a>A méretezési csoport frissítése

Végül frissítenie kell a méretezési csoport ezeket a módosításokat.

1. Válassza ki a **myvmss** méretezési csoportot.
2. A **beállítások**válassza **példányok**.
3. Példány is, majd válassza ki és **frissítése**.
4. Válassza az **Igen** lehetőséget a megerősítéshez.
5. Miután ez befejeződött, lépjen vissza a **myAppGateway** válassza **háttérkészletek**. Most látnia kell, hogy a **appGatewayBackendPool** rendelkezik két célok és **myAppGatewaymyvmss** nulla célok nem.
6. Válassza ki **myAppGatewaymyvmss**, majd válassza ki **törlése**.
7. Válassza ki **OK** megerősítéséhez.

### <a name="install-iis"></a>Az IIS telepítése

IIS telepítése a méretezési egyszerűen az PowerShell-lel. A portálról, a Cloud Shell ikonra, és ellenőrizze, hogy **PowerShell** van kiválasztva.

A PowerShell-ablakban illessze be a következő kódot, és nyomja le az Enter billentyűt.

```azurepowershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzureRmVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

### <a name="upgrade-the-scale-set"></a>A méretezési csoport frissítése

Miután megváltoztatta a példányok IIS-t, ismét frissítenie kell a méretezési csoport ezzel.

1. Válassza ki a **myvmss** méretezési csoportot.
2. A **beállítások**válassza **példányok**.
3. Példány is, majd válassza ki és **frissítése**.
4. Válassza az **Igen** lehetőséget a megerősítéshez.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Az alkalmazás nyilvános IP-cím is kérhet a application gateway – áttekintés oldalra.

1. Válassza ki **myAppGateway**.
2. Az a **áttekintése** lapon, vegye figyelembe az IP-címnek **előtérbeli nyilvános IP-cím**.

3. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Például: http://52.170.203.149

   ![Biztonsági figyelmeztetés](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Ha önaláírt tanúsítványt használt, a biztonsági figyelmeztetés elfogadásához válassza a **Részletek** lehetőséget, majd válassza a **Továbblépés a weblapra** lehetőséget. Ekkor a biztonságos IIS-webhely a következő példához hasonlóan jelenik meg:

   ![Az alap URL-cím tesztelése az alkalmazásátjáróban](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [application gateway létrehozása belső átirányítás](redirect-internal-site-powershell.md).