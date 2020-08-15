---
title: 'Oktatóanyag: a TLS-megszakítás konfigurálása a portálon – Azure Application Gateway'
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat egy Application Gatewayt, és hogyan adhat hozzá tanúsítványokat a TLS-lezáráshoz a Azure Portal használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: victorh
ms.openlocfilehash: 0d0522dd2f206e02ad8b63b13a9537c049232db2
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245740"
---
# <a name="tutorial-configure-an-application-gateway-with-tls-termination-using-the-azure-portal"></a>Oktatóanyag: Application Gateway konfigurálása TLS-megszakítással a Azure Portal használatával

A Azure Portal használatával konfigurálhat egy [Application Gateway](overview.md) -tanúsítványt a TLS-megszakításhoz, amely virtuális gépeket használ a háttér-kiszolgálók számára.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Önaláírt tanúsítvány létrehozása
> * Alkalmazásátjáró létrehozása a tanúsítvánnyal
> * Háttér-kiszolgálóként használt virtuális gépek létrehozása
> * Az alkalmazásátjáró tesztelése

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Jelentkezzen be a Azure Portal [https://portal.azure.com](https://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Ebben a szakaszban a [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) használatával hozzon létre egy önaláírt tanúsítványt. Töltse fel a tanúsítványt a Azure Portalba, amikor létrehozza a figyelőt az Application Gateway számára.

A helyi számítógépen nyisson meg egy Windows PowerShell-ablakot rendszergazdaként. Futtassa a következő parancsot a tanúsítvány létrehozásához:

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Ehhez a válaszhoz hasonlóan kell megjelennie:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Az [export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) és a tanúsítványból származó pfx-fájl exportálásához visszaadott ujjlenyomattal használja. Győződjön meg arról, hogy a jelszó 4-12 karakter hosszú:


```powershell
$pwd = ConvertTo-SecureString -String <your password> -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

1. Válassza az **erőforrás létrehozása** elemet a Azure Portal bal oldali menüjében. Megjelenik az **új** ablak.

2. Válassza a **hálózatkezelés** lehetőséget, majd a **Kiemelt** listában válassza a **Application Gateway** lehetőséget.

### <a name="basics-tab"></a>Alapbeállítások lap

1. Az **alapok** lapon adja meg a következő Application Gateway-beállításokhoz tartozó értékeket:

   - **Erőforráscsoport**: válassza ki az erőforráscsoport **myResourceGroupAG** . Ha nem létezik, válassza az **új létrehozása** elemet a létrehozásához.
   - **Application Gateway neve**: írja be a *myAppGateway* nevet az Application Gateway neveként.

        ![Új Application Gateway létrehozása: alapismeretek](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Ahhoz, hogy az Azure kommunikáljon a létrehozott erőforrások között, szüksége van egy virtuális hálózatra. Hozzon létre egy új virtuális hálózatot, vagy használjon egy meglévőt. Ebben a példában egy új virtuális hálózatot fog létrehozni az Application Gateway létrehozásakor. Application Gateway példányok külön alhálózatokban jönnek létre. Ebben a példában két alhálózatot hoz létre: egyet az Application Gateway számára, és egy másikat a háttér-kiszolgálók számára.

    A **virtuális hálózat konfigurálása**területen hozzon létre egy új virtuális hálózatot az **új létrehozása**lehetőség kiválasztásával. A megnyíló **virtuális hálózat létrehozása** ablakban adja meg a következő értékeket a virtuális hálózat és két alhálózat létrehozásához:

    - **Név**: írja be a *myVNet* nevet a virtuális hálózat nevéhez.

    - **Alhálózat neve** (Application Gateway alhálózat): az **alhálózatok** rácsa egy *alapértelmezett*nevű alhálózatot fog megjeleníteni. Módosítsa az alhálózat nevét a *myAGSubnet*értékre.<br>Az Application Gateway-alhálózat csak Application Gateway átjárókat tartalmazhat. Más erőforrások nem engedélyezettek.

    - **Alhálózat neve** (háttérbeli kiszolgáló alhálózata): az **alhálózatok** rácsának második sorában adja meg az *myBackendSubnet* értéket az **alhálózat neve** oszlopban.

    - **Címtartomány** (háttér-kiszolgáló alhálózata): az **alhálózatok** rácsának második sorában olyan címtartományt adjon meg, amely nem fedi át a *myAGSubnet*címtartomány-tartományát. Ha például a *myAGSubnet* 10.0.0.0/24, a *10.0.1.0/24* értéket adja meg a *myBackendSubnet*-tartományhoz.

    A **virtuális hálózat létrehozása** ablak bezárásához és a virtuális hálózat beállításainak mentéséhez kattintson **az OK gombra** .

    ![Új Application Gateway létrehozása: virtuális hálózat](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Az **alapvető** beállítások lapon fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **Next (tovább): frontends**elemet.

### <a name="frontends-tab"></a>Frontendek lap

1. A **frontendek** lapon ellenőrizze, hogy a előtérbeli **IP-cím típusa** **nyilvános**értékre van-e állítva. <br>Az előtérbeli IP-címet úgy állíthatja be, hogy a használati esetnek megfelelően nyilvános vagy privát legyen. Ebben a példában egy nyilvános előtérbeli IP-címet választ.
   > [!NOTE]
   > A Application Gateway v2 SKU esetében csak **nyilvános** ELŐTÉRBELI IP-konfigurációt választhat. A privát előtérbeli IP-konfiguráció jelenleg nincs engedélyezve ehhez a v2 SKU-hoz.

2. Válassza a **nyilvános IP-cím** **új létrehozása** lehetőséget, és adja meg a *myAGPublicIPAddress* a nyilvános IP-cím neveként, majd kattintson **az OK gombra**. 

   ![Új Application Gateway létrehozása: frontendek](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Válassza a Next (tovább) lehetőséget **: háttérrendszer**.

### <a name="backends-tab"></a>Háttérrendszer lap

A háttér-készlet arra szolgál, hogy a kérelmeket a kérést kiszolgáló háttér-kiszolgálókra irányítsa. A háttér-készletek a hálózati adapterek, a virtuálisgép-méretezési csoportok, a nyilvános IP-címek, a belső IP-címek, a teljes tartománynevek (FQDN) és a több-bérlős háttér-végpontok, például a Azure App Service tagjai lehetnek. Ebben a példában egy üres háttér-készletet hozunk létre az Application Gateway használatával, majd a háttérbeli célokat hozzá kell adni a háttér-készlethez.

1. A **háttérrendszer** lapon válassza a **+ háttér-készlet hozzáadása**elemet.

2. A megnyíló **háttérbeli készlet hozzáadása** ablakban adja meg a következő értékeket egy üres háttérbeli készlet létrehozásához:

    - **Név**: adja meg a *myBackendPool* nevét a háttér-készlet neveként.
    - **Háttérbeli készlet hozzáadása célok nélkül**: válassza az **Igen** lehetőséget, ha célokat nem tartalmazó háttér-készletet szeretne létrehozni. Az Application Gateway létrehozása után hozzá kell adni a háttérbeli célokat.

3. A háttérbeli **készlet hozzáadása** ablakban válassza a **Hozzáadás** elemet a háttérbeli készlet konfigurációjának mentéséhez, és térjen vissza a **háttérrendszer** lapra.

   ![Új Application Gateway létrehozása: háttérrendszer](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. A **háttérrendszer** lapon válassza a **Tovább: konfigurálás**lehetőséget.

### <a name="configuration-tab"></a>Konfiguráció lap

A **konfiguráció** lapon összekapcsolja az útválasztási szabály használatával létrehozott előtér-és háttér-készletet.

1. Válassza a **szabály hozzáadása** lehetőséget az **útválasztási szabályok** oszlopban.

2. A megnyíló **útválasztási szabály hozzáadása** ablakban írja be a *MyRoutingRule* nevet a **szabály neveként**.

3. Egy útválasztási szabályhoz egy figyelő szükséges. Az **útválasztási szabály hozzáadása** ablak **figyelő** lapján adja meg az alábbi értékeket a figyelőhöz:

    - **Figyelő neve**: írja be a *myListener* nevet a figyelőnek.
    - Előtér **-IP**: válassza a **nyilvános** lehetőséget, hogy kiválassza a előtérhez létrehozott nyilvános IP-címet.
    - **Protokoll**: válassza a **https**lehetőséget.
    - **Port**: Ellenőrizze, hogy a 443 a porthoz van-e megadva.

   **Https-tanúsítvány**alatt:

   - **Pfx tanúsítványfájl** – keresse meg és válassza ki a korábban létrehozott c:\appgwcert.pfx-fájlt.
   - **Tanúsítvány neve** – adja meg a tanúsítvány nevét a *mycert1* .
   - **Jelszó** – írja be a jelszót.
  
        Fogadja el az alapértelmezett értékeket a **figyelő** lapon a többi beállításnál, majd válassza a **háttérbeli célok** fület a többi útválasztási szabály konfigurálásához.

   ![Új Application Gateway létrehozása: figyelő](./media/create-ssl-portal/application-gateway-create-rule-listener.png)

4. A **háttérbeli célok** lapon válassza a **MyBackendPool** lehetőséget a **háttérbeli célként**.

5. A **http-beállításnál**válassza az **új létrehozása** lehetőséget egy új http-beállítás létrehozásához. A HTTP-beállítás határozza meg az útválasztási szabály viselkedését. A megnyíló **http-beállítás hozzáadása** ablakban írja be a *MyHTTPSetting* nevet a **http-beállítás neveként**. Fogadja el az alapértelmezett értékeket a további beállításokhoz a **http-beállítás hozzáadása** ablakban, majd válassza a **Hozzáadás** lehetőséget az **útválasztási szabály hozzáadása** ablakhoz való visszatéréshez. 

   :::image type="content" source="./media/create-ssl-portal/application-gateway-create-httpsetting.png" alt-text="Új Application Gateway létrehozása: HTTP-beállítás":::

6. Az útválasztási szabály **hozzáadása** ablakban válassza a **Hozzáadás** lehetőséget az útválasztási szabály mentéséhez és a **konfiguráció** laphoz való visszatéréshez.

   ![Új Application Gateway létrehozása: útválasztási szabály](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Válassza a **Next (tovább** ) gombot, majd a **következőt: felülvizsgálat + létrehozás**.

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
2. Válassza a **Windows Server 2016 Datacenter** elemet a **népszerű** listában. Megjelenik a **virtuális gép létrehozása** lap.

   Application Gateway átirányíthatja a forgalmat a háttér-készletben használt bármilyen típusú virtuális gépre. Ebben a példában egy Windows Server 2016 Datacenter rendszert használ.

1. Adja meg ezeket az értékeket az **alapok** lapon a következő virtuálisgép-beállításokhoz:

    - **Erőforráscsoport**: válassza ki a **myResourceGroupAG** az erőforráscsoport neveként.
    - **Virtuális gép neve**: írja be a *myVM* nevet a virtuális gép nevéhez.
    - **Felhasználónév**: írja be az *azureuser* nevet a rendszergazda felhasználónevének.
    - **Password (jelszó**): adjon meg egy jelszót a rendszergazdai fiókhoz.
1. Fogadja el a többi alapértelmezett értéket, majd válassza a **Next: Disks**elemet.  
2. Fogadja el a **lemezek** lap alapértelmezett értékeit, majd kattintson a **Tovább gombra: hálózatkezelés**elemre.
3. A **hálózatkezelés** lapon ellenőrizze, hogy a **virtuális hálózat** **myVNet** van-e kiválasztva, és az **alhálózat** **myBackendSubnet**értékre van-e állítva. Fogadja el a többi alapértelmezett értéket, majd válassza a **Tovább: kezelés**lehetőséget.

   A Application Gateway képes kommunikálni a virtuális hálózaton kívüli példányokkal, de gondoskodnia kell az IP-kapcsolatról.
1. A **felügyelet** lapon állítsa be a **rendszerindítási diagnosztika** beállítást **off**értékre. Fogadja el a többi alapértelmezett értéket, majd válassza a **felülvizsgálat + létrehozás**lehetőséget.
2. A **felülvizsgálat + létrehozás** lapon tekintse át a beállításokat, javítsa ki az érvényesítési hibákat, majd válassza a **Létrehozás**lehetőséget.
3. Folytatás előtt várja meg, amíg az üzembe helyezési folyamat befejeződik.

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

6. Válassza a **Mentés** lehetőséget.

7. Várjon, amíg a telepítés befejeződik, mielőtt továbblép a következő lépésre.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

1. Válassza a **minden erőforrás**lehetőséget, majd válassza a **myAGPublicIPAddress**lehetőséget.

    ![Alkalmazásátjáró nyilvános IP-címének rögzítése](./media/create-ssl-portal/application-gateway-ag-address.png)

2. A böngésző címsorában írja be a következőt *: \<your application gateway ip address\> https://*.

   Ha önaláírt tanúsítványt használt, a biztonsági figyelmeztetés elfogadásához válassza a **részletek** (vagy a **speciális** a Chrome-on) lehetőséget, majd lépjen a weblapra:

    ![Biztonsági figyelmeztetés](./media/create-ssl-portal/application-gateway-secure.png)

    Ekkor a biztonságos IIS-webhely a következő példához hasonlóan jelenik meg:

    ![Az alap URL-cím tesztelése az alkalmazásátjáróban](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot és az összes kapcsolódó erőforrást. Ehhez válassza ki az erőforráscsoportot, majd válassza az **erőforráscsoport törlése**lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a Application Gateway TLS-támogatásról](ssl-overview.md)
