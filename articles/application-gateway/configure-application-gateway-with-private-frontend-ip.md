---
title: Belső terheléselosztó (ILB) végpontjának konfigurálása
titleSuffix: Azure Application Gateway
description: Ez a cikk tájékoztatást nyújt arról, hogyan konfigurálható a Application Gateway privát előtérbeli IP-címmel
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 01/30/2020
ms.author: victorh
ms.openlocfilehash: c49c37ced4a5d5cc7cdde0737b889aad3b538f7f
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899006"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Application Gateway konfigurálása belső terheléselosztó (ILB) végponttal

Az Azure Application Gateway egy internetre irányuló VIP-sel vagy egy belső végponttal konfigurálható, amely nem érhető el az interneten. A belső végpontok magánhálózati IP-címet használnak a előtér számára, amely *belső terheléselosztó (ILB) végpontként*is ismert.

Az átjáró saját előtér-magánhálózati IP-címmel való konfigurálása olyan belső üzletági alkalmazások esetében hasznos, amelyek nem érhetők el az interneten. A többrétegű alkalmazásokban olyan szolgáltatásokhoz és szintekhez is hasznos, amelyek olyan biztonsági határban vannak, amely nem érhető el az interneten, de továbbra is a ciklikus multiplexelés, a munkamenetek és a SSL (SSL) leállítása szükséges.

Ez a cikk végigvezeti az Application Gateway előtér-magánhálózati IP-címmel történő konfigurálásának lépésein a Azure Portal használatával.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be a Azure Portalba <https://portal.azure.com>kV-ban

## <a name="create-an-application-gateway"></a>Alkalmazásátjáró létrehozása

Ahhoz, hogy az Azure kommunikáljon a létrehozott erőforrások között, szüksége van egy virtuális hálózatra. Hozzon létre egy új virtuális hálózatot, vagy használjon egy meglévőt. Ebben a példában egy új virtuális hálózatot hozunk létre. Virtuális hálózatot az alkalmazásátjáróval együtt is létrehozhat. Application Gateway példányok külön alhálózatokban jönnek létre. Ebben a példában két alhálózatot hoz létre: egyet az Application Gateway számára, és egy másikat a háttér-kiszolgálók számára.

1. Bontsa ki a portál menüt, majd válassza az **erőforrás létrehozása**lehetőséget.
2. Válassza a **Hálózatkezelés**, majd az **Application Gateway** elemet a Kiemeltek listából.
3. Adja meg a *myAppGateway* nevet az Application Gateway és az új erőforráscsoport *myResourceGroupAG* nevéhez.
4. A régió területen válassza az USA **középső** **régióját**.
5. A **szint**mezőben válassza a **standard**lehetőséget.
6. A **virtuális hálózat konfigurálása** területen válassza az **új létrehozása**lehetőséget, majd adja meg a virtuális hálózat következő értékeit:
   - A virtuális hálózat neve *myVNet*.
   - A virtuális hálózat címtere *10.0.0.0/16*.
   - Az alhálózat neve *myAGSubnet*.
   - Az alhálózat címtere *10.0.0.0/24*.
   - *myBackendSubnet* – a háttér alhálózatának neveként.
   - *10.0.1.0/24* – a háttérbeli alhálózat címterület számára.

    ![Virtuális hálózat létrehozása](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. A virtuális hálózat és az alhálózat létrehozásához kattintson **az OK gombra** .
7. Válassza a **Next (tovább): frontends**elemet.
8. Az előtérbeli **IP-cím típusa**beállításnál válassza a **magánjellegű**lehetőséget.

   Alapértelmezés szerint ez egy dinamikus IP-cím-hozzárendelés. A konfigurált alhálózat első elérhető címe az előtér-IP-címként van hozzárendelve.
   > [!NOTE]
   > A lefoglalt IP-cím típusa (statikus vagy dinamikus) később nem módosítható.
9. Válassza a Next (tovább) lehetőséget **: háttérrendszer**.
10. Válassza **a háttérbeli készlet hozzáadása**lehetőséget.
11. A **név**mezőbe írja be a következőt: *appGatewayBackendPool*.
12. Ha a **háttér-készletet célok nélkül**kívánja hozzáadni, válassza az **Igen**lehetőséget. Később hozzáadja a célokat.
13. Válassza a **Hozzáadás** lehetőséget.
14. Válassza a **Tovább: konfigurálás**lehetőséget.
15. Az **útválasztási szabályok**területen válassza **a szabály hozzáadása**elemet.
16. A **szabály neve**mezőbe írja be a következőt: *Rrule-01*.
17. A **figyelő neve**mezőbe írja be a következőt: *Listener-01*.
18. Előtér **-IP**esetén válassza a **magánjellegű**lehetőséget.
19. Fogadja el a fennmaradó alapértékeket, és válassza a **háttérbeli célok** fület.
20. A **cél típusa**beállításnál válassza a **háttér-készlet**lehetőséget, majd válassza a **appGatewayBackendPool**lehetőséget.
21. **Http-beállítás**esetén válassza az **új létrehozása**lehetőséget.
22. A **http-beállítás neve**mezőbe írja be a következőt: *http-Setting-01*.
23. A **háttérrendszer protokoll**esetében válassza a **http**lehetőséget.
24. A **backend port**esetében írja be a *80*értéket.
25. Fogadja el a fennmaradó alapértékeket, és válassza a **Hozzáadás**lehetőséget.
26. Az **útválasztási szabály hozzáadása** lapon válassza a **Hozzáadás**lehetőséget.
27. Kattintson a **Tovább gombra: címkék**.
28. Válassza a **Next (tovább): felülvizsgálat + létrehozás**elemet.
29. Tekintse át a beállításokat az összefoglalás lapon, majd válassza a **Létrehozás** lehetőséget a hálózati erőforrások és az Application Gateway létrehozásához. Az alkalmazásátjáró létrehozása több percig is eltarthat. Várjon, amíg a telepítés sikeresen befejeződik, mielőtt továbblép a következő szakaszra.

## <a name="add-backend-pool"></a>Háttér-készlet hozzáadása

A háttér-készlet arra szolgál, hogy a kérelmeket a kérést kiszolgáló háttér-kiszolgálókra irányítsa. A háttérrendszer a hálózati adapterek, a virtuálisgép-méretezési csoportok, a nyilvános IP-címek, a belső IP-címek, a teljes tartománynevek (FQDN) és a több-bérlős háttér-végpontok, például a Azure App Service tagjai lehetnek. Ebben a példában a virtuális gépeket célként használt háttérként használjuk. Használhat meglévő virtuális gépeket, vagy újakat is létrehozhat. Ebben a példában két virtuális gépet hoz létre, amelyeket az Azure háttér-kiszolgálóként használ az Application Gateway számára.

Ehhez tegye a következőket:

1. Hozzon létre két új, háttér-kiszolgálóként használt virtuális gépet, *myVM* és *myVM2*.
2. Telepítse az IIS-t a virtuális gépeken annak ellenőrzéséhez, hogy az Application Gateway sikeresen létrejött-e.
3. Adja hozzá a háttér-kiszolgálókat a háttér-készlethez.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Válassza **az erőforrás létrehozása**lehetőséget.
2. Válassza a **számítás** , majd a **virtuális gép**lehetőséget.
4. Adja meg a következő értékeket a virtuális gép számára:
   - válassza az myResourceGroupAGlehetőséget.
   - *myVM* – a **virtuális gép neve**.
   - A **rendszerképhez**válassza a **Windows Server 2019 Datacenter** lehetőséget.
   - *azureadmin* – a **felhasználónévhez**.
   - A jelszó *Azure123456!* a **jelszóhoz**.
5. Fogadja el a fennmaradó alapértékeket, és válassza a **Tovább: lemezek**lehetőséget.
6. Fogadja el az alapértelmezett értékeket, és válassza a **Tovább: hálózatkezelés**lehetőséget.
7. Győződjön meg róla, hogy virtuális hálózatként a **myVNet**, alhálózatként pedig a **myBackendSubnet** van kiválasztva.
8. Fogadja el a fennmaradó alapértékeket, és válassza a **Tovább: kezelés**lehetőséget.
9. A rendszerindítási diagnosztika letiltásához válassza a **ki** lehetőséget.
10. Fogadja el a fennmaradó alapértékeket, és válassza a **Tovább: speciális**lehetőséget.
11. Kattintson a **Tovább gombra: címkék**.
12. Válassza a **Next (tovább): felülvizsgálat + létrehozás**elemet.
13. Tekintse át a beállításokat az összefoglalás lapon, majd válassza a **Létrehozás**lehetőséget. A virtuális gép létrehozása több percet is igénybe vehet. Várjon, amíg a telepítés sikeresen befejeződik, mielőtt továbblép a következő szakaszra.

### <a name="install-iis"></a>Az IIS telepítése

1. Nyissa meg a Cloud Shellt, és győződjön meg arról, hogy a **PowerShell**-re van beállítva.
    ![Private-frontendip-3](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
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



3. Hozzon létre egy második virtuális gépet, és telepítse az IIS-t az imént befejezett lépésekkel. Adja meg a myVM2 nevét és a VMName a set-AzVMExtension.

### <a name="add-backend-servers-to-backend-pool"></a>Háttér-kiszolgálók hozzáadása a háttérbeli készlethez

1. Válassza a **minden erőforrás**lehetőséget, majd válassza a **myAppGateway**lehetőséget.
2. Válassza ki a **háttérbeli készletek**elemet. Válassza a **appGatewayBackendPool**lehetőséget.
3. A **cél típusa** területen válassza a **virtuális gép** lehetőséget, és a **cél**területen válassza ki a myVM társított vNIC.
4. MyVM2 hozzáadásához ismételje meg a műveletet.
   ![Private-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. Válassza a **Mentés lehetőséget.**

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

1. Győződjön meg arról, hogy az előtérbeli IP-cím hozzá van rendelve, a portál előtér **IP-konfigurációk** lapjára kattintva.
    ![Private-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Másolja a magánhálózati IP-címet, majd illessze be a böngésző címsorába egy olyan virtuális gépen, amely az adott VNet kapcsolódik, és próbálja meg elérni Application Gateway a VNet.

## <a name="next-steps"></a>Következő lépések

Ha figyelni szeretné a háttér állapotát, tekintse meg a [háttér állapotáról és a diagnosztikai naplókat a Application Gateway](application-gateway-diagnostics.md).
