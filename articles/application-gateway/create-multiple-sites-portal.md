---
title: Hozzon létre több webhely - Azure-portálon üzemeltető Alkalmazásátjáró |} Microsoft Docs
description: Ismerje meg, amelyen az Azure portál használatával több webhely Alkalmazásátjáró létrehozása.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/28/2017
ms.author: victorh
ms.openlocfilehash: 9d55db498624d7f23bc499989333ba46340c43b0
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>Hozza létre és konfigurálja az Azure portál használatával több webhely üzemeltetésére Alkalmazásátjáró

Használhatja az Azure portálon [konfigurálása több webhely üzemeltetéséhez](multiple-site-overview.md) létrehozásakor egy [Alkalmazásátjáró](overview.md). Ebben az oktatóanyagban a virtuális gépeknek háttércímkészletek határozhatók meg. Ezután konfigurálja figyelők és szabályok alapján a tartományok, amelyek a saját győződjön meg arról, hogy a webes forgalom érkezik a készletek a megfelelő kiszolgálókat. Ez az oktatóanyag feltételezi, hogy Ön a tulajdonosa több tartományok és felhasználási mintái *www.contoso.com* és *www.fabrikam.com*.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Application Gateway létrehozása
> * Virtuális gépek háttérkiszolgálókhoz létrehozása
> * Hozzon létre háttérkészletek menüpontot a háttérkiszolgálókon
> * Háttér-figyelők létrehozása
> * Útválasztási szabályok létrehozása
> * Hozzon létre egy CNAME rekordot a tartományban

![Többhelyes útválasztási – példa](./media/create-multiple-sites-portal/scenario.png)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-portálon: [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Egy virtuális hálózatot az Ön által létrehozott erőforrások közötti kommunikációra van szükség. Két alhálózat ebben a példában jönnek létre: egyet az Alkalmazásátjáró, míg a másik a háttérkiszolgálókhoz. Az Alkalmazásátjáró létrehozott egyszerre egy virtuális hálózatot is létrehozhat.

1. Kattintson a **új** az Azure portál bal felső sarkában található.
2. Válassza ki **hálózati** majd **Application Gateway** kiemelt listájában.
3. Adja meg ezeket az értékeket az Alkalmazásátjáró:

    - *myAppGateway* – az Alkalmazásátjáró nevét.
    - *myResourceGroupAG* – az új erőforráscsoport.

    ![Új Alkalmazásátjáró létrehozása](./media/create-multiple-sites-portal/application-gateway-create.png)

4. Fogadja el a további beállításoknál az alapértelmezett értékeket, és kattintson a **OK**.
5. Kattintson a **virtuális hálózatot választ**, kattintson a **hozzon létre új**, és ezekkel az értékekkel adja meg a virtuális hálózat:

    - *myVNet* – a virtuális hálózat nevét.
    - *10.0.0.0/16* – a virtuális hálózat címtere.
    - *myAGSubnet* – az alhálózati név.
    - *10.0.0.0/24* – az alhálózati címtartományt.

    ![Virtuális hálózat létrehozása](./media/create-multiple-sites-portal/application-gateway-vnet.png)

6. Kattintson a **OK** a virtuális hálózati és alhálózati létrehozásához.
7. Kattintson a **egy nyilvános IP-cím kiválasztása**, kattintson a **hozzon létre új**, és írja be a nyilvános IP-cím neve. Ebben a példában a nyilvános IP-cím neve *myAGPublicIPAddress*. Fogadja el a további beállításoknál az alapértelmezett értékeket, és kattintson a **OK**.
8. Fogadja el az alapértelmezett értékeket, a figyelő a konfigurációhoz, hagyja a webalkalmazási tűzfal le van tiltva, és kattintson **OK**.
9. Tekintse át a beállításokat az Összegzés lapon, és kattintson **OK** a hálózati erőforrások és az Alkalmazásátjáró létrehozása. Az alkalmazás-átjáró hozható létre, várjon, amíg a telepítés sikeresen befejeződik, mielőtt továbblép a következő szakaszban több percig is eltarthat.

### <a name="add-a-subnet"></a>Adjon hozzá egy alhálózatot

1. Kattintson a **összes erőforrás** a bal oldali menüből, majd **myVNet** erőforrások listából.
2. Kattintson a **alhálózatok**, és kattintson a **alhálózati**.

    ![Alhálózat létrehozása](./media/create-multiple-sites-portal/application-gateway-subnet.png)

3. Adja meg *myBackendSubnet* neveként az alhálózati majd **OK**.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Ebben a példában két virtuális gép az Alkalmazásátjáró háttér-kiszolgálóként használandó hoz létre. IIS ellenőrzése, hogy a forgalom útválasztásához megfelelően van a virtuális gépeken is telepítse.

1. Kattintson az **Új** lehetőségre.
2. Kattintson a **számítási** majd **Windows Server 2016 Datacenter** kiemelt listájában.
3. Adja meg a virtuális gép ezeket az értékeket:

    - *contosoVM* – a virtuális gép nevét.
    - A rendszergazda felhasználóneve: *azureuser*.
    - *Azure123456!* a jelszó.
    - Válassza ki **meglévő**, majd válassza ki *myResourceGroupAG*.

4. Kattintson az **OK** gombra.
5. A virtuális gép méreténél válassza a **DS1_V2** lehetőséget, majd kattintson a **Kiválasztás** gombra.
6. Győződjön meg arról, hogy **myVNet** van kiválasztva a virtuális hálózat és az alhálózat van **myBackendSubnet**. 
7. A rendszerindítási diagnosztika letiltásához kattintson a **Letiltva** elemre.
8. Kattintson az **OK** gombra, majd az összefoglaló lapon ellenőrizze a beállításokat, és kattintson a **Létrehozás** gombra.

### <a name="install-iis"></a>Az IIS telepítése

1. Az interaktív rendszerhéjat, és győződjön meg arról, hogy van-e állítva **PowerShell**.

    ![Egyéni kiterjesztés telepítése](./media/create-multiple-sites-portal/application-gateway-extension.png)

2. A következő parancsot az IIS telepítése a virtuális gépen: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/vhorne/samplescripts/master/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. A második virtuális gép létrehozásához, és a lépéseket, amelyek az imént befejeződött az IIS telepítése. Írja be a neveket a *fabrikamVM* nevét és a Set-AzureRmVMExtension VMName értékénél.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Háttér-címkészletek létrehozása a virtuális gépekkel

1. Kattintson a **összes erőforrás** majd **myAppGateway**.
2. Kattintson a **háttérkészletek**, és kattintson a **Hozzáadás**.
3. Adjon meg egy *contosoPool* , és adja hozzá *contosoVM* használatával **Hozzáadás cél**.

    ![Adja hozzá a háttérkiszolgálókon](./media/create-multiple-sites-portal/application-gateway-multisite-backendpool.png)

4. Kattintson az **OK** gombra.
5. Kattintson a **háttérkészletek** majd **Hozzáadás**.
6. Hozzon létre a *fabrikamPool* rendelkező a *fabrikamVM* imént befejeződött lépéseit követve.

## <a name="create-backend-listeners"></a>Háttér-figyelők létrehozása

1. Kattintson a **figyelői** majd **többhelyes**.
2. Adja meg a figyelő ezeket az értékeket:
    
    - *contosoListener* – a figyelő nevét.
    - *www.contoso.com* -állomás neve példában cserélje le a tartomány nevét.

3. Kattintson az **OK** gombra.
4. Hozzon létre egy második figyelőt nevével *fabrikamListener* és a második tartománynevét használja. Ebben a példában *www.fabrikam.com* szolgál.

## <a name="create-routing-rules"></a>Útválasztási szabályok létrehozása

Szabályok feldolgozása sorrendben szerepelnek, és a forgalom irányítja a rendszer az első függetlenül sajátlagossága figyelembe vétele egyező szabály használatával. Például ha egy szabályt egy alapszintű figyelő és egy többhelyes figyelő mindkét ugyanazt a portot használó szabály, a szabály a többhelyes figyelőjével szerepelnie kell a szabály a vártnak megfelelően működik az alapvető figyelő ahhoz, hogy a többhelyes szabály előtt. 

Ebben a példában két új szabályt hoz létre, és törölje az alapértelmezett szabály, amely az Alkalmazásátjáró létrehozásakor jött létre. 

1. Kattintson a **szabályok** majd **alapvető**.
2. Adja meg *contosoRule* nevét.
3. Válassza ki *contosoListener* a figyelőhöz.
4. Válassza ki *contosoPool* a háttérkészlet.

    ![Elérési út alapú szabály létrehozása](./media/create-multiple-sites-portal/application-gateway-multisite-rule.png)

5. Kattintson az **OK** gombra.
6. Hozzon létre egy második szabály nevének használatával *fabrikamRule*, *fabrikamListener*, és *fabrikamPool*.
7. Törli az alapértelmezett szabályt nevű *Szabály1* kattintson rá, majd **törlése**.

## <a name="create-a-cname-record-in-your-domain"></a>Hozzon létre egy CNAME rekordot a tartományban

Nyilvános IP-címmel az Alkalmazásátjáró létrehozása után lekérni a DNS-címét, és hozzon létre egy CNAME rekordot a tartomány segítségével. A-rekordok használata nem ajánlott, mert a VIP módosíthatja az Alkalmazásátjáró újraindításakor.

1. Kattintson a **összes erőforrás**, és kattintson a **myAGPublicIPAddress**.

    ![Rekord Alkalmazásátjáró DNS-címét](./media/create-multiple-sites-portal/application-gateway-multisite-dns.png)

2. Másolja a DNS-címét, és használja az értékeként egy CNAME rekordban a tartományban.

## <a name="test-the-application-gateway"></a>Az Alkalmazásátjáró tesztelése

1. Adjon meg a tartománynevet a böngésző címsorába. Például a http://www.contoso.com.

    ![Az alkalmazás átjáró contoso hely tesztelése](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. Módosítsa a címet a másik tartományban, és az alábbihoz hasonlót kell megjelennie:

    ![Az alkalmazás átjáró fabrikam hely tesztelése](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan:

> [!div class="checklist"]
> * Application Gateway létrehozása
> * Virtuális gépek háttérkiszolgálókhoz létrehozása
> * Hozzon létre háttérkészletek menüpontot a háttérkiszolgálókon
> * Háttér-figyelők létrehozása
> * Útválasztási szabályok létrehozása
> * Hozzon létre egy CNAME rekordot a tartományban
