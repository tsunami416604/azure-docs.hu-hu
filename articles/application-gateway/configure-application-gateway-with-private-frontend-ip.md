---
title: Belső terheléselosztó (ILB) végpontkonfigurálása
titleSuffix: Azure Application Gateway
description: Ez a cikk az Application Gateway privát előtér-IP-címmel történő konfigurálásáról nyújt tájékoztatást
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 01/30/2020
ms.author: victorh
ms.openlocfilehash: 809274aba35e9607aeacf7c6483ec3d10f899667
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312369"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Alkalmazásátjáró konfigurálása belső terheléselosztó (ILB) végponttal

Az Azure Application Gateway konfigurálható egy internetre néző VIP vagy egy belső végpont, amely nincs kitéve az interneten. A belső végpont az előtér privát IP-címét használja, amely *belső terheléselosztó (ILB) végpontként*is ismert.

Az átjáró konfigurálása előtér-privát IP-cím használatával hasznos az internetnek nem elérhető belső üzletági alkalmazások esetében. Ez olyan többrétegű alkalmazásokon belüli szolgáltatások és rétegek esetében is hasznos, amelyek olyan biztonsági határon vannak, amely nincs kitéve az internetnek, de továbbra is ciklikus multiplexeléses terheléselosztást, munkamenet-ragacsosságot vagy Transport Layer Security (TLS), korábbi nevén Secure Sockets Layer (SSL) végződést igényelnek.

Ez a cikk végigvezeti a lépéseket egy előtér-privát IP-címmel rendelkező alkalmazásátjáró konfigurálásához az Azure Portal használatával.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a <https://portal.azure.com> webhelyen

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Ahhoz, hogy az Azure kommunikáljon a létrehozott erőforrások között, virtuális hálózatra van szüksége. Létrehozhat egy új virtuális hálózatot, vagy használhat egy meglévőt. Ebben a példában új virtuális hálózatot hoz létre. Virtuális hálózatot az alkalmazásátjáróval együtt is létrehozhat. Az Alkalmazásátjáró-példányok külön alhálózatokban jönnek létre. Ebben a példában két alhálózatot hoz létre: egyet az alkalmazásátjáróhoz, egyet pedig a háttérkiszolgálókhoz.

1. Bontsa ki a portál menüt, és válassza **az Erőforrás létrehozása**lehetőséget.
2. Válassza a **Hálózatkezelés**, majd az **Application Gateway** elemet a Kiemeltek listából.
3. Adja meg a *myAppGateway* az alkalmazásátjáró nevét és *a myResourceGroupAG* az új erőforráscsoporthoz.
4. A **Régió**csoportban válassza az **(USA)Usa középső régióját.**
5. A **Szint csoportban**válassza a **Normál**lehetőséget.
6. A **Virtuális hálózat konfigurálása** csoportban válassza **az Új létrehozása**lehetőséget, majd adja meg ezeket az értékeket a virtuális hálózathoz:
   - A virtuális hálózat neve *myVNet*.
   - A virtuális hálózat címtere *10.0.0.0/16*.
   - Az alhálózat neve *myAGSubnet*.
   - Az alhálózat címtere *10.0.0.0/24*.
   - *myBackendSubnet* – a háttérbeli alhálózat nevéhez.
   - *10.0.1.0/24* - a háttér-alhálózat címteréhez.

    ![Virtuális hálózat létrehozása](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. A virtuális hálózat és alhálózat létrehozásához válassza az **OK gombot.**
7. Válassza a **Tovább:Előtér-rendszerek lehetőséget.**
8. Az **előtér IP-cím típusa esetén**válassza a **Privát**lehetőséget.

   Alapértelmezés szerint ez egy dinamikus IP-cím-hozzárendelés. A konfigurált alhálózat első elérhető címe előtér-IP-címként van hozzárendelve.
   > [!NOTE]
   > A lefoglalást követően az IP-cím típusa (statikus vagy dinamikus) később nem módosítható.
9. Válassza a **Tovább:Háttérrendszerek**lehetőséget.
10. Válassza **a Háttérkészlet hozzáadása lehetőséget.**
11. A **Name (Név)** mezőbe írja be *az appGatewayBackendPool nevet.*
12. A **Háttérkészlet hozzáadása célok nélkül**lehetőséget válassza az **Igen**lehetőséget. Később hozzáadja a célpontokat.
13. Válassza a **Hozzáadás** lehetőséget.
14. Válassza a **Tovább:Configuration**lehetőséget.
15. Az **Útválasztás imperátus szabályok**csoportban válassza **a Szabály hozzáadása**lehetőséget.
16. A **Szabály neve**mezőbe írja be az *Rrule-01 nevet.*
17. A **Figyelő neve**mezőbe írja be a *Figyelő-01 nevet.*
18. Az **előtér IP-cím éhezése**esetén válassza **a Privát**lehetőséget.
19. Fogadja el a fennmaradó alapértelmezett értékeket, és válassza a **háttérrendszer-célok** lapot.
20. A **Céltípus mezőben**válassza **a Háttérkészlet**lehetőséget, majd válassza **az appGatewayBackendPool elemet.**
21. **Http-beállítás esetén**válassza **az Új létrehozása lehetőséget.**
22. **A HTTP-beállítás nevéhez**írja be a *http-setting-01 nevet.*
23. Háttérrendszer **protokoll esetén**válassza a **HTTP**lehetőséget.
24. **Háttérport**esetén írja be a *80-as*beírást.
25. Fogadja el a fennmaradó alapértelmezett értékeket, és válassza a **Hozzáadás gombot.**
26. Az **Útválasztási szabály hozzáadása** lapon válassza a **Hozzáadás**lehetőséget.
27. Válassza a **Tovább lehetőséget: Címkék**.
28. Válassza a **Tovább lehetőséget: Véleményezés + létrehozás**.
29. Tekintse át az összesítő lap beállításait, majd a **Létrehozás gombra** a hálózati erőforrások és az alkalmazásátjáró létrehozásához. Az alkalmazásátjáró létrehozása több percig is eltarthat. Várja meg, amíg a központi telepítés sikeresen befejeződik, mielőtt továbblépne a következő szakaszra.

## <a name="add-backend-pool"></a>Háttérkészlet hozzáadása

A háttérkészlet a kérelmek továbbítására szolgál a kérést kiszolgáló háttérkiszolgálókhoz. A háttérprogram hálózati adapterekből, virtuálisgép-méretezési készletekből, nyilvános IP-címekből, belső IP-címekből, teljesen minősített tartománynevekből (FQDN) és több-bérlős háttérrendszerekből, például az Azure App Service-ből állhat. Ebben a példában a virtuális gépeket használja célháttérként. Használhatja a meglévő virtuális gépeket, vagy újakat hozhat létre. Ebben a példában két virtuális gépet hoz létre, amelyeket az Azure az alkalmazásátjáró háttérkiszolgálóiként használ.

Ehhez:

1. Hozzon létre két új virtuális gépet, *a myVM-et* és *a myVM2-t,* amelyek háttérkiszolgálóként használatosak.
2. Telepítse az IIS-t a virtuális gépekre az alkalmazásátjáró sikeres létrehozásának ellenőrzéséhez.
3. Adja hozzá a háttérkiszolgálókat a háttérkészlethez.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Válassza az **Erőforrás létrehozása** lehetőséget.
2. Válassza **a Számítás,** majd **a Virtuális gép**lehetőséget.
4. Adja meg a következő értékeket a virtuális gép számára:
   - válassza a *myResourceGroupAG* **erőforráscsoporthoz**lehetőséget.
   - *myVM* - a **virtuális gép neve**.
   - Válassza a **Windows Server 2019 Datacenter** for Image **(Kép)** adatbázisát.
   - *azureadmin* - a **felhasználónévhez**.
   - A jelszó *Azure123456!* a **jelszóhoz.**
5. Fogadja el a fennmaradó alapértelmezett értékeket, és válassza **a Tovább : Lemezek**lehetőséget.
6. Az alapértelmezett értékek elfogadása, és válassza a **Tovább : Hálózat lehetőséget.**
7. Győződjön meg róla, hogy virtuális hálózatként a **myVNet**, alhálózatként pedig a **myBackendSubnet** van kiválasztva.
8. Fogadja el a fennmaradó alapértelmezett értékeket, és válassza **a Tovább : Kezelés**lehetőséget.
9. A **rendszerindítási** diagnosztika letiltásához válassza a Ki lehetőséget.
10. Fogadja el a fennmaradó alapértelmezett értékeket, és válassza **a Tovább : Speciális**lehetőséget.
11. Válassza a **Tovább lehetőséget : Címkék**.
12. Válassza a **Tovább lehetőséget : Véleményezés + létrehozás**lehetőséget.
13. Tekintse át az összesítő lap beállításait, és válassza a **Létrehozás gombot.** A virtuális gép létrehozása több percig is eltarthat. Várja meg, amíg a központi telepítés sikeresen befejeződik, mielőtt továbblépne a következő szakaszra.

### <a name="install-iis"></a>Az IIS telepítése

1. Nyissa meg a Cloud Shellt, és győződjön meg arról, hogy a **PowerShell**van beállítva.
    ![privát-frontendip-3](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. Futtassa a következő parancsot az IIS a virtuális gépen való telepítéséhez:

   ```azurepowershell
   Set-AzVMExtension `
   
     -ResourceGroupName myResourceGroupAG `
   
     -ExtensionName IIS `
   
     -VMName myVM `
   
     -Publisher Microsoft.Compute `
   
     -ExtensionType CustomScriptExtension `
   
     -TypeHandlerVersion 1.4 `

     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `

     -Location CentralUS `

   ```



3. Hozzon létre egy második virtuális gépet, és telepítse az IIS-t az imént befejezett lépésekkel. Adja meg a myVM2 nevet és a VMName értéket a Set-AzVMExtension-ben.

### <a name="add-backend-servers-to-backend-pool"></a>Háttérkiszolgálók hozzáadása a háttérkészlethez

1. Válassza **az Összes erőforrás**lehetőséget, majd a **myAppGateway**lehetőséget.
2. Válassza **a háttérkészletek lehetőséget**. Válassza **az appGatewayBackendPool lehetőséget.**
3. A **Céltípus csoportban** válassza a **Virtuális gép** és a **Cél**csoportban válassza ki a myVM-hez társított vNIC-et.
4. Ismételje meg a műveletet a MyVM2 hozzáadásához.
   ![privát-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. válassza a **Mentés lehetőséget.**

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

1. Ellenőrizze az előtér IP-címet, amely hozzárendelt kattintva a **frontend IP-konfigurációk** lap a portálon.
    ![privát-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Másolja a privát IP-címet, majd illessze be a böngésző címsorába egy virtuális gép ugyanabban a virtuális hálózatban vagy a helyszíni, amely kapcsolattal rendelkezik a virtuális hálózat, és próbálja meg elérni az Application Gateway.

## <a name="next-steps"></a>További lépések

Ha a háttérrendszer állapotát szeretné figyelni, olvassa el [az Application Gateway háttér-állapot- és diagnosztikai naplói című témakört.](application-gateway-diagnostics.md)
