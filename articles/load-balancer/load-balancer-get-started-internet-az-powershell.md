---
title: "Hozzon létre egy Load Balancer szabványos nyilvános zónaredundáns nyilvános IP-cím cím előtér PowerShell használatával |} Microsoft Docs"
description: "Útmutató a PowerShell használatával zónaredundáns nyilvános IP-cím cím előtérbeli nyilvános Load Balancer szabványos létrehozása"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: kumud
ms.openlocfilehash: 2945e04814f3d1e5b281391485def47f4b463d88
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-powershell"></a>Hozzon létre egy Load Balancer szabványos nyilvános zónaredundáns nyilvános IP-cím cím előtér PowerShell használatával

Ez a cikk lépésről-lépésre nyilvános létrehozása [Load Balancer szabványos](https://aka.ms/azureloadbalancerstandard) a zónaredundáns időtúllépést IP szabványos nyilvános cím segítségével.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>Standard Preview rendelkezésre állási zónák, a Load Balancer Standard és a nyilvános IP-cím regisztrálása

Ez a cikk, hogy 4.4.0 verzióval rendelkezik, vagy magasabb AzureRM modul telepítve van szükség. A verzió megkereséséhez futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha telepíteni vagy frissíteni kell, telepítse a legújabb verziót a AzureRM modul a [PowerShell-galériában](https://www.powershellgallery.com/packages/AzureRM).

>[!NOTE]
[Terheléselosztó Standard Termékváltozat betöltése](https://aka.ms/azureloadbalancerstandard) jelenleg előzetes verzió. Az előzetes kiadás során a szolgáltatás rendelkezésre állása és megbízhatósága eltérő lehet az általánosan elérhető kiadásétól. További részletekért lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Használja a nyilvánosan elérhető [Load Balancer alapszintű Termékváltozat](load-balancer-overview.md) a termelés számára. 

> [!NOTE]
> Rendelkezésre állási zónák még csak előzetes verziójúak, és készen áll a fejlesztési és forgatókönyvek tesztelése. A select Azure-erőforrások és a régiók és a virtuális gép mérete családok támogatás érhető el. További információ az első lépések, és mely Azure-erőforrások, régiók és virtuális gép mérete családok megpróbálhatja a rendelkezésre állási zónákat, lásd: [rendelkezésre állási zónák áttekintése](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

Mielőtt kiválasztja, a zóna vagy zónaredundáns. lehetőséget az az előtérbeli nyilvános IP-címet a terheléselosztóhoz, akkor először el kell végeznie a lépéseket [regisztrálja a rendelkezésre állási zónák Preview](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Login-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot, a következő parancsot:

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location westeurope
```

## <a name="create-a-public-ip-standard"></a>Hozzon létre egy nyilvános IP Standard 
Hozzon létre egy nyilvános IP szabványos a következő parancsot:

```powershell
$publicIp = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Name 'myPublicIP' `
  -Location westeurope -AllocationMethod Static -Sku Standard 
```

## <a name="create-a-front-end-ip-configuration-for-the-website"></a>A webhely előtér-IP-konfiguráció létrehozása

Hozzon létre egy előtérbeli IP-konfiguráció a következő parancsot:

```powershell
$feip = New-AzureRmLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
```

## <a name="create-the-back-end-address-pool"></a>A háttér-címkészlet létrehozása

Hozzon létre egy háttér címkészletet, a következő parancsot:

```powershell
$bepool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

## <a name="create-a-load-balancer-probe-on-port-80"></a>Terheléselosztói mintavétel létrehozása a 80-as porton

Hozzon létre egy állapotmintáihoz 80-as porton a terheléselosztó a következő parancsot:

```powershell
$probe = New-AzureRmLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

## <a name="create-a-load-balancer"></a>Terheléselosztó létrehozása
Hozzon létre egy terheléselosztási terheléselosztó szabványos a következő parancsot:

```powershell
$lb = New-AzureRmLoadBalancer -ResourceGroupName myResourceGroup -Name 'MyLoadBalancer' -Location westeurope `
  -FrontendIpConfiguration $feip -BackendAddressPool $bepool `
  -Probe $probe -LoadBalancingRule $rule -Sku Standard
```

## <a name="next-steps"></a>Következő lépések
- Megtudhatja, hogyan [hozzon létre egy nyilvános IP-cím egy rendelkezésre állási zónában](../virtual-network/create-public-ip-availability-zone-portal.md)



