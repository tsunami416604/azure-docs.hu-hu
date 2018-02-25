---
title: "Hozzon létre egy alkalmazás URL-cím elérési út-alapú útválasztási szabályokat - Azure-portál |} Microsoft Docs"
description: "Útmutató: az URL-cím elérési út-alapú útválasztási szabályok létrehozása az Alkalmazásátjáró és virtuálisgép-méretezési beállítása az Azure portál használatával."
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: davidmu
ms.openlocfilehash: 62063c42ab15a071a4500417a5d8adf6bfeac97f
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Hozzon létre egy alkalmazás elérési útja-alapú útválasztási szabályokat az Azure portál használatával

Az Azure portál segítségével konfigurálhatja [URL-cím elérési út-alapú útválasztási szabályok](application-gateway-url-route-overview.md) létrehozásakor egy [Alkalmazásátjáró](application-gateway-introduction.md). Ebben az oktatóanyagban létrehoz virtuális gépeknek háttérkészletek menüpontot. Ezután hozzon létre útválasztási szabályokat, győződjön meg arról, hogy a webes forgalom érkezik a készletek a megfelelő kiszolgálókat.

Ebből a cikkből megismerheti, hogyan:

> [!div class="checklist"]
> * Application Gateway létrehozása
> * Virtuális gépek háttérkiszolgálókhoz létrehozása
> * Hozzon létre háttérkészletek menüpontot a háttérkiszolgálókon
> * Háttér-figyelő létrehozása
> * Elérési út-alapú útválasztási szabály létrehozása

![URL-cím útválasztási – példa](./media/application-gateway-create-url-route-portal/scenario.png)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure portálon, a [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Egy virtuális hálózatot az Ön által létrehozott erőforrások közötti kommunikációra van szükség. Két alhálózat ebben a példában jönnek létre: egyet az Alkalmazásátjáró, míg a másik a háttérkiszolgálókhoz. Az Alkalmazásátjáró létrehozott egyszerre egy virtuális hálózatot is létrehozhat.

1. Kattintson a **új** az Azure portál bal felső sarkában található.
2. Válassza ki **hálózati** majd **Application Gateway** kiemelt listájában.
3. Adja meg ezeket az értékeket az Alkalmazásátjáró:

    - *myAppGateway* – az Alkalmazásátjáró nevét.
    - *myResourceGroupAG* – az új erőforráscsoport.

    ![Új Alkalmazásátjáró létrehozása](./media/application-gateway-create-url-route-portal/application-gateway-create.png)

4. Fogadja el a további beállításoknál az alapértelmezett értékeket, és kattintson a **OK**.
5. Kattintson a **virtuális hálózatot választ**, kattintson a **hozzon létre új**, és ezekkel az értékekkel adja meg a virtuális hálózat:

    - *myVNet* – a virtuális hálózat nevét.
    - *10.0.0.0/16* – a virtuális hálózat címtere.
    - *myAGSubnet* – az alhálózati név.
    - *10.0.0.0/24* – az alhálózati címtartományt.

    ![Virtuális hálózat létrehozása](./media/application-gateway-create-url-route-portal/application-gateway-vnet.png)

6. Kattintson a **OK** a virtuális hálózati és alhálózati létrehozásához.
7. Kattintson a **egy nyilvános IP-cím kiválasztása**, kattintson a **hozzon létre új**, és írja be a nyilvános IP-cím neve. Ebben a példában a nyilvános IP-cím neve *myAGPublicIPAddress*. Fogadja el a további beállításoknál az alapértelmezett értékeket, és kattintson a **OK**.
8. Fogadja el az alapértelmezett értékeket, a figyelő a konfigurációhoz, hagyja a webalkalmazási tűzfal le van tiltva, és kattintson **OK**.
9. Tekintse át a beállításokat az Összegzés lapon, és kattintson **OK** a hálózati erőforrások és az Alkalmazásátjáró létrehozása. Az alkalmazás-átjáró hozható létre, várjon, amíg a telepítés sikeresen befejeződik, mielőtt továbblép a következő szakaszban több percig is eltarthat.

### <a name="add-a-subnet"></a>Adjon hozzá egy alhálózatot

1. Kattintson a **összes erőforrás** a bal oldali menüből, majd **myVNet** erőforrások listából.
2. Kattintson a **alhálózatok**, és kattintson a **alhálózati**.

    ![Alhálózat létrehozása](./media/application-gateway-create-url-route-portal/application-gateway-subnet.png)

3. Adja meg *myBackendSubnet* neveként az alhálózati majd **OK**.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Ebben a példában az Alkalmazásátjáró háttér-kiszolgálóként használandó három virtuális gépet hoz létre. Is telepíteni az IIS ellenőrizze, hogy az Alkalmazásátjáró sikeresen létrejött-e a virtuális gépeken.

1. Kattintson az **Új** lehetőségre.
2. Kattintson a **számítási** majd **Windows Server 2016 Datacenter** kiemelt listájában.
3. Adja meg a virtuális gép ezeket az értékeket:

    - *myVM1* – a virtuális gép nevét.
    - *azureuser* – a rendszergazdai felhasználónevet.
    - *Azure123456!* a jelszó.
    - Válassza ki **meglévő**, majd válassza ki *myResourceGroupAG*.

4. Kattintson az **OK** gombra.
5. Válassza ki **DS1_V2** a virtuális gépet, majd kattintson a méretét **válasszon**.
6. Győződjön meg arról, hogy **myVNet** van kiválasztva a virtuális hálózat és az alhálózat van **myBackendSubnet**. 
7. Kattintson a **letiltott** letiltani a rendszerindítási diagnosztika.
8. Kattintson a **OK**, tekintse át a beállításokat az Összegzés lapon, és kattintson a **létrehozása**.

### <a name="install-iis"></a>Az IIS telepítése

1. Az interaktív rendszerhéjat, és győződjön meg arról, hogy van-e állítva **PowerShell**.

    ![Egyéni kiterjesztés telepítése](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. A következő parancsot az IIS telepítése a virtuális gépen: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/davidmu1/samplescripts/master/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName myVM1 `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Hozzon létre két további virtuális gépeket, és a lépéseket, amelyek az imént befejeződött az IIS telepítése. Írja be a neveket a *myVM2* és *myVM3* nevét és a Set-AzureRmVMExtension VMName értékeit.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Háttér-címkészletek létrehozása a virtuális gépekkel

1. Kattintson a **összes erőforrás** majd **myAppGateway**.
2. Kattintson a **háttérkészletek**. Alapértelmezett címkészlet automatikusan jött létre az Alkalmazásátjáró. Click **appGatewayBackendPool**.
3. Kattintson a **Hozzáadás cél** hozzáadása *myVM1* appGatewayBackendPool számára.

    ![Adja hozzá a háttérkiszolgálókon](./media/application-gateway-create-url-route-portal/application-gateway-backend.png)

4. Kattintson a **Save** (Mentés) gombra.
5. Kattintson a **háttérkészletek** majd **Hozzáadás**.
6. Adjon meg egy *imagesBackendPool* , és adja hozzá *myVM2* használatával **Hozzáadás cél**.
7. Kattintson az **OK** gombra.
8. Kattintson a **Hozzáadás** újra nevű másik háttérkészletnek hozzáadandó *videoBackendPool* , és adja hozzá *myVM3* rá.

## <a name="create-a-backend-listener"></a>Háttér-figyelő létrehozása

1. Kattintson a **figyelői** , és kattintson a **alapvető**.
2. Adja meg *myBackendListener* névként *myFrontendPort* elülső rétegbeli portot nevét, majd *8080* , a portot a figyelőhöz.
3. Kattintson az **OK** gombra.

## <a name="create-a-path-based-routing-rule"></a>Elérési út-alapú útválasztási szabály létrehozása

1. Kattintson a **szabályok** majd **elérési alapú**.
2. Adja meg *rule2* nevét.
3. Adja meg *képek* az első elérési út neveként. Adja meg */images/** az elérési utat. Válassza ki **imagesBackendPool** a háttérkészlet.
4. Adja meg *videó* a második elérési út neveként. Adja meg */video/** az elérési utat. Válassza ki **videoBackendPool** a háttérkészlet.

    ![Elérési út alapú szabály létrehozása](./media/application-gateway-create-url-route-portal/application-gateway-route-rule.png)

5. Kattintson az **OK** gombra.

## <a name="test-the-application-gateway"></a>Az Alkalmazásátjáró tesztelése

1. Kattintson a **összes erőforrás**, és kattintson a **myAGPublicIPAddress**.

    ![Rekord alkalmazás átjáró nyilvános IP-címe](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Másolja a nyilvános IP-címet, és illessze be a böngésző címsorába. Például http://http: / / 40.121.222.19.

    ![Az alkalmazás átjáró alap URL-cím tesztelése](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

3. Módosítsa az URL-címet http://&lt;ip-cím&gt;: 8080/video/test.htm, és &lt;ip-cím&gt; az IP-címet, és meg kell megjelennie az alábbihoz hasonlót:

    ![Az alkalmazás átjáró képek URL tesztelése](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

4. Módosítsa az URL-címet http://&lt;ip-cím&gt;: 8080/video/test.htm, és &lt;ip-cím&gt; az IP-címet, és meg kell megjelennie az alábbihoz hasonlót:

    ![Az alkalmazás átjáró Videó URL tesztelése](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan

> [!div class="checklist"]
> * Application Gateway létrehozása
> * Virtuális gépek háttérkiszolgálókhoz létrehozása
> * Hozzon létre háttérkészletek menüpontot a háttérkiszolgálókon
> * Háttér-figyelő létrehozása
> * Elérési út-alapú útválasztási szabály létrehozása

További információt a alkalmazásátjárót és a kapcsolódó erőforrások, továbbra is a útmutatókat.
