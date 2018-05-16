---
title: Internetkapcsolattal rendelkező terheléselosztó létrehozása – klasszikus Azure PowerShell | Microsoft Docs
description: Ismerje meg, hogyan hozható létre internetkapcsolattal rendelkező terheléselosztó klasszikus üzemmódban a PowerShell használatával
services: load-balancer
documentationcenter: na
author: genlin
manager: cshepard
tags: azure-service-management
ms.assetid: 73e8bfa4-8086-4ef0-9e35-9e00b24be319
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: 07d3658ff86a46875a57cb3359a60661911e0c8b
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-powershell"></a>Bevezetés az internetkapcsolattal rendelkező terheléselosztó (klasszikus) létrehozásába a PowerShellben

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure Cloud Services](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Az Azure-erőforrásokkal való munka megkezdése előtt fontos megérteni, hogy az Azure jelenleg két üzembe helyezési modellel rendelkezik, a Resource Managerrel és a klasszikussal. Bizonyosodjon meg arról, hogy megfelelő ismeretekkel rendelkezik az [üzembe helyezési modellekről és eszközökről](../azure-classic-rm.md), mielőtt elkezdene dolgozni az Azure-erőforrásokkal. A különféle eszközök dokumentációit a cikk tetején található fülekre kattintva tekintheti meg. Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. Emellett [azt is megismerheti, hogyan lehet internetkapcsolattal rendelkező terheléselosztót létrehozni az Azure Resource Manager használatával](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="set-up-load-balancer-using-powershell"></a>A terheléselosztó beállítása a PowerShell használatával

Az alábbi lépéseket követve állíthat be egy terheléselosztót a PowerShell használatával:

1. Ha még nem használta az Azure PowerShellt, tekintse meg [How to Install and Configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása) című részt, majd kövesse az utasításokat egészen az utolsó lépésig az Azure-ba való bejelentkezéshez és az előfizetése kiválasztásához.
2. Virtuális gép létrehozása után a PowerShell-parancsmagok segítségével hozzáadhat egy terheléselosztót egy virtuális géphez ugyanazon a felhőszolgáltatáson belül.

Az alábbi példában egy „webfarm” nevű terheléselosztó-készletet fog hozzáadni a „mytestcloud” (vagy myctestcloud.cloudapp.net) nevű felhőszolgáltatáshoz, ehhez a terheléselosztó végpontjait hozzáadja a „web1” és „web2” nevű virtuális gépekhez. A terheléselosztó a 80-as porton keresztül fogadja a hálózati forgalmat, és a TCP használatával elosztja a terhelést a helyi végpont (jelen esetben a 80-as port) által meghatározott virtuális gépek között.

### <a name="step-1"></a>1. lépés

Hozzon létre egy elosztott terhelésű végpontot az első, „web1” nevű virtuális gép számára

```powershell
Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

### <a name="step-2"></a>2. lépés

Hozzon létre egy másik végpontot a második, „web2” nevű virtuális gép számára ugyanazon terheléselosztó-készlet nevének használatával

```powershell
Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

## <a name="remove-a-virtual-machine-from-a-load-balancer"></a>Virtuális gép eltávolítása a terheléselosztóból

A Remove-AzureEndpoint paranccsal távolíthatja el egy virtuális gép végpontját a terheléselosztóból

```powershell
Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin | Update-AzureVM
```

## <a name="next-steps"></a>További lépések

Emellett [megkezdheti egy belső terheléselosztó létrehozását](load-balancer-get-started-ilb-classic-ps.md), és beállíthatja az [elosztási mód](load-balancer-distribution-mode.md) típusát egy adott terheléselosztó hálózati forgalmára vonatkozóan.

Ha az alkalmazásának fenn kell tartania a kapcsolatot a terheléselosztó mögött lévő kiszolgálókkal, többet megtudhat a [terheléselosztó üresjárati TCP-időkorlátjának beállításairól](load-balancer-tcp-idle-timeout.md). A segítségével jobban megismerheti az üresjárati kapcsolatok viselkedését az Azure Load Balancer használata során.
