---
title: Rövid útmutató – Webes forgalom irányítása az Azure Application Gatewayjel – Azure Portal | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egy olyan Azure-Application Gateway, amely a webes forgalmat egy háttér-készletben lévő virtuális gépekre irányítja a Azure Portal használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 6d12b006583c004d12c50bda171c82397ff7949f
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276603"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Gyors útmutató: Webes forgalom közvetlen továbbítása az Azure Application Gateway-Azure Portal

Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Application Gateway-t a Azure Portal használatával.  Az Application Gateway létrehozása után tesztelje, hogy megfelelően működik-e. Az Azure Application Gateway használatával a figyelőket a portokhoz, a szabályok létrehozásához és az erőforrások háttér-készlethez való hozzáadásához irányíthatja az alkalmazás webes forgalmát adott erőforrásokra. Az egyszerűség kedvéért ez a cikk egy egyszerű telepítőt használ egy nyilvános előtér-IP-címmel, egy alapszintű figyelővel, amely egyetlen helyet üzemeltet az Application gatewayben, két virtuális gépet használ a háttér-készlethez, valamint egy alapszintű kérelem-útválasztási szabályt.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

1. Válassza az **erőforrás létrehozása** elemet a Azure Portal bal oldali menüjében. Megjelenik az **új** ablak.

2. Válassza a **hálózatkezelés** lehetőséget, majd a **Kiemelt** listában válassza a **Application Gateway** lehetőséget.

### <a name="basics-tab"></a>Alapbeállítások lap

1. Az **alapok** lapon adja meg a következő Application Gateway-beállításokhoz tartozó értékeket:

   - **Erőforráscsoport**: Válassza ki az erőforráscsoport **myResourceGroupAG** . Ha nem létezik, válassza az **új létrehozása** elemet a létrehozásához.
   - **Application Gateway neve**: Adja meg a *myAppGateway* nevet az Application Gateway neveként.

     ![Új Application Gateway létrehozása: Alapvető beállítások](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Ahhoz, hogy az Azure kommunikáljon a létrehozott erőforrások között, szüksége van egy virtuális hálózatra. Hozzon létre egy új virtuális hálózatot, vagy használjon egy meglévőt. Ebben a példában egy új virtuális hálózatot fog létrehozni az Application Gateway létrehozásakor. Application Gateway példányok külön alhálózatokban jönnek létre. Ebben a példában két alhálózatot hoz létre: egyet az Application Gateway számára, és egy másikat a háttér-kiszolgálók számára.

    A **virtuális hálózat konfigurálása**területen hozzon létre egy új virtuális hálózatot az **új létrehozása**lehetőség kiválasztásával. A megnyíló **virtuális hálózat létrehozása** ablakban adja meg a következő értékeket a virtuális hálózat és két alhálózat létrehozásához:

    - **Név**: Adja meg a virtuális hálózat nevének *myVNet* .

    - **Alhálózat neve** (Application Gateway alhálózat): Az  alhálózatok rácsa az *alapértelmezett*nevű alhálózatot jeleníti meg. Módosítsa az alhálózat nevét a *myAGSubnet*értékre.<br>Az Application Gateway-alhálózat csak Application Gateway átjárókat tartalmazhat. Más erőforrások nem engedélyezettek.

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

A háttér-készlet arra szolgál, hogy a kérelmeket a kérést kiszolgáló háttér-kiszolgálókra irányítsa. A háttér-készletek a hálózati adapterek, a virtuálisgép-méretezési csoportok, a nyilvános IP-címek, a belső IP-címek, a teljes tartománynevek (FQDN) és a több-bérlős háttér-végpontok, például a Azure App Service tagjai lehetnek. Ebben a példában egy üres háttér-készletet fog létrehozni az Application gatewayben, majd a háttérbeli célokat hozzá kell adni a háttér-készlethez.

1. A **háttérrendszer** lapon válassza a **+ háttér-készlet hozzáadása**elemet.

2. A megnyíló **háttérbeli készlet hozzáadása** ablakban adja meg a következő értékeket egy üres háttérbeli készlet létrehozásához:

    - **Név**: Adja meg a *myBackendPool* nevét a háttérbeli készlet neveként.
    - **Háttérbeli készlet hozzáadása célok nélkül**: Válassza az **Igen** lehetőséget, ha célokat nem tartalmazó háttér-készletet szeretne létrehozni. Az Application Gateway létrehozása után hozzá kell adni a háttérbeli célokat.

3. A háttérbeli **készlet hozzáadása** ablakban válassza a **Hozzáadás** elemet a háttérbeli készlet konfigurációjának mentéséhez, és térjen  vissza a háttérrendszer lapra.

     ![Új Application Gateway létrehozása: háttérrendszer](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. A backends (háttérrendszer) **lapon válassza a Next (tovább) gombot:  Konfiguráció**.

### <a name="configuration-tab"></a>Konfiguráció lap

A **konfiguráció** lapon összekapcsolja az útválasztási szabály használatával létrehozott előtér-és háttér-készletet.

1. Válassza a **szabály hozzáadása** lehetőséget az **útválasztási szabályok** oszlopban.

2. A megnyíló **útválasztási szabály hozzáadása** ablakban írja be a *MyRoutingRule* nevet a **szabály neveként**.

3. Egy útválasztási szabályhoz egy figyelő szükséges. Az **útválasztási szabály hozzáadása** ablak **figyelő** lapján adja meg az alábbi értékeket a figyelőhöz:

    - **Figyelő neve**: Adja meg a *myListener* a figyelő neveként.
    - Előtéri **IP-cím**: Válassza a **nyilvános** lehetőséget a előtérhez létrehozott nyilvános IP-cím kiválasztásához.
  
      Fogadja el az alapértelmezett értékeket a **figyelő** lapon a többi beállításnál, majd válassza a **háttérbeli célok** fület a többi útválasztási szabály konfigurálásához.

   ![Új Application Gateway létrehozása: figyelő](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-listener.png)

4. A **háttérbeli célok** lapon válassza a **MyBackendPool** lehetőséget a **háttérbeli célként**.

5. A **http**-beállításnál válassza az **új létrehozása** lehetőséget egy új http-beállítás létrehozásához. A HTTP-beállítás határozza meg az útválasztási szabály viselkedését. A megnyíló **http-beállítás hozzáadása** ablakban írja be a *MyHTTPSetting* nevet a **http-beállítás neveként**. Fogadja el az alapértelmezett értékeket a további beállításokhoz a **http-beállítás hozzáadása** ablakban, majd válassza a **Hozzáadás** lehetőséget az **útválasztási szabály hozzáadása** ablakhoz való visszatéréshez. 

     ![Új Application Gateway létrehozása: HTTP-beállítás](./media/application-gateway-create-gateway-portal/application-gateway-create-httpsetting.png)

6. Az útválasztási szabály **hozzáadása** ablakban válassza a **Hozzáadás** lehetőséget az útválasztási szabály mentéséhez és a **konfiguráció** laphoz való visszatéréshez.

     ![Új Application Gateway létrehozása: útválasztási szabály](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Válassza **a Next (tovább) lehetőséget: Címkék** , majd **tovább: Felülvizsgálat + létrehozás**.

### <a name="review--create-tab"></a>Felülvizsgálat + Létrehozás lap

Tekintse át a **felülvizsgálat + létrehozás** lapon található beállításokat, majd válassza a **Létrehozás** lehetőséget a virtuális hálózat, a nyilvános IP-cím és az Application Gateway létrehozásához. Az Azure az Application Gateway létrehozásához több percet is igénybe vehet. Várjon, amíg a telepítés sikeresen befejeződik, mielőtt továbblép a következő szakaszra.

## <a name="add-backend-targets"></a>Háttérbeli célok hozzáadása

Ebben a példában a virtuális gépeket célként használt háttérként fogja használni. Használhat meglévő virtuális gépeket, vagy újakat is létrehozhat. Hozzon létre két virtuális gépet, amelyeket az Azure háttér-kiszolgálóként használ az Application Gateway számára.

Ehhez a következőket kell tennie:

1. Hozzon létre két új virtuális gépet, *myVM* és *myVM2*a háttér-kiszolgálóként való használathoz.
2. Telepítse az IIS-t a virtuális gépeken annak ellenőrzéséhez, hogy az Application Gateway sikeresen létrejött-e.
3. Adja hozzá a háttér-kiszolgálókat a háttér-készlethez.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. A Azure Portal válassza az **erőforrás létrehozása**lehetőséget. Megjelenik az **új** ablak.
2. Válassza a **számítás** lehetőséget, majd válassza a **Windows Server 2016 Datacenter** elemet a **népszerű** listában. Megjelenik a **virtuális gép létrehozása** lap.<br>Application Gateway átirányíthatja a forgalmat a háttér-készletben használt bármilyen típusú virtuális gépre. Ebben a példában egy Windows Server 2016 Datacenter rendszert használ.
3. Adja meg ezeket az értékeket az **alapok** lapon a következő virtuálisgép-beállításokhoz:

    - **Erőforráscsoport**: Válassza az erőforráscsoport neve **myResourceGroupAG** lehetőséget.
    - **Virtuális gép neve**: Adja meg a *myVM* nevet a virtuális gép nevéhez.
    - **Felhasználónév**: Adja  meg az azureuser nevet a rendszergazda felhasználónevénél.
    - **Jelszó**: Adja meg a *Azure123456!* a rendszergazdai jelszóhoz.
4. Fogadja el a többi alapértelmezett értéket, **majd válassza a Next (tovább) gombot: Lemezek**.  
5. Fogadja el a **lemezek** lap alapértelmezett értékeit **, majd kattintson a Next (tovább) gombra: Hálózatkezelés**.
6. A **hálózatkezelés** lapon ellenőrizze, hogy a **virtuális hálózat** **myVNet** van-e kiválasztva, és az **alhálózat** **myBackendSubnet**értékre van-e állítva. Fogadja el a többi alapértelmezett értéket, **majd válassza a Next (tovább) gombot: Felügyelet**.<br>A Application Gateway képes kommunikálni a virtuális hálózaton kívüli példányokkal, de gondoskodnia kell az IP-kapcsolatról.
7. A **felügyelet** lapon állítsa be a rendszerindítási **diagnosztika** beállítást **off**értékre. Fogadja el a többi alapértelmezett értéket, majd válassza a **felülvizsgálat + létrehozás**lehetőséget.
8. A **felülvizsgálat + létrehozás** lapon tekintse át a beállításokat, javítsa ki az érvényesítési hibákat, majd válassza a **Létrehozás**lehetőséget.
9. A folytatás előtt várja meg, amíg a virtuális gép létrehozása befejeződik.

### <a name="install-iis-for-testing"></a>Az IIS telepítése teszteléshez

Ebben a példában az IIS-t csak akkor telepíti a virtuális gépekre, ha ellenőrzi, hogy az Azure sikeresen létrehozta-e az Application Gatewayt.

1. Nyissa meg [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Ehhez válassza ki a **Cloud Shell** elemet a Azure Portal felső navigációs sávján, majd válassza a **PowerShell** elemet a legördülő listából. 

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

3. Hozzon létre egy második virtuális gépet, és telepítse az IIS-t a korábban befejezett lépések segítségével. Használja a *myVM2* a virtuális gép nevéhez és a **set-AzVMExtension** parancsmag **VMName** beállításához.

### <a name="add-backend-servers-to-backend-pool"></a>Háttér-kiszolgálók hozzáadása a háttérbeli készlethez

1. Válassza a **minden erőforrás**lehetőséget, majd válassza a **myAppGateway**lehetőséget.

2. Válassza ki a **háttér-készletek** elemet a bal oldali menüben.

3. Válassza a **myBackendPool**lehetőséget.

4. A **célok**területen válassza a **virtuális gép** lehetőséget a legördülő listából.

5. A **virtuális gép** és **hálózati adapterek**területen válassza ki a **myVM** és **myVM2** virtuális gépeket és a hozzájuk társított hálózati adaptereket a legördülő listából.

    ![Háttérkiszolgálók hozzáadása](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Kattintson a **Mentés** gombra.

7. Várjon, amíg a telepítés befejeződik, mielőtt továbblép a következő lépésre.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Bár az IIS nem szükséges az Application Gateway létrehozásához, ezt a rövid útmutatóban telepítette annak ellenőrzéséhez, hogy az Azure sikeresen létrehozta-e az Application Gatewayt. Az IIS használata az Application Gateway teszteléséhez:

1. Keresse meg az Application Gateway nyilvános IP-címét az **Áttekintés** oldalon. Az Application Gateway nyilvános IP-](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) címének rögzítése vagy a **minden erőforrás**lehetőség kiválasztásával megadhatja a myAGPublicIPAddress kifejezést a keresőmezőbe, majd kiválaszthatja azt a keresési eredmények között.  ![ Az Azure megjeleníti a nyilvános IP-címet az **Áttekintés** oldalon.
2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.
3. Keresse meg a választ. Egy érvényes válasz ellenőrzi, hogy az Application Gateway sikeresen létrejött-e, és sikeresen tud-e kapcsolatot létesíteni a háttérrel.![Az alkalmazásátjáró tesztelése](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az Application Gateway használatával létrehozott erőforrásokra, távolítsa el az erőforráscsoportot. Az erőforráscsoport eltávolításával az Application Gateway és az ahhoz kapcsolódó összes erőforrást is eltávolítja. 

Az erőforráscsoport eltávolítása:
1. A Azure Portal bal oldali menüjében válassza az **erőforráscsoportok**lehetőséget.
2. Az **erőforráscsoportok** lapon keresse meg a **myResourceGroupAG** a listában, majd válassza ki azt.
3. Az **erőforráscsoport lapon**válassza az **erőforráscsoport törlése**elemet.
4. Írja be a *myResourceGroupAG* **nevet az erőforráscsoport neveként** , majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes forgalom kezelése alkalmazásátjáróval az Azure CLI használatával](./tutorial-manage-web-traffic-cli.md)
