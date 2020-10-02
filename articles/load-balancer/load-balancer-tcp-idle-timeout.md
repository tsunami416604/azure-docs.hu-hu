---
title: A Load Balancer TCP alaphelyzetbe állításának és üresjárati időkorlátjának konfigurálása az Azure
titleSuffix: Azure Load Balancer
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja Azure Load Balancer TCP üresjárati időkorlátját.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/09/2020
ms.author: allensu
ms.openlocfilehash: 26c4c01aaf6abe6b9c9ac6daf6836d7b660ba21e
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2020
ms.locfileid: "91649830"
---
# <a name="configure-tcp-idle-timeout-for-azure-load-balancer"></a>A TCP Üresjárati időkorlát konfigurálása Azure Load Balancerhoz

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor azt is futtatnia kell, `Connect-AzAccount` hogy létrehozza az Azure-hoz való kapcsolódást.

A Azure Load Balancer 4 perces Üresjárati időkorlát 120 percre van beállítva. Alapértelmezés szerint 4 percre van beállítva. Ha egy inaktivitási időtartam hosszabb az időtúllépési értéknél, nem garantálható, hogy a TCP-vagy HTTP-munkamenet az ügyfél és a felhőalapú szolgáltatás között marad. További információ a [TCP tétlen időtúllépésről](load-balancer-tcp-reset.md).

A következő szakaszok azt ismertetik, hogyan változtathatók meg a nyilvános IP-címek és a terheléselosztó erőforrásainak üresjárati időtúllépési beállításai.


## <a name="configure-the-tcp-idle-timeout-for-your-public-ip"></a>A TCP Üresjárati időkorlát konfigurálása a nyilvános IP-címhez

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

A(z) `IdleTimeoutInMinutes` nem kötelező. Ha nincs beállítva, az alapértelmezett időtúllépés 4 perc. Az elfogadható időtúllépési tartomány 4 – 120 perc.

## <a name="set-the-tcp-idle-timeout-on-rules"></a>A TCP Üresjárati időkorlát beállítása a szabályoknál

A terheléselosztó üresjárati időkorlátjának beállításához a "IdleTimeoutInMinutes" beállítás van beállítva a terheléselosztási szabályhoz. Például:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```

## <a name="next-steps"></a>Következő lépések

[A belső Load Balancer áttekintése](load-balancer-internal-overview.md)

[Az internetre irányuló terheléselosztó konfigurálásának első lépései](quickstart-load-balancer-standard-public-powershell.md)

[A terheléselosztó elosztási módjának konfigurálása](load-balancer-distribution-mode.md)
