---
title: "Terheléselosztó SQL Server konfigurálása mindig a |} Microsoft Docs"
description: "SQL Server Always On együttműködésre, és megtudhatja, hogyan PowerShell használatával történő létrehozásáról az SQL Server végrehajtási terheléselosztó terheléselosztó konfigurálása"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: d7bc3790-47d3-4e95-887c-c533011e4afd
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 5e890f8314c8f191dbfa6c6818d810b91d0e829d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="configure-a-load-balancer-for-sql-server-always-on"></a>A terheléselosztó konfigurálása az SQL Server Always On

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

SQL Server Always On rendelkezésre állási csoportok most futtathatja a belső terheléselosztót. Rendelkezésre állási csoport egy SQL Server flagship megoldás magas rendelkezésre állású és vész-helyreállítási. A rendelkezésre állási csoport figyelőjének ügyfélalkalmazások teszi lehetővé az elsődleges másodpéldány, függetlenül a konfigurációban replikák száma zökkenőmentesen csatlakozni.

A figyelő (DNS) nevet egy elosztott terhelésű IP-cím van leképezve. Az Azure Load Balancer irányítja a bejövő forgalom csak az elsődleges kiszolgálóra a replika.

Belső terheléselosztó támogatás az SQL Server Always On (figyelő) végpontok is használhatja. Most már tudja befolyásolni a kisegítő lehetőségek a figyelő. Az elosztott terhelésű IP-cím közül választhat egy bizonyos alhálózat a virtuális hálózat.

A belső terheléselosztó a figyelőnél, az SQL Server endpoint (például a kiszolgáló = tcp:ListenerName, 1433; adatbázis = DatabaseName) csak úgy érhető el:

* Szolgáltatások és az azonos virtuális hálózatban lévő virtuális gépek.
* Szolgáltatások és virtuális gépek csatlakoztatott helyszíni hálózatról.
* Szolgáltatások és virtuális gépek a virtuális hálózatok egymáshoz.

![SQL Server Always On belső terheléselosztó](./media/load-balancer-configure-sqlao/sqlao1.png)

## <a name="add-an-internal-load-balancer-to-the-service"></a>A belső terheléselosztók hozzáadása a szolgáltatás

1. A következő példában egy virtuális hálózatot, amely tartalmazza a "Alhálózat-1" nevű alhálózat konfigurálása:

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. Adja hozzá a belső terheléselosztók végpontjainak elosztott terhelésű minden egyes virtuális gépen.

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    Az előző példában, hogy két virtuális gépek "sqlsvc1" és "sqlsvc2" néven, amelyek a felhőalapú szolgáltatás "Sqlsvc" futnak. Miután létrehozta a belső terheléselosztót, a `DirectServerReturn` váltani, elosztott terhelésű végpont hozzáadása a belső terheléselosztó. Az elosztott terhelésű végpontokkal SQL Server rendelkezésre állási csoportok figyelői konfigurálásához.

SQL Server Always On kapcsolatos további információkért lásd: [belső terheléselosztót egy Always On rendelkezésre állási csoport konfigurálása az Azure-](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

## <a name="see-also"></a>Lásd még:
* [Első lépések egy nyilvános terheléselosztó konfigurálása](load-balancer-get-started-internet-arm-ps.md)
* [Bevezetés a belső terheléselosztók konfigurálásába](load-balancer-get-started-ilb-arm-ps.md)
* [A terheléselosztó elosztási módjának konfigurálása](load-balancer-distribution-mode.md)
* [A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)
