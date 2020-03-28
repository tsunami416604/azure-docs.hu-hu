---
title: 'Oktatóanyag: Létrehozás portál használatával – Webalkalmazás-tűzfal'
description: Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy alkalmazásátjárót egy webalkalmazás-tűzfallal az Azure Portal használatával.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 68a9f051bf3d59cbf32377cb503e9ded0a54d5e9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74049223"
---
# <a name="tutorial-create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Oktatóanyag: Alkalmazásátjáró létrehozása webalkalmazás-tűzfallal az Azure Portal használatával

Ez az oktatóanyag bemutatja, hogyan hozhat létre alkalmazásátjárót webalkalmazás-tűzfallal (WAF) az Azure Portal használatával. A WAF [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project)-szabályokkal védi az alkalmazást. Ezek a szabályok olyan támadások ellen nyújtanak védelmet, mint az SQL-injektálás, a Cross-Site Scripting támadások és a munkamenet-eltérítések. Az alkalmazásátjáró létrehozása után tesztelje, hogy megfelelően működik-e. Az Azure Application Gateway segítségével az alkalmazás webes forgalmát adott erőforrásokhoz irányítja azáltal, hogy figyelőkhöz rendeli a portokat, szabályokat hoz létre, és erőforrásokat ad hozzá egy háttérkészlethez. Az egyszerűség kedvéért ez az oktatóanyag egy egyszerű beállítást használ egy nyilvános előtér-IP-cím, egy alapvető figyelő egyetlen hely üzemeltetéséhez ezen az alkalmazásátjárón, két virtuális gépet a háttérkészlethez, és egy alapvető kérelem-útválasztási szabályt.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Alkalmazásátjáró létrehozása engedélyezett WAF-fel
> * Háttérkiszolgálóként használt virtuális gépek létrehozása
> * Tárfiók létrehozása és diagnosztika konfigurálása
> * Az alkalmazásátjáró tesztelése

![Példa webalkalmazási tűzfalra](../media/application-gateway-web-application-firewall-portal/scenario-waf.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<!---If you prefer, you can complete this tutorial using [Azure PowerShell](tutorial-restrict-web-traffic-powershell.md) or [Azure CLI](tutorial-restrict-web-traffic-cli.md).--->

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Ahhoz, hogy az Azure kommunikáljon az erőforrások között, virtuális hálózatra van szüksége. Létrehozhat egy új virtuális hálózatot, vagy használhat egy meglévőt. Ebben a példában új virtuális hálózatot hoz létre. Virtuális hálózatot az alkalmazásátjáróval együtt is létrehozhat. Az Alkalmazásátjáró-példányok külön alhálózatokban jönnek létre. Ebben a példában két alhálózatot hoz létre: egyet az alkalmazásátjáróhoz, egyet pedig a háttérkiszolgálókhoz.

Válassza **az Erőforrás létrehozása lehetőséget** az Azure Portal bal oldali menüjében. Megjelenik **az Új** ablak.

Válassza **a Hálózat lehetőséget,** majd a **Kiemelt** listában válassza az **Alkalmazásátjáró** elemet.

### <a name="basics-tab"></a>Alapok lap

1. Az **Alapok lapon** adja meg ezeket az értékeket a következő alkalmazásátjáró-beállításokhoz:

   - **Erőforráscsoport**: Válassza ki a **myResourceGroupAG-t** az erőforráscsoporthoz. Ha nem létezik, válassza az **Új létrehozása** lehetőséget a létrehozáshoz.
   - **Alkalmazásátjáró neve**: Adja meg a *myAppGateway-t* az alkalmazásátjáró nevéhez.
   - **Tier**: válassza **a WAF V2**lehetőséget.

     ![Új alkalmazásátjáró létrehozása: Alapok](../media/application-gateway-web-application-firewall-portal/application-gateway-create-basics.png)

2.  Ahhoz, hogy az Azure kommunikáljon a létrehozott erőforrások között, virtuális hálózatra van szüksége. Létrehozhat egy új virtuális hálózatot, vagy használhat egy meglévőt. Ebben a példában egy új virtuális hálózatot hoz létre az alkalmazásátjáró létrehozása kor. Az Alkalmazásátjáró-példányok külön alhálózatokban jönnek létre. Ebben a példában két alhálózatot hoz létre: egyet az alkalmazásátjáróhoz, egyet pedig a háttérkiszolgálókhoz.

    A **Virtuális hálózat konfigurálása**csoportban válasszuk az Új létrehozása lehetőséget, és válassza az **Új létrehozása**lehetőséget. A **megnyíló Virtuális hálózat létrehozása** ablakban adja meg a következő értékeket a virtuális hálózat és két alhálózat létrehozásához:

    - **Név**: Adja meg a *myVNet* nevet a virtuális hálózat nevéhez.

    - **Alhálózat neve** (Application Gateway alhálózat): Az **Alhálózatok** rácsegy *Alapértelmezett*nevű alhálózatot jelenít meg. Módosítsa az alhálózat nevét *myAGSubnet*névre.<br>Az alkalmazásátjáró alhálózata csak alkalmazásátjárókat tartalmazhat. Más erőforrások nem engedélyezettek.

    - **Alhálózat neve** (háttérkiszolgáló-alhálózat): Az **Alhálózatok** rács második sorában adja meg a *myBackendSubnet* értéket az **Alhálózat név** oszlopában.

    - **Címtartomány** (háttérkiszolgáló-alhálózat): Az **Alhálózatok** hálózatának második sorában adjon meg egy olyan címtartományt, amely nem fedi át a *myAGSubnet*címtartományát. Ha például a *myAGSubnet* címtartománya 10.0.0.0/24, írja be a *10.0.1.0/24* értéket a *myBackendSubnet*címtartományába.

    A **Virtuális hálózat létrehozása** ablak bezárásához és a virtuális hálózati beállítások mentéséhez válassza az OK **gombot.**

     ![Új alkalmazásátjáró létrehozása: virtuális hálózat](../media/application-gateway-web-application-firewall-portal/application-gateway-create-vnet.png)
    
3. Az **Alapok** lapon fogadja el a többi beállítás alapértelmezett értékeit, majd válassza a **Tovább: Előtér -** lehetőséget.

### <a name="frontends-tab"></a>Előtér-lapok lap

1. Az **Előtér-szolgáltatások** lapon ellenőrizze, hogy az **előtér IP-címtípusa** **Nyilvános.** <br>Beállíthatja, hogy az előtér-IP-cím nyilvános vagy privát legyen a használati esetnek hasonlóan. Ebben a példában egy nyilvános előtér-IP-címet választ.
   > [!NOTE]
   > Az Application Gateway v2 termékváltozat esetén csak **a Nyilvános** előtér IP-konfigurációt választhatja. A privát előtér-IP-konfiguráció jelenleg nincs engedélyezve ehhez a v2 termékváltozathoz.

2. Válassza az **Új létrehozása** a **nyilvános IP-címhez** lehetőséget, és írja be a *myAGPublicIPAddress értéket* a nyilvános IP-cím nevéhez, majd kattintson az OK **gombra.** 

     ![Új alkalmazásátjáró létrehozása: előtér-rendszerek](../media/application-gateway-web-application-firewall-portal/application-gateway-create-frontends.png)

3. Válassza a **Tovább: Háttérrendszerek lehetőséget.**

### <a name="backends-tab"></a>Háttérrendszerek lap

A háttérkészlet a kérelmek továbbítására szolgál a kérést kiszolgáló háttérkiszolgálókhoz. Háttérkészletek állhat nicc-ek, virtuálisgép-méretezési készletek, nyilvános IP-k, belső IP-k, teljesen minősített tartománynevek (FQDN) és több-bérlős háttér-rendszerek, mint az Azure App Service. Ebben a példában egy üres háttérkészletet hoz létre az alkalmazásátjáróval, majd háttércélokat ad hozzá a háttérkészlethez.

1. A **Háttérrendszerek** lapon válassza a **+Háttérkészlet hozzáadása**lehetőséget.

2. A **Megnyíló háttérkészlet hozzáadása** ablakban adja meg a következő értékeket egy üres háttérkészlet létrehozásához:

    - **Név**: Adja meg a *sajátbackendPool* nevet a háttérkészlet nevéhez.
    - **Háttérkészlet hozzáadása célok nélkül:** Válassza az **Igen** lehetőséget egy cél nélküli háttérkészlet létrehozásához. Az alkalmazásátjáró létrehozása után háttércélokat fog hozzáadni.

3. A **Háttérkészlet hozzáadása** ablakban válassza a **Hozzáadás** lehetőséget a háttérkészlet konfigurációjának mentéséhez és a **háttérrendszerek** lapra való visszatéréshez.

     ![Új alkalmazásátjáró létrehozása: háttérrendszerek](../media/application-gateway-web-application-firewall-portal/application-gateway-create-backends.png)

4. A **háttérrendszerek** lapon válassza a **Tovább: Konfiguráció**lehetőséget.

### <a name="configuration-tab"></a>Konfiguráció lap

A **Konfiguráció** lapon az útválasztási szabállyal létrehozott előtér- és háttérkészletet fogja csatlakoztatni.

1. Válassza **a Szabály hozzáadása elemet** az **Útválasztási szabályok** oszlopban.

2. A **Megnyíló Útválasztási szabály hozzáadása** ablakban adja meg a szabály nevéhez a *myRoutingRule* **parancsot.**

3. Az útválasztási szabály hoz egy figyelő. Az **Útválasztási szabály hozzáadása** ablak **Figyelő** lapján adja meg a figyelő következő értékeit:

    - **Figyelő neve**: Adja meg *a myListener* nevet a figyelő.
    - **Előtér IP:** **Válassza a Nyilvános** lehetőséget az előtérhez létrehozott nyilvános IP-cím kiválasztásához.
  
      Fogadja el a **Figyelő** lap többi beállításának alapértelmezett értékeit, majd a **Háttérrendszer-célok** lapra a művelettervi szabály többi részének konfigurálásához.

   ![Új alkalmazásátjáró létrehozása: figyelő](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-listener.png)

4. A **háttérrendszer-célok** lapon válassza a **myBackendPool** elemet a **háttér-célhoz.**

5. A **HTTP beállításhoz**válassza az **Új létrehozása lehetőséget** új HTTP-beállítás létrehozásához. A HTTP-beállítás határozza meg az útválasztási szabály viselkedését. A **Megnyíló HTTP-beállítás hozzáadása** ablakban adja meg a *myHTTPSetting* parancsot a **HTTP-beállítás nevéhez**. Fogadja el a HTTP hozzáadása **beállításablak** egyéb beállításainak alapértelmezett értékeit, majd a **Hozzáadás gombra** a **Művelettervszabály hozzáadása** ablakhoz való visszatéréshez. 

     ![Új alkalmazásátjáró létrehozása: HTTP-beállítás](../media/application-gateway-web-application-firewall-portal/application-gateway-create-httpsetting.png)

6. A **Művelettervszabály hozzáadása** ablakban válassza a **Hozzáadás** lehetőséget a művelettervi szabály mentéséhez és a **Konfiguráció** lapra való visszatéréshez.

     ![Új alkalmazásátjáró létrehozása: útválasztási szabály](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-backends.png)

7. Válassza a **Tovább: Címkék,** majd **a Tovább: Véleményezés + létrehozás**lehetőséget.

### <a name="review--create-tab"></a>Véleményezés + létrehozás lap

Tekintse át a Beállításokat a **Véleményezés + létrehozás** lapon, majd válassza a **Létrehozás** gombot a virtuális hálózat, a nyilvános IP-cím és az alkalmazásátjáró létrehozásához. Az Azure-nak az alkalmazásátjáró létrehozása több percig is eltarthat. 

Várja meg, amíg a központi telepítés sikeresen befejeződik, mielőtt továbblépne a következő szakaszra.

## <a name="add-backend-targets"></a>Háttér-célok hozzáadása

Ebben a példában a virtuális gépeket fogja használni a cél háttérrendszerként. Használhatja a meglévő virtuális gépeket, vagy újakat hozhat létre. Két virtuális gépet hoz létre, amelyeket az Azure háttérkiszolgálóként használ az alkalmazásátjáróhoz.

Ehhez a következőkre van szüksége:

1. Hozzon létre két új virtuális gépet, *myVM-et* és *myVM2-t,* amelyek háttérkiszolgálóként használhatók.
2. Telepítse az IIS-t a virtuális gépekre az alkalmazásátjáró sikeres létrehozásának ellenőrzéséhez.
3. Adja hozzá a háttérkiszolgálókat a háttérkészlethez.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Az Azure Portalon válassza az **Erőforrás létrehozása lehetőséget.** Megjelenik **az Új** ablak.
2. Válassza a **Windows Server 2016 Datacenter elemet** a **Népszerű** listában. Megjelenik **a Virtuálisgép létrehozása** lap.<br>Az Application Gateway a háttérkészletében használt bármilyen típusú virtuális gépre irányíthatja a forgalmat. Ebben a példában Windows Server 2016 Datacenter t használ.
3. Adja meg ezeket az értékeket az **Alapok** lapon a következő virtuális gépbeállításokhoz:

    - **Erőforráscsoport**: Válassza ki a **myResourceGroupAG** elemet az erőforráscsoport nevéhez.
    - **Virtuális gép neve**: Adja meg a *virtuális* gép nevét.
    - **Felhasználónév**: Adja meg *az azureuser* t a rendszergazdai felhasználónévhez.
    - **Jelszó**: Írja be *az Azure123456!* a rendszergazdai jelszót.
4. Fogadja el a többi alapértelmezést, majd válassza **a Tovább: Lemezek**lehetőséget.  
5. Fogadja el a **Lemezek** lap alapértelmezett beállításait, és válassza **a Tovább: Hálózat lehetőséget.**
6. A **Hálózat** lapon ellenőrizze, hogy a **myVNet** ki van-e jelölve a **virtuális hálózathoz,** és az **alhálózat** beállítása **myBackendSubnet**. Fogadja el a többi alapértelmezést, majd válassza **a Tovább: Kezelés**lehetőséget.<br>Az Application Gateway képes kommunikálni a virtuális hálózaton kívüli példányokkal, de biztosítania kell az IP-kapcsolatot.
7. A **Kezelés** lapon állítsa a **Rendszerindításdia imát** **Ki**beállításra. Fogadja el a többi alapértelmezést, majd válassza **a Véleményezés + létrehozás**lehetőséget.
8. A **Véleményezés + Létrehozás** lapon tekintse át a beállításokat, javítsa ki az érvényesítési hibákat, és válassza a **Létrehozás gombot.**
9. A folytatás előtt várja meg, amíg befejeződik a virtuális gép létrehozása.

### <a name="install-iis-for-testing"></a>Az IIS telepítése tesztelésre

Ebben a példában csak az Azure sikeres létrehozásához telepítse az IIS-t a virtuális gépekre.

1. Nyissa meg [az Azure PowerShellt.](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell) Ehhez válassza a **Cloud Shell** az Azure Portal felső navigációs sávján, majd válassza a **PowerShell** a legördülő listából. 

    ![Egyéni bővítmény telepítése](../media/application-gateway-web-application-firewall-portal/application-gateway-extension.png)

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

3. Hozzon létre egy második virtuális gépet, és telepítse az IIS-t a korábban végrehajtott lépések végrehajtásával. Használja *a myVM2-t* a virtuális gép nevéhez és a **Set-AzVMExtension** parancsmag **VMName** beállításához.

### <a name="add-backend-servers-to-backend-pool"></a>Háttérkiszolgálók hozzáadása a háttérkészlethez

1. Válassza **az Összes erőforrás**lehetőséget, majd a **myAppGateway**lehetőséget.

2. Válassza a bal oldali menü **háttérkészletek parancsát.**

3. Válassza a **myBackendPool lehetőséget.**

4. A **Célok csoportban**válassza a **Virtuális gép** lehetőséget a legördülő listából.

5. A **VIRTUÁLIS GÉP** és a HÁLÓZATI **ADAPTEREK**csoportban válassza ki a **myVM** és **myVM2** virtuális gépeket és a hozzájuk kapcsolódó hálózati interfészeket a legördülő listából.

    ![Háttérkiszolgálók hozzáadása](../media/application-gateway-web-application-firewall-portal/application-gateway-backend.png)

6. Kattintson a **Mentés** gombra.

7. Várja meg, amíg a központi telepítés befejeződik, mielőtt továbblépne a következő lépésre.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Tárfiók létrehozása és diagnosztika konfigurálása

### <a name="create-a-storage-account"></a>Create a storage account

Ebben a cikkben az alkalmazásátjáró egy tárfiókot használ az adatok tárolására észlelési és megelőzési célokra. Az Azure Monitor naplóivagy az Event Hub használatával is rögzítheti az adatokat.

1. Válassza az **Erőforrás létrehozása** lehetőséget az Azure Portal bal felső sarkában.
1. Válassza a Storage ( **Tárolás**) lehetőséget, majd **a Tárfióko lehetőséget.**
1. Az *Erőforráscsoport csoporthoz*válassza a **myResourceGroupAG** lehetőséget az erőforráscsoporthoz.
1. Írja be a *myagstore1* nevet a tárfiók nevéhez.
1. Fogadja el a többi beállítás alapértelmezett értékeit, majd válassza a **Véleményezés + Létrehozás**lehetőséget.
1. Tekintse át a beállításokat, és válassza a **Létrehozás gombot.**

### <a name="configure-diagnostics"></a>Diagnosztika konfigurálása

Konfigurálja a diagnosztikát az adatok az ApplicationGatewayAccessLog, az ApplicationGatewayPerformanceLog és az ApplicationGatewayFirewallLog naplóba rögzítéséhez.

1. A bal oldali menüben válassza az **Összes erőforrás**lehetőséget, majd válassza a *myAppGateway*parancsot.
2. A Figyelés csoportban válassza a **Diagnosztikai beállítások lehetőséget.**
3. válassza **a Diagnosztikai beállítások hozzáadása lehetőséget.**
4. Adja meg *a myDiagnosticsSettings* parancsot a diagnosztikai beállítások neveként.
5. Válassza **az Archiválás tárfiókba**lehetőséget, majd a **Konfigurálás** lehetőséget a korábban létrehozott *myagstore1* tárfiók kiválasztásához, majd az **OK**gombra.
6. Válassza ki az összegyűjtő és megtartandó alkalmazásátjáró-naplókat.
7. Kattintson a **Mentés** gombra.

    ![Diagnosztika konfigurálása](../media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="create-and-link-a-web-application-firewall-policy"></a>Webalkalmazás-tűzfal házirend létrehozása és csatolása

A WAF összes testreszabása és beállítása egy külön objektumban, az úgynevezett WAF-házirendben található. A házirendet az Alkalmazásátjáróhoz kell társtársosan kezelni. WAF-házirend létrehozásáról a [WAF-házirend létrehozása című témakörben.](create-waf-policy-ag.md) Létrehozása után a szabályzatot társíthatja a WAF-hez (vagy egy egyéni figyelőhöz) a WAF-szabályzatból a **Társított alkalmazásátjárók** lapon. 

![Társított alkalmazásátjárók](../media/application-gateway-web-application-firewall-portal/associated-application-gateways.png)

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Bár az IIS nem szükséges az alkalmazásátjáró létrehozásához, telepítette azt annak ellenőrzésére, hogy az Azure sikeresen létrehozta-e az alkalmazásátjárót. Az alkalmazásátjáró teszteléséhez használja az IIS-t:

1. Keresse meg az alkalmazásátjáró nyilvános IP-címét az **Áttekintés** lapon. ![Alkalmazásátjáró nyilvános IP-címének rögzítése](../media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png) 

   Vagy válassza ki **az Összes erőforrást**, írja be a *myAGPublicIPAddress kifejezést* a keresőmezőbe, majd jelölje ki a keresési eredmények között. Az Azure megjeleníti a nyilvános IP-címet az **áttekintés** oldalon.
1. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.
1. Ellenőrizze a választ. Egy érvényes válasz ellenőrzi, hogy az alkalmazásátjáró sikeresen létrejött-e, és sikeresen csatlakozhat a háttérrendszerhez.

   ![Az alkalmazásátjáró tesztelése](../media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az alkalmazásátjáróval létrehozott erőforrásokra, távolítsa el az erőforráscsoportot. Az erőforráscsoport eltávolításával eltávolítja az alkalmazásátjárót és annak összes kapcsolódó erőforrását is. 

Az erőforráscsoport eltávolítása:

1. Az Azure Portal bal oldali menüjében válassza az **Erőforráscsoportok**lehetőséget.
2. Az **Erőforráscsoportok** lapon keresse meg a **myResourceGroupAG** elemet a listában, majd jelölje ki azt.
3. Az **Erőforráscsoport lapon**válassza az **Erőforráscsoport törlése**lehetőséget.
4. Írja be *a myResourceGroupAG* értéket **az ERŐFORRÁSCSOPORT NEVÉNEK BEÍRÁSához,** majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a webalkalmazás-tűzfalról](../overview.md)
