---
title: Oktatóanyag – alkalmazás-átjáró létrehozása URL-alapú útválasztási szabályokkal – Azure Portal
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre URL-elérésiút-alapú útválasztási szabályokat egy Application Gateway és virtuálisgép-méretezési csoport számára a Azure Portal használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: victorh
ms.openlocfilehash: 63a1faa79374e72eabfbee4ece454728c3b4cc05
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597598"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Oktatóanyag: Elérésiút-alapú útválasztási szabályokkal rendelkező Application Gateway létrehozása a Azure Portal használatával

Az [alkalmazás-átjáró](overview.md)létrehozásakor a Azure Portal az [URL-elérésiút-alapú útválasztási szabályok](url-route-overview.md) konfigurálására használható. Ebben az oktatóanyagban a háttér-készleteket virtuális gépek használatával hozza létre. Ezután olyan útválasztási szabályokat hozhat létre, amelyek gondoskodnak arról, hogy a webes forgalom a készletekben lévő megfelelő kiszolgálókon érkezzen.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Application Gateway létrehozása
> * Virtuális gépek létrehozása háttérbeli kiszolgálókhoz
> * Háttér-készletek létrehozása a háttér-kiszolgálókkal
> * Háttér-figyelő létrehozása
> * Elérésiút-alapú útválasztási szabály létrehozása

![URL-útválasztási példa](./media/create-url-route-portal/scenario.png)

Ha szeretné, elvégezheti az oktatóanyagot az [Azure CLI](tutorial-url-route-cli.md) vagy a [Azure PowerShell](tutorial-url-route-powershell.md)használatával.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

1. Válassza az **erőforrás létrehozása** elemet a Azure Portal bal oldali menüjében. Megjelenik az **új** ablak.

2. Válassza a **hálózatkezelés** lehetőséget, majd a **Kiemelt** listában válassza a **Application Gateway** lehetőséget.

### <a name="basics-tab"></a>Alapbeállítások lap

1. Az **alapok** lapon adja meg a következő Application Gateway-beállításokhoz tartozó értékeket:

   - **Erőforráscsoport**: Válassza ki az erőforráscsoport **myResourceGroupAG** . Ha nem létezik, válassza az **új létrehozása** elemet a létrehozásához.
   - **Application Gateway neve**: Adja meg a *myAppGateway* nevet az Application Gateway neveként.

     ![Új Application Gateway létrehozása: Alapvető beállítások](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Ahhoz, hogy az Azure kommunikáljon a létrehozott erőforrások között, szüksége van egy virtuális hálózatra. Hozzon létre egy új virtuális hálózatot, vagy használjon egy meglévőt. Ebben a példában egy új virtuális hálózatot fog létrehozni az Application Gateway létrehozásakor. Application Gateway példányok külön alhálózatokban jönnek létre. Ebben a példában két alhálózatot hoz létre: egyet az Application Gateway számára, és egy másikat a háttér-kiszolgálók számára.

    A **virtuális hálózat konfigurálása**területen válassza az **új létrehozása** lehetőséget az új virtuális hálózat létrehozásához. A megnyíló **virtuális hálózat létrehozása** ablakban adja meg a következő értékeket a virtuális hálózat és két alhálózat létrehozásához:

    - **Név**: Adja meg a virtuális hálózat nevének *myVNet* .

    - **Alhálózat neve** (Application Gateway alhálózat): Az  alhálózatok rácsa az *alapértelmezett*nevű alhálózatot jeleníti meg. Módosítsa az alhálózat nevét a *myAGSubnet*értékre.

      Az Application Gateway-alhálózat csak Application Gateway átjárókat tartalmazhat. Más erőforrások nem engedélyezettek.

    - **Alhálózat neve** (háttér-kiszolgáló alhálózata): Az alhálózatok rácsának  második sorában írja be a *MyBackendSubnet* **nevet az alhálózat neve** oszlopba.

    - **Címtartomány** (háttér-kiszolgáló alhálózata): Az alhálózatok rácsának  második sorában adjon meg egy címtartományt, amely nem fedi át a *myAGSubnet*címtartományt. Ha például a *myAGSubnet* 10.0.0.0/24, a *10.0.1.0/24* értéket adja meg a *myBackendSubnet*-tartományhoz.

    A **virtuális hálózat létrehozása** ablak bezárásához és a virtuális hálózat beállításainak mentéséhez kattintson **az OK gombra** .

     ![Új Application Gateway létrehozása: virtuális hálózat](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Az **alapvető** beállítások lapon fogadja el a többi beállítás alapértelmezett értékeit, majd válassza **a Next (tovább) gombot: Frontendek**.

### <a name="frontends-tab"></a>Frontendek lap

1. A **frontendek** lapon ellenőrizze, hogy a előtérbeli **IP-cím típusa** **nyilvános**értékre van-e állítva. <br>Az előtérbeli IP-címet úgy állíthatja be, hogy a használati esetnek megfelelően nyilvános vagy privát legyen. Ebben a példában egy nyilvános előtérbeli IP-címet választ.
   > [!NOTE]
   > A Application Gateway v2 SKU esetében csak **nyilvános** ELŐTÉRBELI IP-konfigurációt választhat. A privát előtérbeli IP-konfiguráció jelenleg nincs engedélyezve ehhez a v2 SKU-hoz.

2. Válassza a **nyilvános IP-cím** **új létrehozása** lehetőséget, és adja meg a *myAGPublicIPAddress* a nyilvános IP-cím neveként, majd kattintson **az OK gombra**. 

     ![Új Application Gateway létrehozása: frontendek](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Válassza **a Next (tovább) lehetőséget: Háttérrendszer**.

### <a name="backends-tab"></a>Háttérrendszer lap

A háttér-készlet arra szolgál, hogy a kérelmeket a kérést kiszolgáló háttér-kiszolgálókra irányítsa. A háttér-készletek lehetnek hálózati adapterek, virtuálisgép-méretezési csoportok, nyilvános IP-címek, belső IP-címek, teljes tartománynevek (FQDN) és több-bérlős háttérrendszer, például Azure App Service. Ebben a példában egy üres háttér-készletet hozunk létre az Application Gateway használatával, majd a háttérbeli célokat hozzá kell adni a háttér-készlethez.

1. A **háttérrendszer** lapon válassza a **+ háttér-készlet hozzáadása**elemet.

2. A megnyíló **háttérbeli készlet hozzáadása** ablakban adja meg a következő értékeket egy üres háttérbeli készlet létrehozásához:

    - **Név**: Adja meg a *appGatewayBackendPool* nevét a háttérbeli készlet neveként.
    - **Háttérbeli készlet hozzáadása célok nélkül**: Válassza az **Igen** lehetőséget, ha célokat nem tartalmazó háttér-készletet szeretne létrehozni. Az Application Gateway létrehozása után hozzá kell adni a háttérbeli célokat.

3. A háttérbeli **készlet hozzáadása** ablakban válassza a **Hozzáadás** elemet a háttérbeli készlet konfigurációjának mentéséhez, és térjen  vissza a háttérrendszer lapra.
4. Most adjon hozzá még két háttér-készletet, a *imagesBackendPool*és a *videoBackendPool*.

     ![Új Application Gateway létrehozása: háttérrendszer](./media/create-url-route-portal/backends.png)

4. A backends (háttérrendszer) **lapon válassza a Next (tovább) gombot:  Konfiguráció**.

### <a name="configuration-tab"></a>Konfiguráció lap

A **konfiguráció** lapon összekapcsolja az útválasztási szabályok használatával létrehozott előtér-és háttér-készleteket.

1. Válassza a **szabály hozzáadása** lehetőséget az **útválasztási szabályok** oszlopban.
2. A megnyíló **útválasztási szabály hozzáadása** ablakban írja be a *Rule1* nevet a **szabály neveként**.
3. Egy útválasztási szabályhoz egy figyelő szükséges. Az **útválasztási szabály hozzáadása** ablak **figyelő** lapján adja meg az alábbi értékeket a figyelőhöz:

    - **Figyelő neve**: Adja meg a *DefaultListener* a figyelő neveként.
    - Előtéri **IP-cím**: Válassza a **nyilvános** lehetőséget a előtérhez létrehozott nyilvános IP-cím kiválasztásához.

   Fogadja el az alapértelmezett értékeket a **figyelő** lapon a többi beállításnál, majd válassza a **háttérbeli célok** fület a többi útválasztási szabály konfigurálásához.
4. A **háttérbeli célok** lapon válassza a **AppGatewayBackendPool** lehetőséget a **háttérbeli célként**.

5. A **http**-beállításnál válassza az **új létrehozása** lehetőséget egy új http-beállítás létrehozásához. A HTTP-beállítás határozza meg az útválasztási szabály viselkedését. A megnyíló **http-beállítás hozzáadása** ablakban írja be a *MyHTTPSetting* nevet a **http-beállítás neveként**. Fogadja el az alapértelmezett értékeket a további beállításokhoz a **http-beállítás hozzáadása** ablakban, majd válassza a **Hozzáadás** lehetőséget az **útválasztási szabály hozzáadása** ablakhoz való visszatéréshez. 

6. Az útválasztási szabály **hozzáadása** ablakban válassza a **Hozzáadás** lehetőséget az útválasztási szabály mentéséhez és a **konfiguráció** laphoz való visszatéréshez.



1. Válassza a **szabály hozzáadása** lehetőséget az **útválasztási szabályok** oszlopban.

2. A megnyíló **útválasztási szabály hozzáadása** ablakban írja be a *Rule2* nevet a **szabály neveként**.

3. Egy útválasztási szabályhoz egy figyelő szükséges. Az **útválasztási szabály hozzáadása** ablak **figyelő** lapján adja meg az alábbi értékeket a figyelőhöz:

    - **Figyelő neve**: Adja meg a *myBackendListener* a figyelő neveként.
    - Előtéri **IP-cím**: Válassza a **nyilvános** lehetőséget a előtérhez létrehozott nyilvános IP-cím kiválasztásához.
    - **Port**: 8080

   A **További beállítások**területen:
   - **Figyelő típusa**: Alapszintű

   Fogadja el az alapértelmezett értékeket a **figyelő** lapon a többi beállításnál, majd válassza a **háttérbeli célok** fület a többi útválasztási szabály konfigurálásához.

4. A **háttérbeli célok** lapon válassza a **AppGatewayBackendPool** lehetőséget a **háttérbeli célként**.

5. A **http**-beállításnál válassza a *myHTTPSetting*lehetőséget. Fogadja el az alapértelmezett értékeket a további beállításokhoz a **http-beállítás hozzáadása** ablakban, majd válassza a **Hozzáadás** lehetőséget az **útválasztási szabály hozzáadása** ablakhoz való visszatéréshez. 

1. Az **elérésiút-alapú útválasztás**területen válassza **a több cél hozzáadása lehetőséget egy elérésiút-alapú szabály létrehozásához**.
2. Az **Elérésiút-szabály hozzáadása** ablakban adja meg a következő értékeket az elérésiút-szabályhoz:

   - **Elérési út**: */images/\**
   - **Elérésiút-szabály neve**: *Képek*
   - **Http-beállítás**: *myHTTPSetting* kiválasztása
   - **Háttérbeli cél**: *imagesBackendPool*
9. Válassza a **Hozzáadás** lehetőséget.
10. Adjon hozzá egy *videó*nevű másik elérésiút-szabályt *egy\* /video/* elérési úttal és egy *videoBackendPool*.
11. Válassza **a módosítások mentése lehetőséget, és térjen vissza az útválasztási szabályokhoz**.

    ![Útválasztási szabály hozzáadása](media/create-url-route-portal/add-routing-rule.png)

12. Válassza a **Hozzáadás** lehetőséget.

7. Válassza **a Next (tovább) lehetőséget: Címkék** , majd **tovább: Felülvizsgálat + létrehozás**.

### <a name="review--create-tab"></a>Felülvizsgálat + Létrehozás lap

Tekintse át a **felülvizsgálat + létrehozás** lapon található beállításokat, majd válassza a **Létrehozás** lehetőséget a virtuális hálózat, a nyilvános IP-cím és az Application Gateway létrehozásához. Az Azure az Application Gateway létrehozásához több percet is igénybe vehet.

Várjon, amíg a telepítés sikeresen befejeződik, mielőtt továbblép a következő szakaszra.


## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Ebben a példában három virtuális gépet hoz létre, amelyek háttér-kiszolgálóként szolgálnak az Application Gateway számára. Az IIS-t a virtuális gépeken is telepítheti annak ellenőrzéséhez, hogy az Application Gateway sikeresen létrejött-e.

1. A Azure Portal válassza az **erőforrás létrehozása**lehetőséget. Megjelenik az **új** ablak.
2. Válassza a **számítás** lehetőséget, majd válassza a **Windows Server 2016 Datacenter** elemet a **népszerű** listában. Megjelenik a **virtuális gép létrehozása** lap.

   Application Gateway átirányíthatja a forgalmat a háttér-készletben használt bármilyen típusú virtuális gépre. Ebben a példában egy Windows Server 2016 Datacenter rendszert használ.
1. Adja meg ezeket az értékeket az **alapok** lapon a következő virtuálisgép-beállításokhoz:

    - **Erőforráscsoport**: Válassza az erőforráscsoport neve **myResourceGroupAG** lehetőséget.
    - **Virtuális gép neve**: Adja meg a *myVM1* nevet a virtuális gép nevéhez.
    - **Felhasználónév**: Adja  meg az azureuser nevet a rendszergazda felhasználónevénél.
    - **Jelszó**: Adja meg a *Azure123456!* a rendszergazdai jelszóhoz.
4. Fogadja el a többi alapértelmezett értéket, **majd válassza a Next (tovább) gombot: Lemezek**.  
5. Fogadja el a **lemezek** lap alapértelmezett értékeit **, majd kattintson a Next (tovább) gombra: Hálózatkezelés**.
6. A **hálózatkezelés** lapon ellenőrizze, hogy a **virtuális hálózat** **myVNet** van-e kiválasztva, és az **alhálózat** **myBackendSubnet**értékre van-e állítva. Fogadja el a többi alapértelmezett értéket, **majd válassza a Next (tovább) gombot: Felügyelet**.

   A Application Gateway képes kommunikálni a virtuális hálózaton kívüli példányokkal, de gondoskodnia kell az IP-kapcsolatról.
1. A **felügyelet** lapon állítsa be a rendszerindítási **diagnosztika** beállítást **off**értékre. Fogadja el a többi alapértelmezett értéket, majd válassza a **felülvizsgálat + létrehozás**lehetőséget.
2. A **felülvizsgálat + létrehozás** lapon tekintse át a beállításokat, javítsa ki az érvényesítési hibákat, majd válassza a **Létrehozás**lehetőséget.
3. A folytatás előtt várja meg, amíg a virtuális gép létrehozása befejeződik.

### <a name="install-iis"></a>Az IIS telepítése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Nyissa meg az interaktív rendszerhéjt, és győződjön meg róla, hogy a **PowerShell**értékre van állítva.

    ![Egyéni bővítmény telepítése](./media/create-url-route-portal/application-gateway-extension.png)

2. Futtassa a következő parancsot az IIS a virtuális gépen való telepítéséhez: 

    ```azurepowershell-interactive
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

3. Hozzon létre két további virtuális gépet, és telepítse az IIS-t az imént befejezett lépések használatával. Használja a *myVM2* és a *myVM3* a virtuális gépek neveihez és a **VMName** értékekhez a set-AzVMExtension.

## <a name="add-backend-servers-to-backend-pools"></a>Háttér-kiszolgálók hozzáadása a háttérbeli készletekhez

1. Válassza a **minden erőforrás**lehetőséget, majd válassza a **myAppGateway**lehetőséget.

2. Válassza ki a **háttér-készletek** elemet a bal oldali menüben.

3. Válassza ki **appGatewayBackendPool**.

4. A **célok**területen válassza a **virtuális gép** lehetőséget a legördülő listából.

5. A **virtuális gép** és **hálózati adapterek**területen válassza ki a **myVM1** virtuális gépet, és a társított hálózati adaptert a legördülő listából.

    ![Háttérkiszolgálók hozzáadása](./media/create-url-route-portal/backend-pool.png)

6. Kattintson a **Mentés** gombra.
7. Ismételje meg a *myVM2* és az illesztőfelület hozzáadását a *imagesBackendPool*, majd a *MyVM3* és a felületet a *videoBackendPool*.

Várjon, amíg a telepítés befejeződik, mielőtt továbblép a következő lépésre.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

1. Válassza a **minden erőforrás**lehetőséget, majd válassza a **myAGPublicIPAddress**lehetőséget.

    ![Alkalmazásátjáró nyilvános IP-címének rögzítése](./media/create-url-route-portal/application-gateway-record-ag-address.png)

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Például: http://40.121.222.19.

    ![Az alap URL-cím tesztelése az alkalmazásátjáróban](./media/create-url-route-portal/application-gateway-iistest.png)

3. Módosítsa az URL-címet&lt;a http://IP&gt;-cím: 8080/images/test.htm &lt;értékre,&gt; és cserélje le az IP-címet az IP-címére, és az alábbi példához hasonlóan kell megjelennie:

    ![Képek URL-címének tesztelése az alkalmazásátjáróban](./media/create-url-route-portal/application-gateway-iistest-images.png)

4. Módosítsa az URL-címet&lt;a http://IP&gt;-cím: 8080/video/test.htm &lt;értékre,&gt; és cserélje le az IP-címet az IP-címére, és az alábbi példához hasonlóan kell megjelennie:

    ![Videók URL-címének tesztelése az alkalmazásátjáróban](./media/create-url-route-portal/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az Application Gateway használatával létrehozott erőforrásokra, távolítsa el az erőforráscsoportot. Az erőforráscsoport eltávolításával az Application Gateway és az ahhoz kapcsolódó összes erőforrást is eltávolítja. 

Az erőforráscsoport eltávolítása:

1. A Azure Portal bal oldali menüjében válassza az **erőforráscsoportok**lehetőséget.
2. Az **erőforráscsoportok** lapon keresse meg a **myResourceGroupAG** a listában, majd válassza ki azt.
3. Az **erőforráscsoport lapon**válassza az **erőforráscsoport törlése**elemet.
4. Írja be a *myResourceGroupAG* **nevet az erőforráscsoport neveként** , majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ az Azure Application Gateway](application-gateway-introduction.md)
