---
title: 'Oktatóanyag: URL-útvonal-alapú útválasztási szabályok a portál használatával – Azure Application Gateway'
description: Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre URL-útvonal-alapú útválasztási szabályokat egy alkalmazásátjáróhoz és a virtuálisgép-méretezési csoporthoz az Azure Portal használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: bc810ac7901d83f03d3f3ac2199561225326d261
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74048140"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Oktatóanyag: Hozzon létre egy alkalmazásátjárót útalapú útválasztási szabályokkal az Azure Portal használatával

Az Azure Portal segítségével [konfigurálhatja az URL-útvonal-alapú útválasztási szabályokat,](application-gateway-url-route-overview.md) amikor létrehoz egy [alkalmazásátjárót.](application-gateway-introduction.md) Ebben az oktatóanyagban háttérkészleteket hoz létre virtuális gépek használatával. Ezután hozzon létre útválasztási szabályokat, amelyek biztosítják, hogy a webes forgalom megérkezik a készletek megfelelő kiszolgálóihoz.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Application Gateway létrehozása
> * Virtuális gépek létrehozása háttérkiszolgálókhoz
> * Háttérkészletek létrehozása a háttérkiszolgálókkal
> * Háttérfigyelő létrehozása
> * Görbealapú útválasztási szabály létrehozása

![URL-útválasztási példa](./media/application-gateway-create-url-route-portal/scenario.png)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Bejelentkezés az Azure Portalra[https://portal.azure.com](https://portal.azure.com)

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Ebben a példában három virtuális gépet hoz létre, amelyek az alkalmazásátjáró háttérkiszolgálóiként használhatók. Az IIS-t a virtuális gépekre is telepíti annak ellenőrzésére, hogy az alkalmazásátjáró a várt módon működik-e.

1. Az Azure Portalon válassza az **Erőforrás létrehozása lehetőséget.**
2. Válassza a **Windows Server 2016 Datacenter elemet** a Népszerű listában.
3. Adja meg a következő értékeket a virtuális gép számára:

    - **Erőforráscsoport**, válassza **az Új létrehozása**lehetőséget, majd írja be a *myResourceGroupAG parancsot.*
    - **Virtuális gép neve**: *myVM1*
    - **Régió**: *(USA) USA keleti régiója*
    - **Felhasználónév**: *azureuser*
    - **Jelszó**: *Azure123456!*


4. Válassza a **Tovább:Lemezek**lehetőséget.
5. Válassza a **Tovább:Hálózat**
6. **Virtuális hálózat**esetén válassza az Új **létrehozása lehetőséget,** majd írja be ezeket az értékeket a virtuális hálózathoz:

   - A virtuális hálózat neve *myVNet*.
   - A virtuális hálózat címtere *10.0.0.0/16*.
   - *myBackendSubnet* az első alhálózat nevéhez
   - *10.0.1.0/24* - az alhálózati címtérhez.
   - *myAGSubnet* - a második alhálózat nevéhez.
   - Az alhálózat címtere *10.0.0.0/24*.
7. Válassza **az OK gombot.**

8. Győződjön meg arról, hogy a **Hálózati adapter**csoportban a **myBackendSubnet** ki van jelölve az alhálózathoz, majd válassza a **Tovább: Kezelés**lehetőséget.
9. A **rendszerindítási** diagnosztika letiltásához válassza a Ki lehetőséget.
10. Kattintson **a Véleményezés + Létrehozás**gombra, tekintse át az összesítő lap beállításait, és válassza a **Létrehozás lehetőséget.**
11. Hozzon létre még két virtuális gépet, *a myVM2-t* és a *myVM3-at,* és helyezze őket a *MyVNet* virtuális hálózatba és a *myBackendSubnet* alhálózatba.

### <a name="install-iis"></a>Az IIS telepítése

1. Nyissa meg az interaktív rendszerhéjat, és győződjön meg arról, hogy a **PowerShell**van beállítva.

    ![Egyéni bővítmény telepítése](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. Futtassa a következő parancsot az IIS a virtuális gépen való telepítéséhez: 

    ```azurepowershell
         $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

        Set-AzVMExtension `
         -ResourceGroupName myResourceGroupAG `
         -Location eastus `
         -ExtensionName IIS `
         -VMName myVM1 `
         -Publisher Microsoft.Compute `
         -ExtensionType CustomScriptExtension `
         -TypeHandlerVersion 1.4 `
         -Settings $publicSettings
    ```

3. Hozzon létre még két virtuális gépet, és telepítse az IIS-t az imént végzett lépések segítségével. Adja meg a *myVM2* és *a myVM3* nevét és a VMName értékét a Set-AzVMExtension-ben.

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

1. Válassza **az Erőforrás létrehozása lehetőséget** az Azure Portal bal oldali menüjében. Megjelenik **az Új** ablak.

2. Válassza **a Hálózat lehetőséget,** majd a **Kiemelt** listában válassza az **Alkalmazásátjáró** elemet.

### <a name="basics-tab"></a>Alapok lap

1. Az **Alapok lapon** adja meg ezeket az értékeket a következő alkalmazásátjáró-beállításokhoz:

   - **Erőforráscsoport**: Válassza ki a **myResourceGroupAG-t** az erőforráscsoporthoz.
   - **Alkalmazásátjáró neve**: Adja meg a *myAppGateway-t* az alkalmazásátjáró nevéhez.
   - **Régió** - Select **(US) East US**.

        ![Új alkalmazásátjáró létrehozása: Alapok](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  A **Virtuális hálózat konfigurálása**csoportban válassza a **myVNet** lehetőséget a virtuális hálózat nevéhez.
3. Válassza ki a **myAGSubnet** az alhálózathoz.
3. Fogadja el a többi beállítás alapértelmezett értékeit, majd válassza a **Tovább: Előtér lehetőséget.**

### <a name="frontends-tab"></a>Előtér-lapok lap

1. Az **Előtér-szolgáltatások** lapon ellenőrizze, hogy az **előtér IP-címtípusa** **Nyilvános.**

   > [!NOTE]
   > Az Application Gateway v2 termékváltozat esetén csak **a Nyilvános** előtér IP-konfigurációt választhatja. A privát előtér-IP-konfiguráció jelenleg nincs engedélyezve ehhez a v2 termékváltozathoz.

2. Válassza az **Új létrehozása** a **nyilvános IP-címhez** lehetőséget, és írja be a *myAGPublicIPAddress értéket* a nyilvános IP-cím nevéhez, majd kattintson az OK **gombra.** 
3. Válassza a **Tovább: Háttérrendszerek lehetőséget.**

### <a name="backends-tab"></a>Háttérrendszerek lap

A háttérkészlet a kérelmek továbbítására szolgál a kérést kiszolgáló háttérkiszolgálókhoz. Háttérkészletek állhat nicc-ek, virtuálisgép-méretezési készletek, nyilvános IP-k, belső IP-k, teljesen minősített tartománynevek (FQDN) és több-bérlős háttér-rendszerek, mint az Azure App Service.

1. A **Háttérrendszerek** lapon válassza a **+Háttérkészlet hozzáadása**lehetőséget.

2. A **Megnyíló háttérkészlet hozzáadása** ablakban adja meg a következő értékeket egy üres háttérkészlet létrehozásához:

    - **Név**: Adja meg a *sajátbackendPool* nevet a háttérkészlet nevéhez.
3. A **Háttérrendszer-célok**csoportban válassza a **Virtuális gép** **lehetőséget**a legördülő listából.

5. A **Cél csoportban** válassza ki a **myVM1 hálózati felületét.**
6. Válassza a **Hozzáadás** lehetőséget.
7. Ismételje meg a *műveletet:* a myVM2 a célként a *myVM2-vel* és a *myVM3-mal* rendelkező videoháttér-készletet a célként. *Video*
8. Válassza a **Hozzáadás** lehetőséget a háttérkészlet konfigurációjának mentéséhez és a **háttérrendszerek** lapra való visszatéréshez.

4. A **háttérrendszerek** lapon válassza a **Tovább: Konfiguráció**lehetőséget.

### <a name="configuration-tab"></a>Konfiguráció lap

A **Konfiguráció** lapon az útválasztási szabállyal létrehozott előtér- és háttérkészletet fogja csatlakoztatni.

1. Válassza **a Szabály hozzáadása elemet** az **Útválasztási szabályok** oszlopban.

2. A **Megnyíló Útválasztási szabály hozzáadása** ablakban adja meg a szabály nevéhez a *myRoutingRule* **parancsot.**

3. Az útválasztási szabály hoz egy figyelő. Az **Útválasztási szabály hozzáadása** ablak **Figyelő** lapján adja meg a figyelő következő értékeit:

    - **Figyelő neve**: Adja meg *a myListener* nevet a figyelő.
    - **Előtér IP:** **Válassza a Nyilvános** lehetőséget az előtérhez létrehozott nyilvános IP-cím kiválasztásához.
    - **Port**: *8080-as* típus
  
        Fogadja el a **Figyelő** lap többi beállításának alapértelmezett értékeit, majd a **Háttérrendszer-célok** lapra a művelettervi szabály többi részének konfigurálásához.

4. A **háttérrendszer-célok** lapon válassza a **myBackendPool** elemet a **háttér-célhoz.**

5. A **HTTP beállításhoz**válassza az **Új létrehozása lehetőséget** új HTTP-beállítás létrehozásához. A HTTP-beállítás határozza meg az útválasztási szabály viselkedését. 

6. A **Megnyíló HTTP-beállítás hozzáadása** ablakban adja meg a *myHTTPSetting* parancsot a **HTTP-beállítás nevéhez**. Fogadja el a HTTP hozzáadása **beállításablak** egyéb beállításainak alapértelmezett értékeit, majd a **Hozzáadás gombra** a **Művelettervszabály hozzáadása** ablakhoz való visszatéréshez.
7. A **Görbealapú útválasztás**csoportban válassza **a Több tároló hozzáadása lehetőséget a görbealapú szabály létrehozásához.**
8. A **Path**mezőbe írja be a */images/*\*kapcsolót.
9. Az **Elérési út szabály nevéhez**írja be a Képek mezőbe a Képek *(Képek) mezőbe.*
10. **HTTP-beállítás esetén**válassza a **myHTTPSetting lehetőséget**
11. Háttércél **esetén**válassza a **Képek**lehetőséget.
12. Válassza a **Hozzáadás** lehetőséget az elérésiút-szabály mentéséhez és az **Útválasztási szabály hozzáadása** laphoz való visszatéréshez.
13. Ismételje meg a műveletet, ha egy másik szabályt szeretne hozzáadni a Videóhoz.
14. Válassza a **Hozzáadás** lehetőséget az útválasztási szabály hozzáadásához és a **Konfiguráció** lapra való visszatéréshez.
15. Válassza a **Tovább: Címkék,** majd **a Tovább: Véleményezés + létrehozás**lehetőséget.

> [!NOTE]
> Az alapértelmezett esetek kezeléséhez */* nem kell egyéni * elérési útszabályt hozzáadnia. Ezt az alapértelmezett háttérkészlet automatikusan kezeli.

### <a name="review--create-tab"></a>Véleményezés + létrehozás lap

Tekintse át a Beállításokat a **Véleményezés + létrehozás** lapon, majd válassza a **Létrehozás** gombot a virtuális hálózat, a nyilvános IP-cím és az alkalmazásátjáró létrehozásához. Az Azure-nak az alkalmazásátjáró létrehozása több percig is eltarthat. Várja meg, amíg a központi telepítés sikeresen befejeződik, mielőtt továbblépne a következő szakaszra.


## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

1. Válassza **az Összes erőforrás**lehetőséget, majd a **myAppGateway**lehetőséget.

    ![Alkalmazásátjáró nyilvános IP-címének rögzítése](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Mint például,\/http: /52.188.72.175:8080.

    ![Az alap URL-cím tesztelése az alkalmazásátjáróban](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

   A 8080-as port figyelője a kérést az alapértelmezett háttérkészlethez irányítja.

3. Módosítsa az *URL-címet&lt;&gt;http:// ip-címre:8080/images/test.htm*, az IP-cím ip-címének &lt;&gt; az IP-címre való lecserélése, és a következő példához hasonló példát kell látnia:

    ![Tesztképek URL-címe az alkalmazásátjáróban](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

   A 8080-as port figyelője a képkezelő háttérkészletbe *irányítja* ezt a kérést.

4. Módosítsa az *URL-címet&lt;&gt;http:// ip-címre:*&gt; 8080/video/test.htm , az IP-címet az IP-címre cseréli, &lt;és a következő példához hasonló példát kell látnia:

    ![Tesztvideó URL-címe az alkalmazásátjáróban](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

   A 8080-as port figyelője a *videoháttér-készlethez* irányítja ezt a kérést.


## <a name="next-steps"></a>További lépések

- [Az SSL befejezésének engedélyezése az Azure Application Gateway-en](application-gateway-backend-ssl.md)
