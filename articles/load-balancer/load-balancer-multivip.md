---
title: "Mutiple virtuális IP-címek a felhőalapú szolgáltatáshoz"
description: "Több virtuális és egy felhőalapú szolgáltatás több virtuális IP-címek beállítása – áttekintés"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: 85f6d26a-3df5-4b8e-96a1-92b2793b5284
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: b6b7b0b2d7a7f33facaf72bbd2d7937364770673
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-multiple-vips-for-a-cloud-service"></a>Több virtuális IP-címek egy felhőalapú szolgáltatás konfigurálása

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Azure által biztosított IP-cím használatával a nyilvános interneten keresztül férhetnek hozzá Azure felhőalapú szolgáltatásokhoz. A nyilvános IP-cím nevezzük virtuális IP-címhez (virtuális IP-cím), mert nincs csatolva az Azure load balancer, és nem a virtuális gép (VM) példánya a felhőszolgáltatás belül. A Virtuálisgép-példány egy felhőalapú szolgáltatás belül egyetlen virtuális IP-címhez használatával végezheti el.

Vannak azonban forgatókönyvek, ahol esetleg egynél több bejegyzés VIP pont azonos a felhőszolgáltatáshoz. Például a felhőalapú szolgáltatás, amely minden helyen üzemeltetett különböző ügyfél SSL-kapcsolatot használja az alapértelmezett port 443-as, szükség van, vagy a bérlői több webhely futhatnak. Ebben a forgatókönyvben kell egy másik nyilvános felé néző IP-címet minden webhelyre vonatkozóan. Az alábbi ábra szemlélteti a tipikus több-bérlős webszolgáltatási több SSL-tanúsítványok ugyanazt a nyilvános portot van szükség.

![Több virtuális IP-SSL forgatókönyv](./media/load-balancer-multivip/Figure1.png)

A fenti példában az összes virtuális IP-címek használata azonos nyilvános portot (443) és forgalmat a rendszer átirányítja egy vagy több terhelésű egyedi titkos port a belső IP-cím a felhőalapú szolgáltatás, a webhelyek üzemeltető virtuális gépek.

> [!NOTE]
> A több virtuális IP-címek használatát igénylő egy másik helyzet üzemeltető ugyanazokat a virtuális gépek több SQL AlwaysOn rendelkezésre állási csoport affinitásmaszkkal.

Virtuális IP-címmel dinamikus alapértelmezés, ami azt jelenti, hogy a tényleges, a felhőalapú szolgáltatáshoz rendelt IP-cím idővel megváltozhat. Akadályozható, amely, a szolgáltatás foglalhat a virtuális IP-címhez. Fenntartott virtuális IP-címmel kapcsolatos további információkért lásd: [foglalt nyilvános IP-cím](../virtual-network/virtual-networks-reserved-public-ip.md).

> [!NOTE]
> Ellenőrizze a [IP-cím árképzési](https://azure.microsoft.com/pricing/details/ip-addresses/) árazás virtuális IP-címek és foglalt IP-cím.

Ön PowerShell segítségével ellenőrizze a virtuális IP-címek a felhőalapú szolgáltatás által használt, valamint hozzáadása és távolítsa el virtuális IP-címek, társítson egy végpontot a virtuális IP-címhez, és adott VIP-címhez a terheléselosztás konfigurálása.

## <a name="limitations"></a>Korlátozások

Jelenleg több virtuális IP-funkció korlátozódik, a következő esetekben:

* **Csak az infrastruktúra-szolgáltatási**. A virtuális gépeket tartalmazó felhőszolgáltatások több VIP csak engedélyezheti. A PaaS szerepkörpéldányok feladatok több VIP nem használható.
* **Csak a PowerShell**. Több VIP csak kezelheti a PowerShell használatával.

Ezek a korlátozások ideiglenesek, és bármikor módosíthatja. Ügyeljen arra, hogy ezen a lapon ellenőrizze a jövőbeli változásokról le újra.

## <a name="how-to-add-a-vip-to-a-cloud-service"></a>Virtuális IP-címhez hozzáadása egy felhőalapú szolgáltatás
Virtuális IP-címhez a szolgáltatáshoz való hozzáadásához futtassa a következő PowerShell-parancsot:

```powershell
Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

A parancs a következő mintához hasonló eredményt jeleníti meg:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Egy felhőalapú szolgáltatás VIP eltávolítása
A szolgáltatás a fenti példában a hozzáadandó VIP eltávolításához futtassa a következő PowerShell-parancsot:

```powershell
Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

> [!IMPORTANT]
> Virtuális IP-címhez csak távolítsa el, ha nincsenek végpontok társítva van.


## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Hogyan VIP információt lekérni egy felhőalapú szolgáltatás
A felhőszolgáltatás társított virtuális IP-címek lekéréséhez futtassa a következő PowerShell-parancsfájl:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

A parancsfájl a következő mintához hasonló eredményt jeleníti meg:

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

Ebben a példában a felhőszolgáltatás 3 rendelkezik virtuális IP-címmel:

* **Vip1-en** van alapértelmezett VIP ismeri, mert a IsDnsProgrammedName értéke TRUE.
* **Vip2** és **Vip3** nem kell használni, mint az IP-címek nem rendelkeznek. Használni fogják őket csak ha a VIP egy végpontot rendel hozzá.

> [!NOTE]
> Az előfizetés csak megterheljük az extra virtuális IP-címek, ha társítva a végpont. Az árakkal kapcsolatos további információkért lásd: [IP-cím árképzési](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>Hogyan rendelje hozzá a végpont a virtuális IP-címhez

Egy felhőalapú szolgáltatást, hogy a végpont a virtuális IP-címhez társítható, futtassa a következő PowerShell-parancsot:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 |
    Update-AzureVM
```

A parancs létrehoz egy nevű VIP-címhez kapcsolódó végpontot *Vip2* porton *80*, és csatolja azt a virtuális gép nevű *myVM1* felhőszolgáltatásban nevű *myService* használatával *TCP* porton *8080*.

Ellenőrizze a konfigurációt, futtassa a következő PowerShell-parancsot:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

A kimeneti az alábbihoz hasonlít:

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

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>A megadott virtuális IP-címhez tartozó engedélyezése

Egyetlen virtuális IP-címhez több virtuális gép terheléselosztásához célokra is társíthat. Például, hogy nevű felhőszolgáltatás *myService*, és két virtuális gép nevű *myVM1* és *myVM2*. És a felhőalapú szolgáltatás több virtuális IP-címek, egyiket nevű *Vip2*. Ha biztos szeretne lenni abban, hogy minden forgalomnak port *81-es* a *Vip2* közötti elosztását *myVM1* és *myVM2* porton *8181*, futtassa a következő PowerShell-parancsfájlt:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2 -DefaultProbe |
    Update-AzureVM

Get-AzureVM -ServiceName myService -Name myVM2 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe |
    Update-AzureVM
```

A terheléselosztó használatára egy különböző VIP is frissítheti. Például ha az alábbi PowerShell-parancsot futtatja, megváltozik a Vip1 nevű VIP használatára beállított terheléselosztási:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1
```

## <a name="next-steps"></a>Következő lépések

[A terheléselosztás Azure naplóelemzés](load-balancer-monitor-log.md)

[Az Internet felé néző terhelés terheléselosztó áttekintése](load-balancer-internet-overview.md)

[Az internetre irányuló terheléselosztót első lépések](load-balancer-get-started-internet-arm-ps.md)

[Virtual Network áttekintése](../virtual-network/virtual-networks-overview.md)

[Fenntartott IP-cím REST API-k](https://msdn.microsoft.com/library/azure/dn722420.aspx)
