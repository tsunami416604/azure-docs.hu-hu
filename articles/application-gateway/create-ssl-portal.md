---
title: 'Oktatóanyag: A TLS-végződtetés konfigurálása a portálon – Azure Application Gateway'
description: Ebben az oktatóanyagban megtudhatja, hogyan konfigurálhat egy alkalmazásátjárót, és adhat hozzá tanúsítványt a TLS-megszüntetéshez az Azure Portalhasználatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 04/22/2019
ms.author: victorh
ms.openlocfilehash: 62f5375a0d468f5b137c4628c89c802d83dee102
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024492"
---
# <a name="tutorial-configure-an-application-gateway-with-tls-termination-using-the-azure-portal"></a>Oktatóanyag: Alkalmazásátjáró konfigurálása TLS-végződtetéssel az Azure Portal használatával

Az Azure Portal segítségével konfigurálhat egy [alkalmazásátjárót](overview.md) egy TLS-végződési tanúsítvánnyal, amely virtuális gépeket használ a háttérkiszolgálókhoz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Önaláírt tanúsítvány létrehozása
> * Alkalmazásátjáró létrehozása a tanúsítvánnyal
> * Háttérkiszolgálóként használt virtuális gépek létrehozása
> * Az alkalmazásátjáró tesztelése

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Bejelentkezés az Azure Portalra[https://portal.azure.com](https://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Ebben a szakaszban a [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) használatával önaláírt tanúsítványt hozhat létre. Töltse fel a tanúsítványt az Azure Portalon, amikor létrehozza az alkalmazásátjáró figyelőjét.

A helyi számítógépen nyisson meg egy Windows PowerShell-ablakot rendszergazdaként. A tanúsítvány létrehozásához futtassa a következő parancsot:

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Meg kell látni valami ilyesmi választ:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Használja [az Export-PfxCertificate-t](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) a visszaadott ujjlenyomattal a pfx fájl exportálásához a tanúsítványból. Győződjön meg arról, hogy a jelszó 4-12 karakter hosszú:


```powershell
$pwd = ConvertTo-SecureString -String <your password> -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

1. Válassza **az Erőforrás létrehozása lehetőséget** az Azure Portal bal oldali menüjében. Megjelenik **az Új** ablak.

2. Válassza **a Hálózat lehetőséget,** majd a **Kiemelt** listában válassza az **Alkalmazásátjáró** elemet.

### <a name="basics-tab"></a>Alapok lap

1. Az **Alapok lapon** adja meg ezeket az értékeket a következő alkalmazásátjáró-beállításokhoz:

   - **Erőforráscsoport**: Válassza ki a **myResourceGroupAG-t** az erőforráscsoporthoz. Ha nem létezik, válassza az **Új létrehozása** lehetőséget a létrehozáshoz.
   - **Alkalmazásátjáró neve**: Adja meg a *myAppGateway-t* az alkalmazásátjáró nevéhez.

        ![Új alkalmazásátjáró létrehozása: Alapok](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Ahhoz, hogy az Azure kommunikáljon a létrehozott erőforrások között, virtuális hálózatra van szüksége. Létrehozhat egy új virtuális hálózatot, vagy használhat egy meglévőt. Ebben a példában egy új virtuális hálózatot hoz létre az alkalmazásátjáró létrehozása kor. Az Alkalmazásátjáró-példányok külön alhálózatokban jönnek létre. Ebben a példában két alhálózatot hoz létre: egyet az alkalmazásátjáróhoz, egyet pedig a háttérkiszolgálókhoz.

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
   > Az Application Gateway v2 termékváltozat esetén csak **a Nyilvános** előtér IP-konfigurációt választhatja. A privát előtér-IP-konfiguráció jelenleg nincs engedélyezve ehhez a v2 termékváltozathoz.

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
    - **Protokoll**: Válassza a **HTTPS**lehetőséget.
    - **Port**: Ellenőrizze, hogy a 443-as be van-e adva a porthoz.

   A **HTTPS-tanúsítvány csoportban:**

   - **PFX tanúsítványfájl** – Keresse meg és jelölje ki a korábban létrehozott c:\appgwcert.pfx fájlt.
   - **Tanúsítvány neve** - Írja be a *mycert1* nevet a tanúsítvány nevéhez.
   - **Jelszó** – Írja be a jelszavát.
  
        Fogadja el a **Figyelő** lap többi beállításának alapértelmezett értékeit, majd a **Háttérrendszer-célok** lapra a művelettervi szabály többi részének konfigurálásához.

   ![Új alkalmazásátjáró létrehozása: figyelő](./media/create-ssl-portal/application-gateway-create-rule-listener.png)

4. A **háttérrendszer-célok** lapon válassza a **myBackendPool** elemet a **háttér-célhoz.**

5. A **HTTP beállításhoz**válassza az **Új létrehozása lehetőséget** új HTTP-beállítás létrehozásához. A HTTP-beállítás határozza meg az útválasztási szabály viselkedését. A **Megnyíló HTTP-beállítás hozzáadása** ablakban adja meg a *myHTTPSetting* parancsot a **HTTP-beállítás nevéhez**. Fogadja el a HTTP hozzáadása **beállításablak** egyéb beállításainak alapértelmezett értékeit, majd a **Hozzáadás gombra** a **Művelettervszabály hozzáadása** ablakhoz való visszatéréshez. 

   ![Új alkalmazásátjáró létrehozása: HTTP-beállítás](./media/create-ssl-portal/application-gateway-create-httpsetting.png)

6. A **Művelettervszabály hozzáadása** ablakban válassza a **Hozzáadás** lehetőséget a művelettervi szabály mentéséhez és a **Konfiguráció** lapra való visszatéréshez.

   ![Új alkalmazásátjáró létrehozása: útválasztási szabály](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Válassza a **Tovább: Címkék,** majd **a Tovább: Véleményezés + létrehozás**lehetőséget.

### <a name="review--create-tab"></a>Véleményezés + létrehozás lap

Tekintse át a Beállításokat a **Véleményezés + létrehozás** lapon, majd válassza a **Létrehozás** gombot a virtuális hálózat, a nyilvános IP-cím és az alkalmazásátjáró létrehozásához. Az Azure-nak az alkalmazásátjáró létrehozása több percig is eltarthat. Várja meg, amíg a központi telepítés sikeresen befejeződik, mielőtt továbblépne a következő szakaszra.

## <a name="add-backend-targets"></a>Háttér-célok hozzáadása

Ebben a példában a virtuális gépeket fogja használni a cél háttérrendszerként. Használhatja a meglévő virtuális gépeket, vagy újakat hozhat létre. Két virtuális gépet hoz létre, amelyeket az Azure háttérkiszolgálóként használ az alkalmazásátjáróhoz.

Ehhez a következőkre van szüksége:

1. Hozzon létre két új virtuális gépet, *myVM-et* és *myVM2-t,* amelyek háttérkiszolgálóként használhatók.
2. Telepítse az IIS-t a virtuális gépekre az alkalmazásátjáró sikeres létrehozásának ellenőrzéséhez.
3. Adja hozzá a háttérkiszolgálókat a háttérkészlethez.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Az Azure Portalon válassza az **Erőforrás létrehozása lehetőséget.** Megjelenik **az Új** ablak.
2. Válassza a **Windows Server 2016 Datacenter elemet** a **Népszerű** listában. Megjelenik **a Virtuálisgép létrehozása** lap.

   Az Application Gateway a háttérkészletében használt bármilyen típusú virtuális gépre irányíthatja a forgalmat. Ebben a példában Windows Server 2016 Datacenter t használ.

1. Adja meg ezeket az értékeket az **Alapok** lapon a következő virtuális gépbeállításokhoz:

    - **Erőforráscsoport**: Válassza ki a **myResourceGroupAG** elemet az erőforráscsoport nevéhez.
    - **Virtuális gép neve**: Adja meg a *virtuális* gép nevét.
    - **Felhasználónév**: Adja meg *az azureuser* t a rendszergazdai felhasználónévhez.
    - **Jelszó**: Adja meg a rendszergazdai fiók jelszavát.
1. Fogadja el a többi alapértelmezést, majd válassza **a Tovább: Lemezek**lehetőséget.  
2. Fogadja el a **Lemezek** lap alapértelmezett beállításait, és válassza **a Tovább: Hálózat lehetőséget.**
3. A **Hálózat** lapon ellenőrizze, hogy a **myVNet** ki van-e jelölve a **virtuális hálózathoz,** és az **alhálózat** beállítása **myBackendSubnet**. Fogadja el a többi alapértelmezést, majd válassza **a Tovább: Kezelés**lehetőséget.

   Az Application Gateway képes kommunikálni a virtuális hálózaton kívüli példányokkal, de biztosítania kell az IP-kapcsolatot.
1. A **Kezelés** lapon állítsa a **Rendszerindításdia imát** **Ki**beállításra. Fogadja el a többi alapértelmezést, majd válassza **a Véleményezés + létrehozás**lehetőséget.
2. A **Véleményezés + Létrehozás** lapon tekintse át a beállításokat, javítsa ki az érvényesítési hibákat, és válassza a **Létrehozás gombot.**
3. A folytatás előtt várja meg, amíg befejeződik a telepítés.

### <a name="install-iis-for-testing"></a>Az IIS telepítése tesztelésre

Ebben a példában csak az Azure sikeres létrehozásához telepítse az IIS-t a virtuális gépekre.

1. Nyissa meg [az Azure PowerShellt.](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell) Ehhez válassza a **Cloud Shell** az Azure Portal felső navigációs sávján, majd válassza a **PowerShell** a legördülő listából. 

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

3. Hozzon létre egy második virtuális gépet, és telepítse az IIS-t a korábban végrehajtott lépések végrehajtásával. Használja *a myVM2-t* a virtuális gép nevéhez és a **Set-AzVMExtension** parancsmag **VMName** beállításához.

### <a name="add-backend-servers-to-backend-pool"></a>Háttérkiszolgálók hozzáadása a háttérkészlethez

1. Válassza **az Összes erőforrás**lehetőséget, majd a **myAppGateway**lehetőséget.

2. Válassza a bal oldali menü **háttérkészletek parancsát.**

3. Válassza a **myBackendPool lehetőséget.**

4. A **Célok csoportban**válassza a **Virtuális gép** lehetőséget a legördülő listából.

5. A **VIRTUÁLIS GÉP** és a HÁLÓZATI **ADAPTEREK**csoportban válassza ki a **myVM** és **myVM2** virtuális gépeket és a hozzájuk kapcsolódó hálózati interfészeket a legördülő listából.

    ![Háttérkiszolgálók hozzáadása](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Kattintson a **Mentés** gombra.

7. Várja meg, amíg a központi telepítés befejeződik, mielőtt továbblépne a következő lépésre.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

1. Válassza **az Összes erőforrás**lehetőséget, majd a **myAGPublicIPAddress lehetőséget.**

    ![Alkalmazásátjáró nyilvános IP-címének rögzítése](./media/create-ssl-portal/application-gateway-ag-address.png)

2. A böngésző címsorába írja be *https://\<az\>alkalmazásátjáró IP-címét.*

   Ha önaláírt tanúsítványt használ, válassza a **Részletek** (vagy **a Speciális** a Chrome-on) lehetőséget, és lépjen a weblapra:

    ![Biztonsági figyelmeztetés](./media/create-ssl-portal/application-gateway-secure.png)

    Ekkor a biztonságos IIS-webhely a következő példához hasonlóan jelenik meg:

    ![Az alap URL-cím tesztelése az alkalmazásátjáróban](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ az Application Gateway TLS támogatásáról](ssl-overview.md)
