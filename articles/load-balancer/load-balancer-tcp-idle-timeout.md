---
title: A tcp-időtúltöltés konfigurálása az Azure-ban
titleSuffix: Azure Load Balancer
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja az Azure Load Balancer TCP tétlen időtúltöltést.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: d0bb73b58aa23e5f7eb784772acf37b05df463ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456828"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>TCP-időtúlterhelési beállítások konfigurálása az Azure Load Balancer-hez

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, `Connect-AzAccount` az Azure-ral való kapcsolat létrehozásához is futnia kell.

## <a name="tcp-idle-timeout"></a>TCP-járat-időeltetés
Az Azure Load Balancer egy tétlen időtúlterhelési beállítás 4 perc és 30 perc között van. Alapértelmezés szerint 4 percre van beállítva. Ha az inaktív időszak hosszabb, mint az időtúlérték, nincs garancia arra, hogy a TCP vagy HTTP-munkamenet megmarad az ügyfél és a felhőszolgáltatás között.

A kapcsolat bezárásaután az ügyfélalkalmazás a következő hibaüzenetet kaphatja: "Az alapul szolgáló kapcsolat megszakadt: A kiszolgáló bezártegy olyan kapcsolatot, amelyet életben kellett tartania."

Bevett gyakorlat a TCP életben tartásának használata. Ez a gyakorlat hosszabb ideig tartja a kapcsolatot aktívállapotban. További információt a [.NET példákban talál.](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx) Ha engedélyezve van az életben tartás, a csomagok küldése a kapcsolat inaktivitási időszakaiban történik. Az életben tartó csomagok biztosítják, hogy az érvénytelen időtúllépési érték nem érhető el, és a kapcsolat hosszú ideig megmarad.

A beállítás csak bejövő kapcsolatok esetén működik. A kapcsolat elvesztésének elkerülése érdekében konfigurálja a TCP életben tartását az alapjárati időtúllépési beállításnál kisebb időközzel, vagy növelje az alapjárati időtúllépésértékét. Ezeknek a forgatókönyveknek a támogatásához egy konfigurálható tétlen időkitöltés támogatott.

A TCP keep-alive olyan esetekben működik, amikor az akkumulátor élettartama nem megszorítás. Mobilalkalmazásokhoz nem ajánlott. A TCP életben tartás mobilalkalmazásban való használata gyorsabban lemerítheti az eszköz akkumulátorát.

![TCP időmeghosszabbítása](./media/load-balancer-tcp-idle-timeout/image1.png)

A következő szakaszok ismertetik, hogyan módosíthatja az írási időtúlterhelési beállításokat a nyilvános IP és a terheléselosztó erőforrások.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>A tcp-időszám-szám beállítása a példányszintű nyilvános IP-címhez 15 percre

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

A(z) `IdleTimeoutInMinutes` nem kötelező. Ha nincs beállítva, az alapértelmezett időtúlérték 4 perc. Az időhosszabbítási tartomány 4–30 perc.

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>A TCP időtúlnapjának beállítása 15 percre egy terheléselosztásos szabályon

A terheléselosztó tétlen időtúlnapjának beállításához az "IdleTimeoutInMinutes" beállítás a terheléselosztási szabályon van beállítva. Példa:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>További lépések

[Belső terheléselosztó áttekintése](load-balancer-internal-overview.md)

[Internetalapú terheléselosztó konfigurálásának első lépései](quickstart-create-standard-load-balancer-powershell.md)

[A terheléselosztó elosztási módjának konfigurálása](load-balancer-distribution-mode.md)
