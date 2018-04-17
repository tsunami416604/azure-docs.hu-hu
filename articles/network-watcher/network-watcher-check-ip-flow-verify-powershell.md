---
title: Ellenőrizze a forgalom Azure hálózati figyelő IP-adatfolyam ellenőrizze - PowerShell |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan ellenőrizhető, ha a bejövő és kimenő forgalmat a virtuális gépek engedélyezett vagy megtagadott a PowerShell használatával
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: e1dad757-8c5d-467f-812e-7cc751143207
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 7c2d5e0811f7a5e1f865992be1d5a2c189f10374
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Ellenőrizze, hogy a forgalom engedélyezett vagy megtagadott vagy a virtuális gép IP-adatfolyam és Azure hálózati figyelőt összetevője ellenőrzése

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [Az Azure REST API-n](network-watcher-check-ip-flow-verify-rest.md)


IP-adatfolyam ellenőrizze, hogy egy funkciója, amely lehetővé teszi, hogy ellenőrizze, hogy ha a forgalom engedélyezve van-e, vagy a virtuális gép hálózati figyelőt. Ebben a forgatókönyvben akkor hasznos, ha szeretné, hogy a virtuális gép kommunikálhat külső erőforrás- vagy háttéradatbázis aktuális állapotának. IP-adatfolyam győződjön meg arról is használható, ha a hálózati biztonsági csoport (NSG) szabályok konfigurációja megfelelő-e, és hibáinak elhárítása az NSG-szabályok által blokkolt adatfolyamok ellenőrzése. Egy másik oka IP folyamat, ellenőrizze annak biztosítása, amelyet a letiltott forgalmat blokkol megfelelően az NSG.

## <a name="before-you-begin"></a>Előkészületek

Ez a forgatókönyv azt feltételezi, hogy már követte lépéseit [hozzon létre egy hálózati figyelőt](network-watcher-create.md) hozzon létre egy hálózati figyelőt, vagy egy meglévő példánya hálózati figyelőt. A forgatókönyv feltételezi, hogy létezik-e egy erőforráscsoportot, egy érvényes virtuális géppel használandó.

## <a name="scenario"></a>Forgatókönyv

Ezen forgatókönyv által használt IP-adatfolyam ellenőrizze ellenőrizheti, ha egy virtuális gép működik egy ismert Bing IP-címre. Ha a forgalmat a rendszer megtagadja, a biztonsági szabály, amely megtagadja a forgalom adja vissza. IP-adatfolyam bővebben ellenőrizze megismeréséhez látogasson el [IP folyamat ellenőrzése – áttekintés](network-watcher-ip-flow-verify-overview.md)

## <a name="retrieve-network-watcher"></a>Hálózati figyelőt beolvasása

Az első lépés a hálózati figyelőt példányának lekéréséhez. A `$networkWatcher` változó átadása az IP-adatfolyam parancsmag ellenőrzése.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>A virtuális gép beolvasása

IP-adatfolyam tesztek bejövő és kimenő forgalmat a virtuális gép IP-címet vagy egy távoli céljához ellenőrzése. A virtuális gép azonosítóját a parancsmag szükség. Ha már ismeri az Azonosítót a virtuális gép használja, kihagyhatja ezt a lépést.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="get-the-nics"></a>A hálózati adapterek beolvasása

A virtuális gépen egy hálózati adapter IP-címe van szükség. A hálózati adaptert egy virtuális géphez, a következő parancs beolvasása. Ha már ismeri a virtuális gépen vizsgálni kívánt IP-cím, kihagyhatja ezt a lépést.

```powershell
$Nics = Get-AzureRmNetworkInterface | Where {$_.Id -eq $vm.NetworkProfile.NetworkInterfaces.Id.ForEach({$_})}
```

## <a name="run-ip-flow-verify"></a>Futtatási IP-adatfolyam ellenőrzése

Futtassa a `Test-AzureRmNetworkWatcherIPFlow` parancsmag segítségével tesztelheti a forgalmat. Ebben a példában az első IP-címet az első hálózati adapter által használt.

```powershell
Test-AzureRmNetworkWatcherIPFlow -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id `
-Direction Outbound -Protocol TCP `
-LocalIPAddress $nics[0].IpConfigurations[0].PrivateIpAddress -LocalPort 6895 -RemoteIPAddress 204.79.197.200 -RemotePort 80
```

> [!NOTE]
> IP-adatfolyam győződjön meg arról, hogy a virtuális gép erőforrásához lefoglalt futtatásához szükséges.

## <a name="review-results"></a>Tekintse át az eredményeket

Futtatása után `Test-AzureRmNetworkWatcherIPFlow` eredményeinek, az alábbi példa az előző lépésben adott eredmények.

```
Access RuleName                                  
------ --------                                  
Allow  defaultSecurityRules/AllowInternetOutBound
```

## <a name="next-steps"></a>További lépések

Ha a forgalmat blokkol, és nem kell, lásd: [hálózati biztonsági csoportok kezelése](../virtual-network/manage-network-security-group.md) nyomon követheti a hálózati biztonsági csoport és a biztonsági meghatározott szabályokat.

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png













