---
title: Több VIP-cím a Cloud Service-hez
titlesuffix: Azure Load Balancer
description: A című üzemelő áttekintése és a több virtuális IP-cím beállítása a Cloud Service-ben
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 3e97bea85d4d97b159168b21b4a6e932e655ccfb
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274706"
---
# <a name="configure-multiple-vips-for-a-cloud-service"></a>Több virtuális IP-cím konfigurálása egy felhőalapú szolgáltatáshoz

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Az Azure Cloud Services a nyilvános interneten keresztül érhető el az Azure által biztosított IP-cím használatával. Ez a nyilvános IP-cím VIP-ként (virtuális IP) hivatkozik, mert az Azure Load Balancerhez van csatolva, és nem a felhőalapú szolgáltatásban található virtuálisgép-példányokhoz. Egy felhőalapú szolgáltatás bármely virtuálisgép-példányát egyetlen virtuális IP-cím használatával érheti el.

Vannak azonban olyan helyzetek, amikor egynél több VIP-t kell bejelentkeznie ugyanahhoz a felhőalapú szolgáltatáshoz. Előfordulhat például, hogy a felhőalapú szolgáltatás több olyan webhelyet is üzemeltet, amelyek SSL-kapcsolatot igényelnek az alapértelmezett 443-as porton, mivel az egyes helyek egy másik ügyfélhez vagy bérlőhöz vannak tárolva. Ebben az esetben az egyes webhelyekhez eltérő nyilvános IP-címmel kell rendelkeznie. Az alábbi ábra egy tipikus több-bérlős webüzemeltetést mutat be, amelynek több SSL-tanúsítványra van szüksége ugyanazon a nyilvános porton.

![Több VIP SSL-forgatókönyv](./media/load-balancer-multivip/Figure1.png)

A fenti példában az összes virtuális IP-cím ugyanazt a nyilvános portot (443) használja, és a rendszer átirányítja egy vagy több elosztott terhelésű virtuális gépre az összes webhelyet üzemeltető felhőalapú szolgáltatás belső IP-címére.

> [!NOTE]
> Egy másik megoldás, ha a több virtuális IP-cím használatát igényli, több SQL alAlwaysOnon rendelkezésre állási csoport figyelőt üzemeltet ugyanazon a Virtual Machineson.

Alapértelmezés szerint a VIP-címek dinamikusak, ami azt jelenti, hogy a felhőalapú szolgáltatáshoz hozzárendelt tényleges IP-cím idővel változhat. Ennek megelőzése érdekében fenntarthat egy VIP-t a szolgáltatás számára. További információ a fenntartott VIP-címekről: [fenntartott nyilvános IP-cím](../virtual-network/virtual-networks-reserved-public-ip.md).

> [!NOTE]
> A virtuális [IP](https://azure.microsoft.com/pricing/details/ip-addresses/) -címekkel és a fenntartott IP-címekkel kapcsolatos díjszabásról a további tudnivalókat lásd:

A PowerShell segítségével ellenőrizheti a Cloud Services által használt VIP-ket, valamint a VIP-címek hozzáadását és eltávolítását, a virtuális IP-címek hozzárendelését egy végponthoz, és konfigurálhatja a terheléselosztást egy adott VIP-címen.

## <a name="limitations"></a>Korlátozások

Jelenleg a több VIP-funkció a következő forgatókönyvekre korlátozódik:

* **Csak IaaS**. Csak a virtuális gépeket tartalmazó felhőalapú szolgáltatások esetében engedélyezheti a több VIP-t. A több virtuális IP-cím nem használható a szerepkör-példányokkal rendelkező, Pásti-forgatókönyvekben.
* **Csak PowerShell**. A PowerShell használatával csak a több VIP-t kezelheti.

Ezek a korlátozások ideiglenesek, és bármikor megváltozhatnak. A jövőbeli változások ellenőrzéséhez mindenképpen tekintse át ezt a lapot.

## <a name="how-to-add-a-vip-to-a-cloud-service"></a>VIP hozzáadása felhőalapú szolgáltatáshoz
Ha VIP-t szeretne hozzáadni a szolgáltatáshoz, futtassa a következő PowerShell-parancsot:

```powershell
Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

Ez a parancs a következő mintához hasonló eredményt jelenít meg:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>VIP eltávolítása felhőalapú szolgáltatásból
A fenti példában a szolgáltatáshoz hozzáadott virtuális IP-cím eltávolításához futtassa a következő PowerShell-parancsot:

```powershell
Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

> [!IMPORTANT]
> Csak akkor távolíthat el VIP-t, ha nincs hozzá társított végpont.


## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>VIP-információk lekérése egy felhőalapú szolgáltatásból
A felhőalapú szolgáltatáshoz társított VIP-címek lekéréséhez futtassa a következő PowerShell-parancsfájlt:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

A parancsfájl a következő mintához hasonló eredményt jelenít meg:

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

Ebben a példában a Cloud Service 3 VIP-t tartalmaz:

* A **Vip1** az alapértelmezett VIP, de tudja, hogy a IsDnsProgrammedName értéke TRUE (igaz).
* A **VIP2** és a **Vip3** nem használhatók, mert nincsenek IP-címei. Csak akkor lesznek használatban, ha egy végpontot rendel hozzá a VIP-hez.

> [!NOTE]
> Az előfizetést csak akkor számítjuk fel, ha a további VIP-címekhez egy végpontot társítanak. A díjszabással kapcsolatos további információkért tekintse meg az [IP-címek díjszabását](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>VIP hozzárendelése végponthoz

Ha VIP-t szeretne hozzárendelni egy felhőalapú szolgáltatáshoz egy végponthoz, futtassa a következő PowerShell-parancsot:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 |
    Update-AzureVM
```

A parancs létrehoz egy *VIP2* nevű virtuális IP-címet a *80*-as porton, és összekapcsolja azt a *myService* nevű *myVM1* nevű Felhőbeli szolgáltatásban a *8080*-es porton a *TCP* protokoll használatával.

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

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>Egy adott VIP-cím terheléselosztásának engedélyezése

Terheléselosztási célokra egyetlen virtuális IP-címet is hozzárendelhet több virtuális géphez. Tegyük fel például, hogy rendelkezik egy *myService*nevű felhőalapú szolgáltatással, és két virtuális gépet *myVM1* és *myVM2*néven. A Cloud Service-nek több VIP-je van, amelyek közül az egyik a *VIP2*nevű. Ha biztosítani szeretné, hogy a *Vip2* *81* -es portján lévő összes forgalom a *8181*-es porton a *myVM1* és a *myVM2* között legyen, futtassa a következő PowerShell-parancsfájlt:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2 -DefaultProbe |
    Update-AzureVM

Get-AzureVM -ServiceName myService -Name myVM2 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe |
    Update-AzureVM
```

A terheléselosztó másik VIP használatára is frissíthető. Ha például az alábbi PowerShell-parancsot futtatja, a terheléselosztási készletet úgy kell módosítania, hogy az Vip1 nevű VIP-t használja:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1
```

## <a name="next-steps"></a>További lépések

[Azure Monitor naplók az Azure Load Balance szolgáltatáshoz](load-balancer-monitor-log.md)

[Internetkapcsolattal rendelkező Load Balancer – áttekintés](load-balancer-internet-overview.md)

[Ismerkedés az internetkapcsolattal rendelkező Load balancerrel](load-balancer-get-started-internet-arm-ps.md)

[A Virtual Network áttekintése](../virtual-network/virtual-networks-overview.md)

[Fenntartott IP REST API-k](https://msdn.microsoft.com/library/azure/dn722420.aspx)
