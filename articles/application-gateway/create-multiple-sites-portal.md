---
title: 'Oktatóanyag: Több webhelyet üzemeltet az Azure Portal használatával'
titleSuffix: Azure Application Gateway
description: Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy alkalmazásátjárót, amely több webhelyet üzemeltet az Azure Portal használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: victorh
ms.openlocfilehash: ca6be666a9b77532b4f1c61f6e3391c239e82c91
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74075153"
---
# <a name="tutorial-create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>Oktatóanyag: Hozzon létre és konfiguráljon egy alkalmazásátjárót több webhely üzemeltetéséhez az Azure Portalhasználatával

Az Azure Portal segítségével [konfigurálhatja több webhely üzemeltetését,](multiple-site-overview.md) amikor létrehoz egy [alkalmazásátjárót.](overview.md) Ebben az oktatóanyagban virtuális gépek használatával definiálja a háttércímkészleteket. Ezután az Ön tulajdonában lévő tartományok alapján konfigurálhat figyelőket és szabályokat a webes forgalom a készletekben lévő megfelelő kiszolgálókra irányításához. Ez az oktatóanyag feltételezi, hogy Ön több tartománnyal rendelkezik. Példaként a *www.contoso.com* és a *www.fabrikam.com* tartományt használja.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Application Gateway létrehozása
> * Virtuális gépek létrehozása háttérkiszolgálókhoz
> * Háttérkészletek létrehozása a háttérkiszolgálókkal
> * Háttérbeli figyelők létrehozása
> * Útválasztási szabályok létrehozása
> * CNAME rekord létrehozása a tartományban

![Többhelyes útválasztási példa](./media/create-multiple-sites-portal/scenario.png)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Bejelentkezés az Azure Portalra[https://portal.azure.com](https://portal.azure.com)

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

1. Válassza **az Erőforrás létrehozása lehetőséget** az Azure Portal bal oldali menüjében. Megjelenik **az Új** ablak.

2. Válassza **a Hálózat lehetőséget,** majd a **Kiemelt** listában válassza az **Alkalmazásátjáró** elemet.

### <a name="basics-tab"></a>Alapok lap

1. Az **Alapok lapon** adja meg ezeket az értékeket a következő alkalmazásátjáró-beállításokhoz:

   - **Erőforráscsoport**: Válassza ki a **myResourceGroupAG-t** az erőforráscsoporthoz. Ha nem létezik, válassza az **Új létrehozása** lehetőséget a létrehozáshoz.
   - **Alkalmazásátjáró neve**: Adja meg a *myAppGateway-t* az alkalmazásátjáró nevéhez.

     ![Új alkalmazásátjáró létrehozása: Alapok](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Ahhoz, hogy az Azure kommunikáljon a létrehozott erőforrások között, virtuális hálózatra van szüksége. Létrehozhat egy új virtuális hálózatot, vagy használhat egy meglévőt. Ebben a példában egy új virtuális hálózatot hoz létre az alkalmazásátjáró létrehozása kor. Az Alkalmazásátjáró-példányok külön alhálózatokban jönnek létre. Ebben a példában két alhálózatot hoz létre: egyet az alkalmazásátjáróhoz, egyet pedig a háttérkiszolgálókhoz.

    A **Virtuális hálózat konfigurálása**csoportban válassza az **Új létrehozása** új lehetőséget új virtuális hálózat létrehozásához. A **megnyíló Virtuális hálózat létrehozása** ablakban adja meg a következő értékeket a virtuális hálózat és két alhálózat létrehozásához:

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
   > Az Application Gateway v2 termékváltozat esetén csak **a Nyilvános** előtér IP-konfigurációt választhatja. A privát előtér-IP-konfiguráció jelenleg nincs engedélyezve ehhez a v2 termékváltozathoz.

2. Válassza az **Új létrehozása** a **nyilvános IP-címhez** lehetőséget, és írja be a *myAGPublicIPAddress értéket* a nyilvános IP-cím nevéhez, majd kattintson az OK **gombra.** 

     ![Új alkalmazásátjáró létrehozása: előtér-rendszerek](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Válassza a **Tovább: Háttérrendszerek lehetőséget.**

### <a name="backends-tab"></a>Háttérrendszerek lap

A háttérkészlet a kérelmek továbbítására szolgál a kérést kiszolgáló háttérkiszolgálókhoz. Háttérkészletek lehetnek hálózati adapterek, virtuálisgép-méretezési készletek, nyilvános IP-k, belső IP-k, teljesen minősített tartománynevek (FQDN) és több-bérlős háttér-rendszerek, mint az Azure App Service. Ebben a példában egy üres háttérkészletet hoz létre az alkalmazásátjáróval, majd háttércélokat ad hozzá a háttérkészlethez.

1. A **Háttérrendszerek** lapon válassza a **+Háttérkészlet hozzáadása**lehetőséget.

2. A **Megnyíló háttérkészlet hozzáadása** ablakban adja meg a következő értékeket egy üres háttérkészlet létrehozásához:

    - **Név**: Adja meg a *contosoPool* nevet a háttérkészlet nevéhez.
    - **Háttérkészlet hozzáadása célok nélkül:** Válassza az **Igen** lehetőséget egy cél nélküli háttérkészlet létrehozásához. Az alkalmazásátjáró létrehozása után háttércélokat fog hozzáadni.

3. A **Háttérkészlet hozzáadása** ablakban válassza a **Hozzáadás** lehetőséget a háttérkészlet konfigurációjának mentéséhez és a **háttérrendszerek** lapra való visszatéréshez.
4. Most adjunk hozzá egy másik háttérkészlet nevű *fabrikamPool*.

     ![Új alkalmazásátjáró létrehozása: háttérrendszerek](./media/create-multiple-sites-portal/backend-pools.png)

4. A **háttérrendszerek** lapon válassza a **Tovább: Konfiguráció**lehetőséget.

### <a name="configuration-tab"></a>Konfiguráció lap

A **Konfiguráció** lapon az útválasztási szabállyal létrehozott előtér- és háttérkészleteket fogja csatlakoztatni.

1. Válassza **a Szabály hozzáadása elemet** az **Útválasztási szabályok** oszlopban.

2. A **Megnyíló Útválasztási szabály hozzáadása** ablakban adja meg a szabály nevéhez a *contosoRule* **parancsot.**

3. Az útválasztási szabály hoz egy figyelő. Az **Útválasztási szabály hozzáadása** ablak **Figyelő** lapján adja meg a figyelő következő értékeit:

    - **Figyelő neve**: Adja meg *contosoListener* a neve a figyelő.
    - **Előtér IP:** **Válassza a Nyilvános** lehetőséget az előtérhez létrehozott nyilvános IP-cím kiválasztásához.

   A **További beállítások csoportban:**
   - **Figyelő típusa**: Több webhely
   - **Állomásnév**: **www.contoso.com**

   Fogadja el a **Figyelő** lap többi beállításának alapértelmezett értékeit, majd a **Háttérrendszer-célok** lapra a művelettervi szabály többi részének konfigurálásához.

   ![Új alkalmazásátjáró létrehozása: figyelő](./media/create-multiple-sites-portal/routing-rule.png)

4. A **háttérrendszer-célok** lapon válassza a **contosoPool** elemet a **háttérrendszer-célhoz.**

5. A **HTTP beállításhoz**válassza az **Új létrehozása lehetőséget** új HTTP-beállítás létrehozásához. A HTTP-beállítás határozza meg az útválasztási szabály viselkedését. A **Megnyíló HTTP-beállítás hozzáadása** ablakban adja meg a *CONTOSOHTTPSetting* parancsot a **HTTP-beállítás nevéhez.** Fogadja el a HTTP hozzáadása **beállításablak** egyéb beállításainak alapértelmezett értékeit, majd a **Hozzáadás gombra** a **Művelettervszabály hozzáadása** ablakhoz való visszatéréshez. 

6. A **Művelettervszabály hozzáadása** ablakban válassza a **Hozzáadás** lehetőséget a művelettervi szabály mentéséhez és a **Konfiguráció** lapra való visszatéréshez.
7. Válassza **a Szabály hozzáadása lehetőséget,** és adjon hozzá egy hasonló szabályt, figyelőt, háttércélt és HTTP-beállítást a Fabrikam hoz.

     ![Új alkalmazásátjáró létrehozása: útválasztási szabály](./media/create-multiple-sites-portal/fabrikamRule.png)

7. Válassza a **Tovább: Címkék,** majd **a Tovább: Véleményezés + létrehozás**lehetőséget.

### <a name="review--create-tab"></a>Véleményezés + létrehozás lap

Tekintse át a Beállításokat a **Véleményezés + létrehozás** lapon, majd válassza a **Létrehozás** gombot a virtuális hálózat, a nyilvános IP-cím és az alkalmazásátjáró létrehozásához. Az Azure-nak az alkalmazásátjáró létrehozása több percig is eltarthat.

Várja meg, amíg a központi telepítés sikeresen befejeződik, mielőtt továbblépne a következő szakaszra.

## <a name="add-backend-targets"></a>Háttér-célok hozzáadása

Ebben a példában a virtuális gépeket fogja használni a cél háttérrendszerként. Használhatja a meglévő virtuális gépeket, vagy újakat hozhat létre. Két virtuális gépet hoz létre, amelyeket az Azure háttérkiszolgálóként használ az alkalmazásátjáróhoz.

Háttércélok hozzáadásához a következőkre van szükség:

1. Hozzon létre két új virtuális gépet, *contosoVM* és *fabrikamVM*, háttérkiszolgálóként használandó.
2. Telepítse az IIS-t a virtuális gépekre az alkalmazásátjáró sikeres létrehozásának ellenőrzéséhez.
3. Adja hozzá a háttérkiszolgálókat a háttérkészletekhez.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Az Azure Portalon válassza az **Erőforrás létrehozása lehetőséget.** Megjelenik **az Új** ablak.
2. Válassza **a Számítási lehetőséget,** majd a **Népszerű** listában a Windows **Server 2016 Datacenter** elemet. Megjelenik **a Virtuálisgép létrehozása** lap.<br>Az Application Gateway a háttérkészletében használt bármilyen típusú virtuális gépre irányíthatja a forgalmat. Ebben a példában Windows Server 2016 Datacenter t használ.
3. Adja meg ezeket az értékeket az **Alapok** lapon a következő virtuális gépbeállításokhoz:

    - **Erőforráscsoport**: Válassza ki a **myResourceGroupAG** elemet az erőforráscsoport nevéhez.
    - **Virtuálisgép neve**: Adja meg *a contosoVM* nevet a virtuális gép nevéhez.
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

    ![Egyéni bővítmény telepítése](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Futtassa a következő parancsot az IIS a virtuális gépen való telepítéséhez: 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Hozzon létre egy második virtuális gépet, és telepítse az IIS-t a korábban végrehajtott lépések segítségével. Használja *a fabrikamVM-et* a virtuális gép nevéhez és a **Set-AzVMExtension** parancsmag **VMName** beállításához.

### <a name="add-backend-servers-to-backend-pools"></a>Háttérkiszolgálók hozzáadása háttérkészletekhez

1. Válassza **az Összes erőforrás**lehetőséget, majd a **myAppGateway**lehetőséget.

2. Válassza a bal oldali menü **háttérkészletek parancsát.**

3. Válassza **a contosoPool lehetőséget.**

4. A **Célok csoportban**válassza a **Virtuális gép** lehetőséget a legördülő listából.

5. A **VIRTUÁLIS GÉP** és a HÁLÓZATI **ADAPTEREK csoportban**válassza ki a **contosoVM** virtuális gépet, és a legördülő listából a hozzá tartozó hálózati felületet válassza ki.

    ![Háttérkiszolgálók hozzáadása](./media/create-multiple-sites-portal/edit-backend-pool.png)

6. Kattintson a **Mentés** gombra.
7. A *fabrikamVM* és a felület hozzáadásához ismételje meg a műveletet a *fabrikamPool hoz.*

Várja meg, amíg a központi telepítés befejeződik, mielőtt továbblépne a következő lépésre.

## <a name="create-a-www-a-record-in-your-domains"></a>Www A rekord létrehozása a tartományokban

Miután az alkalmazásátjáró nyilvános IP-címmel jött létre, lekaphatja az IP-címet, és a tartományaiban a segítségével létrehozhat egy A rekordot. 

1. Kattintson **az Összes erőforrás**elemre, majd a **myAGPublicIPAddress parancsra.**

    ![Alkalmazásátjáró DNS-címének rögzítése](./media/create-multiple-sites-portal/public-ip.png)

2. Másolja az IP-címet, és használja azt egy új *www* A rekord értékeként a tartományaiban.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

1. Adja meg a tartománya nevét a böngésző címsorában. Például: `http://www.contoso.com`.

    ![Contoso webhely tesztelése az alkalmazásátjáróban](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. Változtassa meg a címet a másik tartományára. Ekkor az eredmény a következő példához hasonló:

    ![Fabrikam webhely tesztelése az alkalmazásátjáróban](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az alkalmazásátjáróval létrehozott erőforrásokra, távolítsa el az erőforráscsoportot. Az erőforráscsoport eltávolításakor az alkalmazásátjárót és annak összes kapcsolódó erőforrását is eltávolítja.

Az erőforráscsoport eltávolítása:

1. Az Azure Portal bal oldali menüjében válassza az **Erőforráscsoportok**lehetőséget.
2. Az **Erőforráscsoportok** lapon keresse meg a **myResourceGroupAG** elemet a listában, majd jelölje ki azt.
3. Az **Erőforráscsoport lapon**válassza az **Erőforráscsoport törlése**lehetőséget.
4. Adja meg *a myResourceGroupAG* értéket **az ERŐFORRÁSCSOPORT NEVÉNEK BEÍRÁSához,** majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ arról, hogy mit tehet az Azure Application Gateway használatával](application-gateway-introduction.md)