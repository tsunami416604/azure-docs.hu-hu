---
title: 'Rövid útmutató: Közvetlen webes forgalom a portál használatával'
titleSuffix: Azure Application Gateway
description: Megtudhatja, hogyan hozhat létre az Azure Portalon egy Azure Application Gateway-t, amely a webes forgalmat egy háttérkészletben lévő virtuális gépekre irányítja.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/09/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 17adc800bd5a2ae53e27350c7e0d588eaeee4a8f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241401"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Rövid útmutató: Webes forgalom irányítása az Azure Application Gatewayjel – Azure Portal

Ebben a rövid útmutatóban az Azure Portal használatával hozzon létre egy alkalmazásátjárót. Ezután tesztelje, hogy megfelelően működik-e. 

Az alkalmazásátjáró az alkalmazás webforgalmát egy háttérkészlet ben lévő erőforrásokhoz irányítja. A figyelők portokhoz rendelhető, szabályokat hozhat létre, és erőforrásokat adhat hozzá egy háttérkészlethez. Az egyszerűség kedvéért ez a cikk egy egyszerű beállítást használ egy nyilvános előtér-IP-cím, egy alapszintű figyelő egyetlen hely üzemeltetéséhez az alkalmazásátjárón, egy alapvető kérelem-útválasztási szabályt és két virtuális gépet a háttérkészletben.

Ezt a rövid útmutatót az [Azure PowerShell](quick-create-powershell.md) vagy az Azure CLI használatával is elvégezheti. [Azure CLI](quick-create-cli.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]



## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az alkalmazásátjárót az **Alkalmazásátjáró létrehozása** lap lapjaival hozza létre.

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet. Megjelenik **az Új** ablak.

2. Válassza **a Hálózat lehetőséget,** majd a **Kiemelt** listában válassza az **Alkalmazásátjáró** elemet.

### <a name="basics-tab"></a>Alapok lap

1. Az **Alapok lapon** adja meg ezeket az értékeket a következő alkalmazásátjáró-beállításokhoz:

   - **Erőforráscsoport**: Válassza ki a **myResourceGroupAG-t** az erőforráscsoporthoz. Ha nem létezik, válassza az **Új létrehozása** lehetőséget a létrehozáshoz.
   - **Alkalmazásátjáró neve**: Adja meg a *myAppGateway-t* az alkalmazásátjáró nevéhez.

     ![Új alkalmazásátjáró létrehozása: Alapok](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2. Ahhoz, hogy az Azure kommunikáljon a létrehozott erőforrások között, virtuális hálózatra van szüksége. Létrehozhat egy új virtuális hálózatot, vagy használhat egy meglévőt. Ebben a példában egy új virtuális hálózatot hoz létre az alkalmazásátjáró létrehozása kor. Az Alkalmazásátjáró-példányok külön alhálózatokban jönnek létre. Ebben a példában két alhálózatot hoz létre: egyet az alkalmazásátjáróhoz, egyet pedig a háttérkiszolgálókhoz.

    A **Virtuális hálózat konfigurálása**csoportban válasszuk az Új létrehozása lehetőséget, és válassza az **Új létrehozása**lehetőséget. A **megnyíló Virtuális hálózat létrehozása** ablakban adja meg a következő értékeket a virtuális hálózat és két alhálózat létrehozásához:

    - **Név**: Adja meg a *myVNet* nevet a virtuális hálózat nevéhez.

    - **Alhálózat neve** (Application Gateway alhálózat): Az **Alhálózatok** rácsegy *Alapértelmezett*nevű alhálózatot jelenít meg. Módosítsa az alhálózat nevét *myAGSubnet*névre.<br>Az alkalmazásátjáró alhálózata csak alkalmazásátjárókat tartalmazhat. Más erőforrások nem engedélyezettek.

    - **Alhálózat neve** (háttérkiszolgáló-alhálózat): Az **Alhálózatok** rács második sorában adja meg a *myBackendSubnet* értéket az **Alhálózat név** oszlopában.

    - **Címtartomány** (háttérkiszolgáló-alhálózat): Az **Alhálózatok** hálózatának második sorában adjon meg egy olyan címtartományt, amely nem fedi át a *myAGSubnet*címtartományát. Ha például a *myAGSubnet* címtartománya 10.0.0.0/24, írja be a *10.0.1.0/24* értéket a *myBackendSubnet*címtartományába.

    A **Virtuális hálózat létrehozása** ablak bezárásához és a virtuális hálózati beállítások mentéséhez válassza az OK **gombot.**

     ![Új alkalmazásátjáró létrehozása: virtuális hálózat](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Az **Alapok** lapon fogadja el a többi beállítás alapértelmezett értékeit, majd válassza a **Tovább: Előtér -** lehetőséget.

### <a name="frontends-tab"></a>Előtér-lapok lap

1. Az **Előtér-szolgáltatások** lapon ellenőrizze, hogy az **előtér IP-címtípusa** **Nyilvános.** <br>Beállíthatja, hogy az előtér-IP-cím nyilvános vagy privát legyen a használati esetnek hasonlóan. Ebben a példában egy nyilvános előtér-IP-címet választ.
   > [!NOTE]
   > Az Application Gateway v2 termékváltozat, **Public** nyilvános előtér-IP-konfigurációnak kell lennie. Továbbra is rendelkezhet nyilvános és privát előtér-IP-konfigurációval, de a csak privát előtér-IP-konfiguráció (Csak ILB-mód) jelenleg nincs engedélyezve a v2 termékváltozathoz. 

2. Válassza az **Új létrehozása** a **nyilvános IP-címhez** lehetőséget, és írja be a *myAGPublicIPAddress értéket* a nyilvános IP-cím nevéhez, majd kattintson az OK **gombra.** 

     ![Új alkalmazásátjáró létrehozása: előtér-rendszerek](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Válassza a **Tovább: Háttérrendszerek lehetőséget.**

### <a name="backends-tab"></a>Háttérrendszerek lap

A háttérkészlet a kérelmek továbbítására szolgál a kérést kiszolgáló háttérkiszolgálókhoz. Háttérkészletek állhat nicc-ek, virtuálisgép-méretezési készletek, nyilvános IP-k, belső IP-k, teljesen minősített tartománynevek (FQDN) és több-bérlős háttér-rendszerek, mint az Azure App Service. Ebben a példában egy üres háttérkészletet hoz létre az alkalmazásátjáróval, majd háttércélokat ad hozzá a háttérkészlethez.

1. A **Háttérrendszerek** lapon válassza a **+Háttérkészlet hozzáadása**lehetőséget.

2. A **Megnyíló háttérkészlet hozzáadása** ablakban adja meg a következő értékeket egy üres háttérkészlet létrehozásához:

    - **Név**: Adja meg a *sajátbackendPool* nevet a háttérkészlet nevéhez.
    - **Háttérkészlet hozzáadása célok nélkül:** Válassza az **Igen** lehetőséget egy cél nélküli háttérkészlet létrehozásához. Az alkalmazásátjáró létrehozása után háttércélokat fog hozzáadni.

3. A **Háttérkészlet hozzáadása** ablakban válassza a **Hozzáadás** lehetőséget a háttérkészlet konfigurációjának mentéséhez és a **háttérrendszerek** lapra való visszatéréshez.

     ![Új alkalmazásátjáró létrehozása: háttérrendszerek](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. A **háttérrendszerek** lapon válassza a **Tovább: Konfiguráció**lehetőséget.

### <a name="configuration-tab"></a>Konfiguráció lap

A **Konfiguráció** lapon az útválasztási szabállyal létrehozott előtér- és háttérkészletet fogja csatlakoztatni.

1. Válassza **a Szabály hozzáadása elemet** az **Útválasztási szabályok** oszlopban.

2. A **Megnyíló Útválasztási szabály hozzáadása** ablakban adja meg a szabály nevéhez a *myRoutingRule* **parancsot.**

3. Az útválasztási szabály hoz egy figyelő. Az **Útválasztási szabály hozzáadása** ablak **Figyelő** lapján adja meg a figyelő következő értékeit:

    - **Figyelő neve**: Adja meg *a myListener* nevet a figyelő.
    - **Előtér IP:** **Válassza a Nyilvános** lehetőséget az előtérhez létrehozott nyilvános IP-cím kiválasztásához.
  
      Fogadja el a **Figyelő** lap többi beállításának alapértelmezett értékeit, majd a **Háttérrendszer-célok** lapra a művelettervi szabály többi részének konfigurálásához.

   ![Új alkalmazásátjáró létrehozása: figyelő](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-listener.png)

4. A **háttérrendszer-célok** lapon válassza a **myBackendPool** elemet a **háttér-célhoz.**

5. A **HTTP beállításhoz**válassza az **Új létrehozása lehetőséget** új HTTP-beállítás létrehozásához. A HTTP-beállítás határozza meg az útválasztási szabály viselkedését. A **Megnyíló HTTP-beállítás hozzáadása** ablakban adja meg a *myHTTPSetting* értéket a **HTTP-beállítás nevéhez** és *a 80* értéket a **háttérporthoz.** Fogadja el a HTTP hozzáadása **beállításablak** egyéb beállításainak alapértelmezett értékeit, majd a **Hozzáadás gombra** a **Művelettervszabály hozzáadása** ablakhoz való visszatéréshez. 

     ![Új alkalmazásátjáró létrehozása: HTTP-beállítás](./media/application-gateway-create-gateway-portal/application-gateway-create-httpsetting.png)

6. A **Művelettervszabály hozzáadása** ablakban válassza a **Hozzáadás** lehetőséget a művelettervi szabály mentéséhez és a **Konfiguráció** lapra való visszatéréshez.

     ![Új alkalmazásátjáró létrehozása: útválasztási szabály](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Válassza a **Tovább: Címkék,** majd **a Tovább: Véleményezés + létrehozás**lehetőséget.

### <a name="review--create-tab"></a>Véleményezés + létrehozás lap

Tekintse át a Beállításokat a **Véleményezés + létrehozás** lapon, majd válassza a **Létrehozás** gombot a virtuális hálózat, a nyilvános IP-cím és az alkalmazásátjáró létrehozásához. Az Azure-nak az alkalmazásátjáró létrehozása több percig is eltarthat. Várja meg, amíg a központi telepítés sikeresen befejeződik, mielőtt továbblépne a következő szakaszra.

## <a name="add-backend-targets"></a>Háttér-célok hozzáadása

Ebben a példában a virtuális gépeket fogja használni a cél háttérrendszerként. Használhatja a meglévő virtuális gépeket, vagy újakat hozhat létre. Két virtuális gépet hoz létre az alkalmazásátjáró háttérkiszolgálóiként.

Ehhez a következőkre van szüksége:

1. Hozzon létre két új virtuális gépet, *myVM-et* és *myVM2-t,* amelyek háttérkiszolgálóként használhatók.
2. Telepítse az IIS-t a virtuális gépekre az alkalmazásátjáró sikeres létrehozásának ellenőrzéséhez.
3. Adja hozzá a háttérkiszolgálókat a háttérkészlethez.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet. Megjelenik **az Új** ablak.
2. Válassza a **Windows Server 2016 Datacenter elemet** a **Népszerű** listában. Megjelenik **a Virtuálisgép létrehozása** lap.<br>Az Application Gateway a háttérkészletében használt bármilyen típusú virtuális gépre irányíthatja a forgalmat. Ebben a példában Windows Server 2016 Datacenter t használ.
3. Adja meg ezeket az értékeket az **Alapok** lapon a következő virtuális gépbeállításokhoz:

    - **Erőforráscsoport**: Válassza ki a **myResourceGroupAG** elemet az erőforráscsoport nevéhez.
    - **Virtuális gép neve**: Adja meg a *virtuális* gép nevét.
    - **Régió**: Válassza ki ugyanazt a régiót, ahol létrehozta az alkalmazásátjárót.
    - **Felhasználónév**: Írja be *az azureuser* parancsot a rendszergazdai felhasználónévhez.
    - **Jelszó**: Írjon be egy jelszót.
4. Fogadja el a többi alapértelmezést, majd válassza **a Tovább: Lemezek**lehetőséget.  
5. Fogadja el a **Lemezek** lap alapértelmezett beállításait, és válassza **a Tovább: Hálózat lehetőséget.**
6. A **Hálózat** lapon ellenőrizze, hogy a **myVNet** ki van-e jelölve a **virtuális hálózathoz,** és az **alhálózat** beállítása **myBackendSubnet**. Fogadja el a többi alapértelmezést, majd válassza **a Tovább: Kezelés**lehetőséget.<br>Az Application Gateway képes kommunikálni a virtuális hálózaton kívüli példányokkal, de biztosítania kell az IP-kapcsolatot.
7. A **Kezelés** lapon állítsa a **Rendszerindításdia imát** **Ki**beállításra. Fogadja el a többi alapértelmezést, majd válassza **a Véleményezés + létrehozás**lehetőséget.
8. A **Véleményezés + Létrehozás** lapon tekintse át a beállításokat, javítsa ki az érvényesítési hibákat, és válassza a **Létrehozás gombot.**
9. A folytatás előtt várja meg, amíg befejeződik a virtuális gép létrehozása.

### <a name="install-iis-for-testing"></a>Az IIS telepítése tesztelésre

Ebben a példában csak az Azure sikeres létrehozásához telepítse az IIS-t a virtuális gépekre.

1. Nyissa meg az Azure PowerShellt. Válassza ki a **Cloud Shell** az Azure Portal felső navigációs sávján, majd válassza a **PowerShell** a legördülő listából. 

    ![Egyéni bővítmény telepítése](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Az IIS virtuális gépen való telepítéséhez futtassa a következő parancsot. Szükség esetén módosítsa a *Hely* paramétert: 

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

3. Hozzon létre egy második virtuális gépet, és telepítse az IIS-t a korábban végrehajtott lépések végrehajtásával. Használja *a myVM2-t* a virtuális gép nevéhez és a **Set-AzVMExtension** parancsmag **VMName** beállításához.

### <a name="add-backend-servers-to-backend-pool"></a>Háttérkiszolgálók hozzáadása a háttérkészlethez

1. Az Azure Portal menüben válassza az **Összes erőforrás** lehetőséget, vagy keresse meg az *Összes erőforrás t.* Ezután válassza **a myAppGateway lehetőséget.**

2. Válassza a bal oldali menü **háttérkészletek parancsát.**

3. Válassza a **myBackendPool lehetőséget.**

4. A **Háttérrendszer-célok** **csoportban**válassza a **Virtuális gép** lehetőséget a legördülő listából.

5. A **Cél csoportban**válassza ki a **myVM** és **myVM2** virtuális gépeket és a hozzájuk kapcsolódó hálózati felületeket a legördülő listákból.


   > [!div class="mx-imgBorder"]
   > ![Háttérkiszolgálók hozzáadása](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Kattintson a **Mentés** gombra.

7. Várja meg, amíg a központi telepítés befejeződik, mielőtt továbblépne a következő lépésre.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Bár az IIS nem szükséges az alkalmazásátjáró létrehozásához, ebben a rövid útmutatóban telepítette, hogy ellenőrizze, hogy az Azure sikeresen létrehozta-e az alkalmazásátjárót. Az alkalmazásátjáró teszteléséhez használja az IIS-t:

1. Keresse meg az alkalmazásátjáró nyilvános IP-címét az **Áttekintés** lapon. ![Rekord alkalmazás átjáró](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) nyilvános IP-cím Vagy, akkor válassza ki **az Összes erőforrást**, írja *myAGPublicIPAddress* a keresőmezőbe, majd jelölje ki azt a keresési eredmények között. Az Azure megjeleníti a nyilvános IP-címet az **áttekintés** oldalon.
2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába az adott IP-cím böngészéséhez.
3. Ellenőrizze a választ. Egy érvényes válasz ellenőrzi, hogy az alkalmazásátjáró sikeresen létrejött-e, és sikeresen csatlakozhat-e a háttérrendszerhez.

   ![Az alkalmazásátjáró tesztelése](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Frissítse a böngészőt többször, és látnia kell a myVM és a myVM2 kapcsolatokat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az alkalmazásátjáróval létrehozott erőforrásokra, törölje az erőforráscsoportot. Az erőforráscsoport törlésekor az alkalmazásátjárót és az összes kapcsolódó erőforrást is eltávolítja.

Az erőforráscsoport törlése:

1. Az Azure Portal menüben válassza az **Erőforráscsoportok** lehetőséget, vagy keressen rá, és válassza *az Erőforráscsoportok lehetőséget.*
2. Az **Erőforráscsoportok** lapon keresse meg a **myResourceGroupAG** elemet a listában, majd jelölje ki azt.
3. Az **Erőforráscsoport lapon**válassza az **Erőforráscsoport törlése**lehetőséget.
4. Adja meg *a myResourceGroupAG* értéket **az ERŐFORRÁSCSOPORT NEVÉNEK BEÍRÁSához,** majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes forgalom kezelése Application Gatewayjel az Azure CLI segítségével](./tutorial-manage-web-traffic-cli.md)
