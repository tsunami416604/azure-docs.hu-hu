---
title: A terheléselosztó TCP-üresjárati időkorlátjának konfigurálása az Azure-ban
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
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: 317f6a73812b0e4284564ca9b5593e09e22edf12
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048703"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>A TCP Üresjárati időkorlát beállításainak konfigurálása Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor azt is futtatnia kell, `Connect-AzAccount` hogy létrehozza az Azure-hoz való kapcsolódást.

## <a name="tcp-idle-timeout"></a>TCP Üresjárati időkorlát
Azure Load Balancer üresjárati időkorlátja 4 perc és 30 perc között van. Alapértelmezés szerint 4 percre van beállítva. Ha egy inaktivitási időtartam hosszabb az időtúllépési értéknél, nem garantálható, hogy a TCP-vagy HTTP-munkamenet az ügyfél és a felhőalapú szolgáltatás között marad.

Ha a csatlakozás be van zárva, az ügyfélalkalmazás a következő hibaüzenetet kaphatja: "az alapul szolgáló csatlakozás bezárult: a kiszolgáló lezárta az életben tartani várt kapcsolatokat."

Gyakori eljárás a TCP Keep-Alive használata. Ez a gyakorlat hosszabb ideig tart a kapcsolatok aktív állapotban. További információkért tekintse meg ezeket a [.net-példákat](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Ha a Keep-Alive engedélyezve van, a rendszer a csatlakozáskor inaktivitási időszakokban küld csomagokat. Életben tartási csomagok gondoskodnak arról, hogy az Üresjárati időkorlát értéke ne legyen elérhető, és a kapcsolat hosszabb ideig marad.

A beállítás csak a bejövő kapcsolatok esetében működik. A kapcsolat elvesztésének elkerüléséhez konfigurálja a TCP Keep-Alive értéket az Üresjárati időkorlát beállításnál kisebb intervallumra, vagy növelje az Üresjárati időkorlát értékét. Ezen forgatókönyvek támogatásához konfigurálható Üresjárati időkorlát támogatása lett hozzáadva.

A TCP Keep-Alive olyan forgatókönyvek esetén működik, ahol az akkumulátorok élettartama nem korlátozás. A mobil alkalmazások esetében nem ajánlott. Ha egy TCP Keep-Alive protokollt használ a mobil alkalmazásban, az eszköz akkumulátora gyorsabban kiüríthető.

![TCP-időtúllépés](./media/load-balancer-tcp-idle-timeout/image1.png)

A következő szakaszok azt ismertetik, hogyan változtathatók meg a nyilvános IP-címek és a terheléselosztó erőforrásainak üresjárati időtúllépési beállításai.

>[!NOTE]
> A TCP Üresjárati időkorlát nem befolyásolja az UDP protokoll terheléselosztási szabályait.


## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>A példány-szintű nyilvános IP-cím TCP-időtúllépésének beállítása 15 percre

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

A(z) `IdleTimeoutInMinutes` nem kötelező. Ha nincs beállítva, az alapértelmezett időtúllépés 4 perc. Az elfogadható időtúllépési tartomány 4 – 30 percet vesz igénybe.

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>Egy elosztott terhelésű szabály TCP-időtúllépésének beállítása 15 percre

A terheléselosztó üresjárati időkorlátjának beállításához a "IdleTimeoutInMinutes" beállítás van beállítva a terheléselosztási szabályhoz. Például:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>Következő lépések

[A belső Load Balancer áttekintése](load-balancer-internal-overview.md)

[Az internetre irányuló terheléselosztó konfigurálásának első lépései](quickstart-load-balancer-standard-public-powershell.md)

[A terheléselosztó elosztási módjának konfigurálása](load-balancer-distribution-mode.md)
