---
title: Több virtuális IP-cím egy felhőalapú szolgáltatás
description: Több és több virtuális IP-címek megadása a felhő alapú szolgáltatás áttekintése
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 03ecb44f3d561ff720939d0605d342b299194ef2
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218797"
---
# <a name="configure-multiple-vips-for-a-cloud-service"></a>Több virtuális IP-címek a felhőszolgáltatások konfigurálása

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

A nyilvános interneten keresztül az Azure cloud services hozzáférhet az Azure által megadott IP-cím használatával. A nyilvános IP-cím néven virtuális IP-címhez (virtuális IP-), mivel az Azure load balancer kapcsolódik, és nem a virtuális gép (VM) instances felhőszolgáltatáson belül. Minden Virtuálisgép-példány a felhőszolgáltatásban elérheti egy egyetlen virtuális IP-cím használatával.

Vannak azonban forgatókönyvek, amelyben szükség lehet egynél több virtuális IP-CÍMEK bejegyzésként ugyanazon a felhőszolgáltatáson mutasson. Például a cloud Services több webhelyet is, amely SSL-kapcsolatok használatával az alapértelmezett port a 443-as, szükség szerint minden hely egy másik ügyfél üzemel, vagy a bérlői is üzemeltethet. Ebben az esetben szüksége lesz egy másik nyilvános felé néző IP-címet minden webhelyre vonatkozóan. Az alábbi ábra egy tipikus több-bérlős webszolgáltatási az ugyanazt a nyilvános portot több SSL-tanúsítvány szükséges.

![Több virtuális IP-cím SSL eset](./media/load-balancer-multivip/Figure1.png)

A fenti példában az összes virtuális IP-címeket használja a nyilvános portot (443) és forgalmat a rendszer átirányítja egy vagy több elosztott terhelésű virtuális gépek egyedi magánhálózati port az összes webhely üzemeltető felhőszolgáltatás belső IP-címét.

> [!NOTE]
> A több virtuális IP-címek használni egy másik helyzet futtató több SQL AlwaysOn rendelkezésre állási csoport figyelője a virtuális gépek ugyanahhoz az adatkészlethez.

Virtuális IP-címek dinamikusak alapértelmezettként, ami azt jelenti, hogy a felhőalapú szolgáltatáshoz rendelt konkrét IP-címet idővel változhat. Akadályozható, amely, fenntartott virtuális IP-címhez a szolgáltatás. Fenntartott virtuális IP-címek kapcsolatos további információkért lásd: [lefoglalt nyilvános IP-cím](../virtual-network/virtual-networks-reserved-public-ip.md).

> [!NOTE]
> Lásd: [IP-címek díjszabása](https://azure.microsoft.com/pricing/details/ip-addresses/) díjszabás a virtuális IP-címek és fenntartott IP-címek kapcsolatos információkat.

Meg is a VIP-címek a felhőalapú szolgáltatások által használt ellenőrzése a PowerShell használatával, valamint hozzáadása és távolítsa el a virtuális IP-címek egy végpontnak egy virtuális IP-cím társítása és konfigurálja egy adott VIP-címet a terheléselosztási.

## <a name="limitations"></a>Korlátozások

Jelenleg több virtuális IP-CÍMEK funkció korlátozódik a következő forgatókönyvekre:

* **Csak az IaaS**. Több virtuális IP-CÍMEK a virtuális gépeket tartalmazó felhőszolgáltatások csak engedélyezheti. Több virtuális IP-cím nem használható PaaS szerepkörpéldányok forgatókönyvekben.
* **Csak a PowerShell**. Több virtuális IP-CÍMEK csak a PowerShell-lel kezelheti.

Ezek a korlátozások ideiglenesek, és bármikor megváltozhat. Ellenőrizze, hogy ezen a lapon a későbbi módosítások ellenőrzéséhez nyissa meg újra.

## <a name="how-to-add-a-vip-to-a-cloud-service"></a>Felhőszolgáltatás virtuális IP-címhez hozzáadása
Egy virtuális IP-CÍMEK hozzáadása a szolgáltatáshoz, futtassa a következő PowerShell-parancsot:

```powershell
Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

Ez a parancs a következő mintához hasonló eredményt jeleníti meg:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Felhőszolgáltatás virtuális IP-címhez eltávolítása
A fenti példában a szolgáltatáshoz hozzáadott virtuális IP-CÍMEK eltávolításához futtassa a következő PowerShell-parancsot:

```powershell
Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

> [!IMPORTANT]
> Egy virtuális IP-CÍMEK csak távolítsa el, ha nincsenek végpontok társítva van hozzá.


## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Hogyan Felhőszolgáltatás virtuális IP-CÍMEK információt lekérni
Egy felhőszolgáltatáshoz társított virtuális IP-cím lekéréséhez futtassa a következő PowerShell-parancsfájlt:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

A szkript a következő mintához hasonló eredményt jeleníti meg:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

Ebben a példában a felhőszolgáltatás 3 rendelkezik virtuális IP-címek:

* **Vip1-en** az alapértelmezett virtuális IP-cím, akkor tudja, hogy mivel a IsDnsProgrammedName értéke igaz.
* **Vip2** és **Vip3** nem szolgálnak bármely IP-címek nem rendelkeznek. Használni fogják őket csak egy végpontot a virtuális IP-CÍMEK társításakor.

> [!NOTE]
> Az előfizetés csak kell fizetni további virtuális IP-címek után azok a végpont. További információ a díjszabásról lásd: [IP-címek díjszabása](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>Egy végpontnak egy virtuális IP-cím társítása

Egy végpontot egy felhőszolgáltatásban lévő virtuális IP-címhez társítható, futtassa a következő PowerShell-parancsot:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 |
    Update-AzureVM
```

A parancs létrehoz egy végpontot a virtuális IP-cím nevű társított *Vip2* porton *80-as*, és csatolja azt a virtuális gép nevű *myVM1* felhőszolgáltatásban nevű *myService* használatával *TCP* porton *8080-as*.

A konfiguráció ellenőrzéséhez futtassa a következő PowerShell-parancsot:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

A kimenet a következő példához hasonlóan néz ki:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>Egy adott virtuális IP-cím a terheléselosztás engedélyezése

A terheléselosztás céljából több virtuális gép egyetlen virtuális IP-címhez társíthatja. Például, hogy egy felhőalapú szolgáltatás nevű *myService*, és két virtuális gép *myVM1* és *myVM2*. A cloud Services pedig több virtuális IP-cím, az egyiket nevű *Vip2*. Ha szeretné biztosítani, hogy minden forgalomnak portra *81-es* a *Vip2* közötti kiegyensúlyozott *myVM1* és *myVM2* porton *8181* , futtassa a következő PowerShell-parancsfájlt:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2 -DefaultProbe |
    Update-AzureVM

Get-AzureVM -ServiceName myService -Name myVM2 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe |
    Update-AzureVM
```

A terheléselosztó egy másik virtuális IP-cím használatára is frissítheti. Például ha az alábbi PowerShell-parancsot futtatta, megváltozik a terheléselosztás beállítása egy vip1-en nevű virtuális IP-cím használata:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1
```

## <a name="next-steps"></a>További lépések

[Naplóelemzés az Azure-terheléselosztás](load-balancer-monitor-log.md)

[Az Internet felé néző load balancer áttekintése](load-balancer-internet-overview.md)

[Bevezetés az internetkapcsolattal rendelkező load balancer](load-balancer-get-started-internet-arm-ps.md)

[A Virtual Network áttekintése](../virtual-network/virtual-networks-overview.md)

[Fenntartott IP-címet REST API-k](https://msdn.microsoft.com/library/azure/dn722420.aspx)
