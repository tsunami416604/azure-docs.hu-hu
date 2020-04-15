---
title: HTTP-ről HTTPS-re történő átirányítás a portálon – Azure Application Gateway
description: Megtudhatja, hogyan hozhat létre egy alkalmazásátjáró átirányított forgalmat HTTP-https az Azure Portalon keresztül.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: cd33d23a506bd86b9651af3d4c3bbca01673a7a4
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312093"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Alkalmazásátjáró létrehozása HTTP-HTTPS átirányítással az Azure Portal használatával

Az Azure Portal segítségével létrehozhat egy [alkalmazásátjárót](overview.md) a TLS-megszüntetési tanúsítvánnyal. Az útválasztási szabály segítségével átirányítják a HTTP-forgalmat az alkalmazásátjáró HTTPS-portjára. Ebben a példában egy [virtuálisgép-méretezési csoportot](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) is létrehozhat az alkalmazásátjáró háttérkészletéhez, amely két virtuálisgép-példányt tartalmaz.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Önaláírt tanúsítvány létrehozása
> * Hálózat beállítása
> * Alkalmazásátjáró létrehozása a tanúsítvánnyal
> * Figyelő és átirányítási szabály hozzáadása
> * Virtuálisgép-méretezési csoport létrehozása az alapértelmezett háttérkészlettel

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez az oktatóanyag az Azure PowerShell-modul 1.0.0-s vagy újabb verzióját igényli egy tanúsítvány létrehozásához és az IIS telepítéséhez. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Az oktatóanyag parancsainak futtatásához is `Login-AzAccount` futtatnia kell az Azure-ral való kapcsolat létrehozásához.

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Éles használatra egy megbízható szolgáltató által aláírt érvényes tanúsítványt kell importálnia. Ebben az oktatóanyagban egy önaláírt tanúsítványt hoz létre a [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) parancsmaggal. Az [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) parancsmagot a visszaadott ujjlenyomattal futtatva egy PFX-fájlt exportálhat a tanúsítványból.

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

A létrehozott erőforrások közötti kommunikációhoz virtuális hálózatra van szükség. Ebben a példában két alhálózatot hozunk létre: egyet az alkalmazásátjáróhoz, egy másikat pedig a háttérkiszolgálókhoz. Virtuális hálózatot az alkalmazásátjáróval együtt is létrehozhat.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Az Azure Portal bal felső sarkában kattintson az **Erőforrás létrehozása** gombra.
3. Válassza a **Hálózatkezelés**, majd az **Application Gateway** elemet a Kiemeltek listából.
4. Adja meg a következő értékeket az alkalmazásátjáróhoz:

   - Az alkalmazásátjáró neve *myAppGateway*.
   - Az új erőforráscsoport *myResourceGroupAG*.

     ![Új alkalmazásátjáró létrehozása](./media/create-url-route-portal/application-gateway-create.png)

5. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.
6. Kattintson **a Virtuális hálózat kiválasztása**gombra, kattintson az Új **létrehozása**gombra, majd adja meg ezeket az értékeket a virtuális hálózathoz:

   - A virtuális hálózat neve *myVNet*.
   - A virtuális hálózat címtere *10.0.0.0/16*.
   - Az alhálózat neve *myAGSubnet*.
   - Az alhálózat címtere *10.0.0.0/24*.

     ![Virtuális hálózat létrehozása](./media/create-url-route-portal/application-gateway-vnet.png)

7. A virtuális hálózat és az alhálózat létrehozásához kattintson az **OK** gombra.
8. Az **Előtér IP-konfigurációja**csoportban győződjön meg arról, hogy az **IP-cím típusa** **Nyilvános,** és az **Új létrehozása** lehetőség van kiválasztva. Adja meg a nevet a *myAGPublicIPAddress címcím* megadásához. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.
9. A **Figyelő konfigurációja**csoportban válassza a **HTTPS**lehetőséget, majd válassza a Fájl **kiválasztása lehetőséget,** és keresse meg a *c:\appgwcert.pfx* fájlt, és válassza a **Megnyitás**lehetőséget.
10. Írja be a *tanúsítvány* nevét és az *Azure123456-ot!* jelszót.
11. Hagyja letiltva a webalkalmazás tűzfalát, majd kattintson **az OK gombra.**
12. Tekintse át az összesítő lap beállításait, majd kattintson az **OK gombra** a hálózati erőforrások és az alkalmazásátjáró létrehozásához. Az alkalmazásátjáró létrehozása több percig is eltarthat, várjon, amíg a központi telepítés sikeresen befejeződik, mielőtt továbblépne a következő szakaszra.

### <a name="add-a-subnet"></a>Alhálózat hozzáadása

1. Válassza a bal oldali menü **Minden erőforrás elemét,** majd válassza a **myVNet** elemet az erőforráslistából.
2. Válassza **az Alhálózatok**lehetőséget, majd kattintson **az Alhálózat gombra.**

    ![Alhálózat létrehozása](./media/create-url-route-portal/application-gateway-subnet.png)

3. Írja be a *myBackendSubnet* nevet az alhálózat nevéhez.
4. Írja be a *10.0.2.0/24* parancsot a címtartományhoz, majd kattintson az **OK gombra.**

## <a name="add-a-listener-and-redirection-rule"></a>Figyelő és átirányítási szabály hozzáadása

### <a name="add-the-listener"></a>A figyelő hozzáadása

Először adja hozzá a *myListener* nevű hallgatót a 80-as porthoz.

1. Nyissa meg a **myResourceGroupAG** erőforráscsoportot, és válassza a **myAppGateway**lehetőséget.
2. Válassza **a Figyelők,** majd **a + Alap .**
3. Írja be a *MyListener* nevet a névhez.
4. Írja be a *httpPort* nevet az új előtérport nevéhez és *80-at* a porthoz.
5. Győződjön meg arról, hogy a protokoll **HTTP-re**van állítva, majd kattintson **az OK gombra.**

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>Átirányítási konfigurációval rendelkező útválasztási szabály hozzáadása

1. A **myAppGateway -n**válassza a **Szabályok** lehetőséget, majd a **+Routing szabály kérése lehetőséget.**
2. A **szabály nevéhez**írja be *a 2.*
3. Győződjön meg arról, hogy a **MyListener** ki van jelölve a figyelőszámára.
4. Kattintson a **Háttérrendszer célok** fülre, és válassza a **Céltípus lehetőséget** *átirányításként.*
5. Az **Átirányítás típushoz**válassza **az Állandó**lehetőséget.
6. Az **Átirányítási cél**kiválasztásához válassza **a Figyelő**lehetőséget.
7. Győződjön meg arról, hogy a **Célfigyelő** **appGatewayHttpListener**értékre van állítva.
8. A **Lekérdezési karakterlánckal együtt** és az **elérési úttal együtt** válassza az *Igen*lehetőséget.
9. Válassza a **Hozzáadás** lehetőséget.

## <a name="create-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport létrehozása

Ebben a példában egy olyan virtuálisgép-méretezési csoportot hoz létre, amely kiszolgálókat biztosít a háttérkészlet számára az alkalmazásátjáróban.

1. A portál bal felső sarkában válassza az **"Erőforrás létrehozása**" lehetőséget.
2. Válassza ki **Számítás** (Compute) lehetőséget.
3. A keresőmezőbe írja be a *méretezési készletet,* és nyomja le az Enter billentyűt.
4. Válassza **a Virtuálisgép-méretezéskészlet**lehetőséget, majd a **Létrehozás**lehetőséget.
5. A **Virtuálisgép méretezési csoport nevéhez**írja be *a myvmss (Myvmss ) mezőbe.*
6. Az Operációs rendszer lemezlemez-lemezlemez-lemezlemez-lemezképéhez** győződjön meg arról, hogy a **Windows Server 2016 Datacenter** ki van jelölve.
7. Az **Erőforráscsoport csoportban**válassza a **myResourceGroupAG lehetőséget.**
8. A **Felhasználónév mezőbe**írja be az *azureuser (azureuser*) mezőbe.
9. **Jelszó mezőbe**írja be az *Azure123456!* és erősítse meg a jelszót.
10. A **példányok száma**esetén győződjön meg arról, hogy az érték **2**.
11. A **Példány mérete**esetén válassza a **D2s_v3**lehetőséget.
12. A **Hálózat csoportban**győződjön meg arról, hogy **a Terheléselosztás beállításainak megadása** az Application **Gateway**beállításra van állítva.
13. Győződjön meg arról, hogy az **alkalmazásátjáró** **a myAppGateway-re**van állítva.
14. Győződjön meg arról, hogy az **alhálózat** **beállítása myBackendSubnet**.
15. Kattintson a **Létrehozás** gombra.

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>A méretezési készlet társítása a megfelelő háttérkészlettel

A virtuálisgép-méretezési csoport portál felhasználói felülete új háttérkészletet hoz létre a méretezési csoporthoz, de szeretné társítani a meglévő appGatewayBackendPool-hoz.

1. Nyissa meg a **myResourceGroupAg** erőforráscsoportot.
2. Válassza a **myAppGateway**lehetőséget.
3. Válassza **a háttérkészletek lehetőséget**.
4. Válassza a **myAppGatewaymyvmss lehetőséget.**
5. Válassza **az Összes cél eltávolítása a háttérkészletből**lehetőséget.
6. Kattintson a **Mentés** gombra.
7. Miután ez a folyamat befejeződött, válassza ki a **myAppGatewaymyvmss** háttérkészletét, majd válassza a **Törlés,** majd **az OK gombot** a megerősítéshez.
8. Válassza **az appGatewayBackendPool lehetőséget.**
9. A **Célok csoportban**válassza a **VMSS lehetőséget.**
10. A **VMSS**csoportban válassza **a myvmss lehetőséget.**
11. A **Hálózati adapter konfigurációja csoportban**válassza **a myvmssNic**lehetőséget.
12. Kattintson a **Mentés** gombra.

### <a name="upgrade-the-scale-set"></a>A méretezési készlet frissítése

Végül ezekkel a módosításokkal frissítenie kell a méretezési készletet.

1. Válassza ki a **myvmss méretezési** készletet.
2. A **Beállítások** alatt válassza a **Példányok** lehetőséget.
3. Jelölje ki mindkét példányt, majd kattintson **a Frissítés gombra.**
4. Válassza az **Igen** lehetőséget a megerősítéshez.
5. Miután ez befejeződött, lépjen vissza a **myAppGateway-hez,** és válassza **a Háttérkészletek lehetőséget.** Most látnia kell, hogy az **appGatewayBackendPool** két cél, és **myAppGatewaymyvmss** nulla célokat.
6. Válassza a **myAppGatewaymyvms**s lehetőséget, majd a **Törlés**lehetőséget.
7. Válassza az **OK** lehetőséget a megerősítéshez.

### <a name="install-iis"></a>Az IIS telepítése

Az IIS méretezési készletre történő telepítésének egyszerű módja a PowerShell használata. A portálon kattintson a Cloud Shell ikonra, és győződjön meg arról, hogy a **PowerShell** ki van jelölve.

Illessze be a következő kódot a PowerShell ablakba, és nyomja le az Enter billentyűt.

```azurepowershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

### <a name="upgrade-the-scale-set"></a>A méretezési készlet frissítése

Miután módosította a példányokat az IIS rendszerrel, ezzel a módosítással újra frissítenie kell a méretezési készletet.

1. Válassza ki a **myvmss méretezési** készletet.
2. A **Beállítások** alatt válassza a **Példányok** lehetőséget.
3. Jelölje ki mindkét példányt, majd kattintson **a Frissítés gombra.**
4. Válassza az **Igen** lehetőséget a megerősítéshez.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Az alkalmazás nyilvános IP-címét az alkalmazásátjáró áttekintése lapról szerezheti be.

1. Válassza a **myAppGateway**lehetőséget.
2. Az **Áttekintés** lapon jegyezze fel az IP-címet a **Frontend nyilvános IP-címe**alatt.

3. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Például: http://52.170.203.149

   ![Biztonsági figyelmeztetés](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Ha önaláírt tanúsítványt használt, a biztonsági figyelmeztetés elfogadásához válassza a **Részletek** lehetőséget, majd válassza a **Továbblépés a weblapra** lehetőséget. Ekkor a biztonságos IIS-webhely a következő példához hasonlóan jelenik meg:

   ![Az alap URL-cím tesztelése az alkalmazásátjáróban](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [hozhat létre alkalmazásátjárót belső átirányítással.](redirect-internal-site-powershell.md)
