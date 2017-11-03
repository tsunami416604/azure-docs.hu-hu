---
title: "Konfigurálása terheléselosztó SQL mindig |} Microsoft Docs"
description: "Terheléselosztó együttműködni SQL mindig és hogyan használhatók ki a terheléselosztó SQL végrehajtása létrehozásához powershell konfigurálása"
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
ms.openlocfilehash: 3ebbf1c4009d89b1f18b2ff8ff5dd243c456dff8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-load-balancer-for-sql-always-on"></a>Konfigurálása terheléselosztó SQL mindig

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

SQL Server AlwaysOn rendelkezésre állási csoportok futtatható a Példánynak. Rendelkezésre állási csoport egy SQL Server flagship megoldás magas rendelkezésre állási és vészhelyreállítási helyreállításhoz. A rendelkezésre állási csoport figyelőjének ügyfélalkalmazások teszi lehetővé az elsődleges másodpéldány, függetlenül a konfigurációban a replikák száma zökkenőmentesen csatlakozni.

A figyelő (DNS) nevet egy elosztott terhelésű IP-cím van leképezve, és Azure terheléselosztója irányítja a bejövő forgalom csak az elsődleges kiszolgálóra a replika.

Az SQL Server AlwaysOn (figyelő) végpontokhoz ILB támogatási is használhatja. Most a figyelő a kisegítő szabályozhatják, és az elosztott terhelésű IP-cím közül választhatnak a megadott alhálózat a virtuális hálózat (VNet).

A figyelő az SQL server endpoint ILB használatával (pl. Server = tcp:ListenerName, 1433; adatbázis = DatabaseName) csak által elérhető:

* Szolgáltatások és az azonos virtuális hálózatban lévő virtuális gépek
* Szolgáltatások és virtuális gépek csatlakoztatott helyszíni hálózatról
* Szolgáltatások és az összekapcsolt Vnetek virtuális gépek

![ILB_SQLAO_NewPic](./media/load-balancer-configure-sqlao/sqlao1.png)

1. ábra – az SQL AlwaysOn konfigurálva az Internet felé néző terheléselosztó

## <a name="add-internal-load-balancer-to-the-service"></a>A szolgáltatás belső terheléselosztó hozzáadása

1. A következő példában azt egy virtuális hálózatot, amely tartalmazza a "Alhálózat-1" nevű alhálózat konfigurálhatja:

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. Elosztott terhelésű végpont-hozzáadáshoz a Példánynak az egyes virtuális gépek

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    A fenti példában rendelkezik 2 VM hívott "sqlsvc1" és "sqlsvc2" fut a felhőben található "Sqlsvc" szolgáltatás. Miután létrehozta a ILB `DirectServerReturn` kapcsoló ad hozzá elosztott terhelésű végpont a Példánynak, hogy az SQL rendelkezésre állási csoportok figyelői konfigurálása.

Az SQL AlwaysOn szolgáltatással kapcsolatos további információkért lásd: [belső terheléselosztót egy AlwaysOn rendelkezésre állási csoport konfigurálása az Azure-](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

## <a name="see-also"></a>Lásd még:
[Ismerkedés az Internet felé néző terheléselosztó konfigurálása](load-balancer-get-started-internet-arm-ps.md)

[Ismerkedés a belső terheléselosztók konfigurálása](load-balancer-get-started-ilb-arm-ps.md)

[A terheléselosztó elosztási módjának konfigurálása](load-balancer-distribution-mode.md)

[A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)
