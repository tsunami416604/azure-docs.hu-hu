---
title: Belső terheléselosztó (ILB) végpontjának konfigurálása
titleSuffix: Azure Application Gateway
description: Ez a cikk tájékoztatást nyújt arról, hogyan konfigurálható a Application Gateway privát előtérbeli IP-címmel
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: a9e3150a5382e4d690ddf66c43bbe51e125509d3
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075219"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Application Gateway konfigurálása belső terheléselosztó (ILB) végponttal

Az Azure Application Gateway konfigurálható egy internetre irányuló virtuális IP-címmel vagy egy belső végponttal, amely nem érhető el az interneten (a előtéri IP-cím magánhálózati IP-címének használatával), más néven belső terheléselosztó (ILB) végpont. Az átjáró saját előtér-magánhálózati IP-címmel való konfigurálása olyan belső üzletági alkalmazások esetében hasznos, amelyek nem érhetők el az interneten. Emellett a többrétegű alkalmazások szolgáltatásai és rétegei számára is hasznos, amelyek egy internettel nem érintkező biztonsági korláton belül vannak, de attól még igényelik az időszeleteléses terheléselosztást, a munkamenet tartós használatát vagy a Secure Sockets Layer (SSL) lezárását.

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy Application Gateway felületet magánhálózati IP-címmel az Azure Portalon.

Ez a cikk azt ismerteti, hogyan lehet:

- Privát előtérbeli IP-konfiguráció létrehozása Application Gatewayhoz
- Application Gateway létrehozása privát előtér-IP-konfigurációval


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a <https://portal.azure.com> címen.

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Ahhoz, hogy az Azure kommunikáljon a létrehozott erőforrások között, szüksége van egy virtuális hálózatra. Hozzon létre egy új virtuális hálózatot, vagy használjon egy meglévőt. Ebben a példában egy új virtuális hálózatot hozunk létre. Virtuális hálózatot az alkalmazásátjáróval együtt is létrehozhat. Application Gateway példányok külön alhálózatokban jönnek létre. Ebben a példában két alhálózatot hoz létre: egyet az Application Gateway számára, és egy másikat a háttér-kiszolgálók számára.

1. Kattintson a Azure Portal bal felső sarkában található **új** elemre.
2. Válassza a **Hálózatkezelés**, majd az **Application Gateway** elemet a Kiemeltek listából.
3. Adja meg a *myAppGateway* nevet az Application Gateway és az új erőforráscsoport *myResourceGroupAG* nevéhez.
4. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.
5. Kattintson a **virtuális hálózat választása**, kattintson a **új létrehozása**, majd adja meg ezeket az értékeket a virtuális hálózat:
   - myVNet * – a virtuális hálózat nevéhez.
   - 10.0.0.0/16 * – a virtuális hálózati címtartomány esetében.
   - Az alhálózat neve *myAGSubnet*.
   - Az alhálózat címtere *10.0.0.0/24*.  
     ![private-frontendip-1](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)
6. A virtuális hálózat és az alhálózat létrehozásához kattintson az **OK** gombra.
7. Válassza ki az előtérbeli IP-konfigurációt magánjellegűként, és alapértelmezés szerint ez egy dinamikus IP-cím-hozzárendelés. A kiválasztott alhálózat első elérhető címe az előtér-IP-címként lesz hozzárendelve.
8. Ha privát IP-címet szeretne kijelölni az alhálózat címtartományból (statikus kiosztás), akkor kattintson a mező **egy adott magánhálózati IP-cím választása** lehetőségre, és adja meg az IP-címet.
   > [!NOTE]
   > A lefoglalt IP-cím típusa (statikus vagy dinamikus) később nem módosítható.
9. Válassza ki a figyelő konfigurációját a protokollhoz és a porthoz, WAF-konfigurációt (ha szükséges), majd kattintson az OK gombra.
    ![private-frontendip-2](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-2.png)
10. Tekintse át a beállításokat az összefoglalás lapon, majd kattintson az **OK** gombra a hálózati erőforrások és az Application Gateway létrehozásához. Az application gateway hozhatók létre, várjon, amíg az üzembe helyezés sikeresen befejeződik, mielőtt továbblép a következő szakaszban több percig is eltarthat.

## <a name="add-backend-pool"></a>Háttér-készlet hozzáadása

A háttér-készlet a kérelmeknek a háttér-kiszolgálókra való továbbítására szolgál, amelyek a kérést fogják szolgálni. A háttér a hálózati adapterek, a virtuálisgép-méretezési csoportok, a nyilvános IP-címek, a belső IP-címek, a teljes tartománynevek (FQDN) és a több-bérlős háttérrendszer, például a Azure App Service tagjai lehetnek. Ebben a példában a virtuális gépeket a célként szolgáló háttérként fogjuk használni. Használhatunk meglévő virtuális gépeket, vagy újakat is létrehozhat. Ebben a példában két virtuális gépet hozunk létre, amelyeket az Azure háttér-kiszolgálóként használ az Application Gateway számára. Ehhez a következőket fogjuk tenni:

1. Hozzon létre 2 új virtuális gépet, *myVM* és *myVM2*a háttér-kiszolgálóként való használatra.
2. Telepítse az IIS-t a virtuális gépeken annak ellenőrzéséhez, hogy az Application Gateway sikeresen létrejött-e.
3. Adja hozzá a háttér-kiszolgálókat a háttér-készlethez.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Kattintson az **Új** lehetőségre.
2. Kattintson a **számítás** elemre, majd válassza a **Windows Server 2016 Datacenter** elemet a Kiemelt listában.
3. Adja meg a következő értékeket a virtuális gép számára:
   - A virtuális gép neve *myVM*.
   - A rendszergazda felhasználóneve: *azureuser*.
   - A jelszó *Azure123456!* jelszót.
   - Válassza a **Meglévő használata**, majd a *myResourceGroupAG* lehetőséget.
4. Kattintson az **OK** gombra.
5. Válassza a **DS1_V2** lehetőséget a virtuális gép méretének kiválasztásához, majd kattintson a **kiválasztás**gombra.
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
