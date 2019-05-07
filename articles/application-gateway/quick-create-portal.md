---
title: Rövid útmutató – Webes forgalom irányítása az Azure Application Gatewayjel – Azure Portal | Microsoft Docs
description: Ismerje meg, amely arra utasítja a webes forgalom háttérkészlet a virtuális gépek az Azure Application Gateway létrehozása az Azure portal használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 5/7/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: bcbbb63206a443d87afa656ace6f141c6567d17d
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192669"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Gyors útmutató: A közvetlen webes forgalom az Azure Application Gatewayjel – Azure portal

Ez a rövid útmutató bemutatja, hogyan hozzon létre egy application gateway az Azure portal használatával.  Az application gateway létrehozása után tesztelje, hogy ellenőrizze, hogy azok megfelelően működnek. Az Azure Application Gatewayjel az alkalmazás webes forgalom az erőforrásoknál figyelői hozzárendelése portokat, szabályok és hozzáadunk erőforrásokat egy háttérkészlet közvetlen. Az egyszerűség kedvéért a jelen cikk egy egyszerű beállítás nyilvános előtérbeli IP-cím, egy alapszintű figyelő gazdagéphez az application gateway-en egyetlen hely, a háttérkészlet és a egy egyszerű kérelem-útválasztási szabály használható két virtuális gépet használ.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az Azure-hoz az erőforrások közötti kommunikációt, hogy hozzon létre egy virtuális hálózat szükséges. Hozzon létre egy új virtuális hálózatot, vagy használjon egy meglévőt. Ebben a példában egy új virtuális hálózatot fog létrehozni. Az application gateway létrehozására, egyszerre egy virtuális hálózatot hoz létre. Application Gateway-példány külön alhálózatra jönnek létre. Ebben a példában két alhálózattal hoz létre: egyet az application gateway, a másik pedig a háttérkiszolgálókhoz.

1. Válassza ki **erőforrás létrehozása** a az Azure Portal bal oldali menüben. A **új** ablak jelenik meg.

2. Válassza ki **hálózatkezelés** , majd **Application Gateway** a a **kiemelt** listája.

### <a name="basics-page"></a>Alapvető beállítások lap

1. Az a **alapjai** lap, adja meg ezeket az értékeket a következő application gateway beállításait:

   - **Név**: Adja meg *myAppGateway* az application gateway neve.
   - **Erőforráscsoport**: Válassza ki **myResourceGroupAG** az erőforráscsoportnak. Ha még nem létezik, válassza ki a **új létrehozása** létrehozásához.

     ![Új alkalmazásátjáró létrehozása](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

2. Az alapértelmezett értékeket a többi beállítás esetében fogadja el, majd **OK**.

### <a name="settings-page"></a>Beállítások lap

1. Az a **beállítások** lap **alhálózati konfigurációt**válassza **virtuális hálózat választása**. <br>

2. Az a **válasszon egy virtuális hálózatot** lapon jelölje be **új létrehozása**, majd adja meg az értékeket a következő virtuális hálózati beállítások:

   - **Név**: Adja meg *myVNet* a virtuális hálózat neve.

   - **Címtér**: Adja meg *10.0.0.0/16* számára a virtuális hálózat címterét.

   - **Alhálózat neve**: Adja meg *myAGSubnet* az alhálózat neve.<br>Az application gateway alhálózatának csak az application Gateway-átjárókon is tartalmazhat. Egyéb erőforrások nem engedélyezettek.

   - **Alhálózati címtartomány**: Adja meg *10.0.0.0/24* -alhálózat címtartománya.

     ![Virtuális hálózat létrehozása](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

3. Válassza ki **OK** térjen vissza a **beállítások** lapot.

4. Válassza ki a **előtérbeli IP-konfiguráció**. A **előtérbeli IP-konfiguráció**, ellenőrizze **IP-cím típusa** értékre van állítva **nyilvános**. A **nyilvános IP-cím**, ellenőrizze **új létrehozása** van kiválasztva. <br>Az előtérbeli IP-cím lehet magán- vagy a használati eset megfelelően konfigurálhatja. Ebben a példában egy nyilvános előtérbeli IP-címet fogja választani.
   > [!NOTE]
   > Az Application Gateway v2 szintű Termékváltozatot, csak kiválaszthatja az **nyilvános** előtérbeli IP-konfigurációt. Magánhálózati előtérbeli IP-konfiguráció jelenleg nem engedélyezett a v2-termékváltozat.

5. Adja meg *myAGPublicIPAddress* számára a nyilvános IP-cím neve. 

6. Az alapértelmezett értékeket a többi beállítás esetében fogadja el, majd **OK**.<br>Az egyszerűség kedvéért ez a cikk az alapértelmezett értékeket fog választani, de be lehet állítani egyéni értékeket a többi beállítás esetében attól függően, a használati eset 

### <a name="summary-page"></a>Összefoglaló lap

Tekintse át a beállításokat a **összefoglalás** oldalra, és kattintson **OK** a virtuális hálózat, nyilvános IP-cím és az application gateway létrehozásához. Az application gateway létrehozása több percig is eltarthat. Várjon, amíg az üzembe helyezés sikeresen befejeződik, mielőtt továbblép a következő szakaszban.

## <a name="add-backend-pool"></a>Háttérkészlet hozzáadása

A háttérkészlet segítségével irányíthatja a kérelmeket a háttérkiszolgálók, amely teljesíteni a kérést. Háttérkészletek összeállítható a hálózati adapterek, a virtual machine scale sets, nyilvános IP-címek, belső IP-címek, teljesen minősített neve (FQDN), és több-bérlős háttéralkalmazások hasonlóan az Azure App Service-ben. A háttérrendszer célok fogja hozzáadni a háttérkészlethez.

Ebben a példában a virtuális gépek, a cél háttérrendszer fogja használni. Meglévő virtuális gépeken, vagy újakat hoz létre. Létre fog hozni a két virtuális gépet használó Azure háttérkiszolgálóiként az application gateway számára.

Ehhez jelennek meg:

1. Hozzon létre egy új alhálózatot *myBackendSubnet*, az új virtuális gép létrejön.
2. Két új virtuális gép létrehozása *myVM* és *myVM2*háttérkiszolgálóiként használandó.
3. IIS telepítése a virtuális gépeket, ellenőrizze, hogy az application gateway létrehozása sikeres volt.
4. A háttérkiszolgálók hozzáadásához a háttérkészlethez.

### <a name="add-a-subnet"></a>Alhálózat hozzáadása

Adjon hozzá egy alhálózatot a következő lépések során létrehozott virtuális hálózat:

1. Válassza ki **összes erőforrás** adja meg az Azure portal bal oldali menüben *myVNet* a keresési mezőbe, és válassza ki a **myVNet** a keresési eredmények közül.

2. Válassza ki **alhálózatok** a bal oldali menüben, és válassza ki a **+ alhálózat**. 

   ![Alhálózat létrehozása](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Az a **alhálózat hozzáadása** lap, adja meg *myBackendSubnet* számára a **neve** az alhálózatot, és válassza ki a **OK**.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Az Azure Portalon, válassza ki a **erőforrás létrehozása**. A **új** ablak jelenik meg.
2. Válassza ki **számítási** majd **Windows Server 2016 Datacenter** a a **kiemelt** listája. A **hozzon létre egy virtuális gépet** lap jelenik meg.<br>Az Application Gateway is irányítják a forgalmat a virtuális gép a háttérkészletben használni bármilyen típusú. Ebben a példában egy Windows Server 2016 Datacenter használja.
3. Adja meg ezeket az értékeket a **alapjai** lapon találja a következő virtuális gép beállításait:

    - **Erőforráscsoport**: Válassza ki **myResourceGroupAG** az erőforráscsoport neveként.
    - **A virtuális gép neve**: Adja meg *myVM* a virtuális gép neve.
    - **Felhasználónév**: Adja meg *azureuser* az rendszergazdájának felhasználóneve.
    - **Jelszó**: Adja meg *Azure123456!* a rendszergazdai jelszót.
4. Elfogadhatja az alapértelmezett beállításokat, majd **tovább: Lemezek**.  
5. Fogadja el a **lemezek** alapértelmezett lapot, majd **tovább: Hálózatkezelés**.
6. Az a **hálózatkezelés** lapon ellenőrizze, hogy **myVNet** van kiválasztva a **virtuális hálózati** és a **alhálózati** értékre van állítva  **myBackendSubnet**. Elfogadhatja az alapértelmezett beállításokat, majd **tovább: Felügyeleti**.<br>Az Application Gateway képes kommunikálni az, hogy a virtuális hálózaton kívüli példányok, de győződjön meg arról, IP van kapcsolat.
7. Az a **felügyeleti** lapra, és állítsa **rendszerindítási diagnosztika** való **ki**. Elfogadhatja az alapértelmezett beállításokat, majd **felülvizsgálat + létrehozása**.
8. Az a **tekintse át + létrehozása** lapon tekintse át a beállításokat, javíthatja az esetleges érvényesítési hibákat, és válassza ki **létrehozás**.
9. Várjon, amíg a virtuális gép létrehozásának befejeződését a továbblépés előtt.

### <a name="install-iis-for-testing"></a>Az IIS telepítése teszteléshez

Ebben a példában telepítse az IIS a virtuális gépek csak azt kell ellenőriznie az Azure application gateway létrehozása sikerült.

1. Nyissa meg [az Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Ehhez válassza ki a **Cloud Shell** az Azure Portalon, és ezután válassza a felső navigációs sávon **PowerShell** a legördülő listából. 

    ![Egyéni bővítmény telepítése](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

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

3. Hozzon létre egy második virtuális gépet, és az IIS telepítése a korábban végrehajtott lépések segítségével. Használat *myVM2* a virtuális gép nevét, valamint a a **VMName** beállításaként a **Set-AzVMExtension** parancsmagot.

### <a name="add-backend-servers-to-backend-pool"></a>Háttérkiszolgálók háttérkészlet hozzáadása

1. Válassza ki **összes erőforrás**, majd válassza ki **myAppGateway**.

2. Válassza ki **háttérkészletek** a bal oldali menüből. Az Azure automatikusan létrejön egy alapértelmezett címkészlet **appGatewayBackendPool**, az application gateway létrehozása során. 

3. Válassza ki **appGatewayBackendPool**.

4. A **célok**válassza **virtuális gép** a legördülő listából.

5. A **virtuális gép** és **hálózati adapterek**, jelölje be a **myVM** és **myVM2** virtuális gépek és a kapcsolódó hálózati a legördülő listákból felületek.

    ![Háttérkiszolgálók hozzáadása](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Kattintson a **Mentés** gombra.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Bár az application gateway létrehozásához az IIS nem szükséges, telepítette azt ebben a rövid, ellenőrizze, hogy az Azure sikeresen létrejött-e az application gateway. Az IIS használatával az application gateway teszteléséhez:

1. Az application gateway a nyilvános IP-cím keresése a **áttekintése** lap.![ Jegyezze fel az application gateway nyilvános IP-cím](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) vagy választhat **összes erőforrás**, adja meg *myAGPublicIPAddress* keresési mezőbe, majd kattintson a keresési eredmények között. Az Azure nyilvános IP-címét jeleníti meg a **áttekintése** lapot.
2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.
3. Ellenőrizze a választ. Érvényes válasz ellenőrzi, hogy az application gateway létrehozása sikeresen megtörtént-e, és sikeresen csatlakozott a háttérszolgáltatás használatára.![Az alkalmazásátjáró tesztelése](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

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
