---
title: Privát előtéri IP-címmel rendelkező Azure Application Gateway konfigurálása
description: Ez a cikk információt nyújt az Application Gateway konfigurálása magánhálózati előtérbeli IP-címmel
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/26/2019
ms.author: absha
ms.openlocfilehash: cfc63349e20aa6dbef4e0d31e81842d325bd3ec6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58905538"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Application gateway konfigurálása egy belső terheléselosztó (ILB) végponthoz

Az Azure Application Gateway konfigurálható egy internetre irányuló virtuális IP-cím vagy egy belső végpont, amely nem internettel érintkező (az előtérbeli IP-cím egy magánhálózati IP-cím használatával), más néven belső terheléselosztóval (ILB) végponthoz. Konfigurálja az átjárót, előtérbeli, magánhálózati IP-cím használata esetén hasznos belső üzleti alkalmazások, amelyek nem csatlakoznak az internethez. Emellett a többrétegű alkalmazások szolgáltatásai és rétegei számára is hasznos, amelyek egy internettel nem érintkező biztonsági korláton belül vannak, de attól még igényelik az időszeleteléses terheléselosztást, a munkamenet tartós használatát vagy a Secure Sockets Layer (SSL) lezárását.

Ez a cikk végigvezeti az application gateway konfigurálása egy előtérbeli személyes IP-cím az Azure Portalról.

Ez a cikk azt ismerteti, hogyan lehet:

- Az Application Gateway egy magánhálózati előtérbeli IP-konfiguráció létrehozása
- Hozzon létre egy application gateway magánhálózati előtérbeli IP-konfiguráció


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a <https://portal.azure.com> címen.

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az Azure-hoz az erőforrások közötti kommunikációt, hogy hozzon létre egy virtuális hálózat szükséges. Hozzon létre egy új virtuális hálózatot, vagy használjon egy meglévőt. Ebben a példában létrehozunk egy új virtuális hálózatot. Virtuális hálózatot az alkalmazásátjáróval együtt is létrehozhat. Application Gateway-példány külön alhálózatra jönnek létre. Ebben a példában két alhálózattal hoz létre: egyet az application gateway, a másik pedig a háttérkiszolgálókhoz.

1. Kattintson a **új** az Azure portal bal felső sarkában található.
2. Válassza a **Hálózatkezelés**, majd az **Application Gateway** elemet a Kiemeltek listából.
3. Adja meg *myAppGateway* az application gateway neve és *myResourceGroupAG* az új erőforráscsoportnak.
4. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.
5. Kattintson a **virtuális hálózat választása**, kattintson a **új létrehozása**, majd adja meg ezeket az értékeket a virtuális hálózat:
   - myVNet * – a virtuális hálózat neve.
   - 10.0.0.0/16* – a virtuális hálózat címterét.
   - Az alhálózat neve *myAGSubnet*.
   - Az alhálózat címtere *10.0.0.0/24*.  
     ![private-frontendip-1](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)
6. A virtuális hálózat és az alhálózat létrehozásához kattintson az **OK** gombra.
7. Válassza ki az előtérbeli IP-konfiguráció: magánjellegű, és alapértelmezés szerint a dinamikus IP-cím hozzárendelése. Az első rendelkezésre álló címet a választott alhálózat előtérbeli IP-címet kap.
8. Ha szeretne egy magánhálózati IP-cím az alhálózat címtartományán (statikus foglalási) közül választhat, jelölje be a **egy adott privát IP-cím választása** , és adja meg az IP-címet.
   > [!NOTE]
   > Miután lefoglalt, az IP-cím típusa (statikus vagy dinamikus) később nem módosítható.
9. Válassza ki a figyelő konfigurációját tartalmazza a protokollt és a portot, a WAF konfigurálása (ha szükséges), és kattintson az OK gombra.
    ![private-frontendip-2](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-2.png)
10. Tekintse át a beállításokat az Összegzés lapon, és kattintson **OK** a hálózati erőforrások és az application gateway létrehozásához. Az application gateway hozhatók létre, várjon, amíg az üzembe helyezés sikeresen befejeződik, mielőtt továbblép a következő szakaszban több percig is eltarthat.

## <a name="add-backend-pool"></a>Háttérkészlet hozzáadása

A háttérkészlet irányíthatja a kérelmeket a háttérkiszolgálók, amely a kérés kiszolgálása lesz használatos. Háttérbeli hálózati adapterek, a virtual machine scale sets, nyilvános IP-címek állhat, belső IP-címek, teljesen minősített neve (FQDN), és több-bérlős háttéralkalmazások hasonlóan az Azure App Service-ben. Ebben a példában használjuk virtuális gépek, a cél-háttérrendszert. Azt is vagy használjon létező virtuális gépeket, vagy újakat hoz létre. Ebben a példában hozunk létre két virtuális gépet használó Azure háttérkiszolgálóiként az application gateway számára. Ehhez a következő történik:

1. 2 új virtuális Gépeket létre *myVM* és *myVM2*háttérkiszolgálóiként használandó.
2. IIS telepítése a virtuális gépeket, ellenőrizze, hogy az application gateway létrehozása sikeres volt.
3. A háttérkiszolgálók hozzáadásához a háttérkészlethez.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Kattintson az **Új** lehetőségre.
2. Kattintson a **számítási** majd **Windows Server 2016 Datacenter** a kiemelt lista.
3. Adja meg a következő értékeket a virtuális gép számára:
   - A virtuális gép neve *myVM*.
   - A rendszergazda felhasználóneve: *azureuser*.
   - A jelszó *Azure123456!* .
   - Válassza a **Meglévő használata**, majd a *myResourceGroupAG* lehetőséget.
4. Kattintson az **OK** gombra.
5. Válassza ki **DS1_V2** méretét, a virtuális gépet, majd kattintson **kiválasztása**.
6. Győződjön meg róla, hogy virtuális hálózatként a **myVNet**, alhálózatként pedig a **myBackendSubnet** van kiválasztva.
7. A rendszerindítási diagnosztika letiltásához kattintson a **Letiltva** elemre.
8. Kattintson az **OK** gombra, majd az összefoglaló lapon ellenőrizze a beállításokat, és kattintson a **Létrehozás** gombra.

### <a name="install-iis"></a>Az IIS telepítése

1. Nyissa meg az interaktív felületet, és győződjön meg róla, hogy a **PowerShell** van beállítva.
    ![private-frontendip-3](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. Futtassa a következő parancsot az IIS a virtuális gépen való telepítéséhez:

   ```azurepowershell
   Set-AzVMExtension `
   
     -ResourceGroupName myResourceGroupAG `
   
     -ExtensionName IIS `
   
     -VMName myVM `
   
     -Publisher Microsoft.Compute `
   
     -ExtensionType CustomScriptExtension `
   
     -TypeHandlerVersion 1.4 `
   
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' -Location EastUS  ```



3. Create a second virtual machine and install IIS using the steps that you just finished. Enter myVM2 for its name and for VMName in Set-AzVMExtension.

### Add backend servers to backend pool

1. Click **All resources**, and then click **myAppGateway**.
2. Click **Backend pools**. A default pool was automatically created with the application gateway. Click **appGatewayBackendPool**.
3. Click **Add target** to add each virtual machine that you created to the backend pool.
   ![private-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
4. Click **Save.**

## Test the application gateway

1. Check your frontend IP that got assigned by clicking the **Frontend IP Configurations** blade in the portal.
    ![private-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Copy the private IP address, and then paste it into the address bar of your browser of a VM in the same VNet or on-premises which has connectivity to this VNet and try to access the Application Gateway.

## Next steps

In this tutorial, you learned how to:

- Create a private frontend IP configuration for an Application Gateway
- Create an application gateway with private frontend IP configuration

If you want to monitor the health of your backend, see [Application Gateway Diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
