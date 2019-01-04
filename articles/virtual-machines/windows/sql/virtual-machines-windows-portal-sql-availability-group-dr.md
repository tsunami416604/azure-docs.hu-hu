---
title: SQL Server rendelkezésre állási csoportok – az Azure Virtual Machines - vészhelyreállítás |} A Microsoft Docs
description: Ez a cikk bemutatja egy SQL Server rendelkezésre állási csoport konfigurálása és a egy másik régióban lévő replika Azure virtuális gépeken.
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: d64c55857cda0aa64dc010566490e1696fffdea0
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53972379"
---
# <a name="configure-an-always-on-availability-group-on-azure-virtual-machines-in-different-regions"></a>Az Azure-beli virtuális gépek különböző régiókban Always On rendelkezésre állási csoport konfigurálása

Ez a cikk bemutatja egy SQL Server Always On rendelkezésre állási csoportreplikákhoz konfigurálása Azure-beli virtuális gépeken egy távoli Azure-beli helyen. Ez a konfiguráció használatával támogatja a vészhelyreállítást.

Ez a cikk az Azure Virtual Machines Resource Manager módra vonatkozik.

Az alábbi képen látható a gyakori telepítési egy rendelkezésre állási csoport Azure-beli virtuális gépeken:

   ![Rendelkezésre állási csoport](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic.png)

A központi telepítésben lévő összes virtuális gépet egy Azure-régióban vannak. A rendelkezésre állási csoport replikái lehet szinkron véglegesítést SQL-1 és az SQL-2 automatikus feladatátvétellel. Ez az architektúra hozhat létre, tekintse meg a [rendelkezésre állási csoport sablon vagy az oktatóanyagban](virtual-machines-windows-portal-sql-availability-group-overview.md).

Ez az architektúra olyan sebezhető az állásidő, ha az Azure-régió elérhetetlenné válik. Hogy a biztonsági rés, adjon hozzá egy replikát egy másik Azure-régióban. Az alábbi ábrán látható, hogyan néz meg az új architektúra:

   ![Rendelkezésre állási csoport DR](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic-dr.png)

A fentebbi ábra bemutatja azokat a nevű SQL-3 új virtuális gépet. SQL-3 különböző Azure-régióban van. SQL-3 hozzáadódik a Windows Server feladatátvételi fürtben. SQL-3 üzemeltethető egy rendelkezésre állási csoportreplikákhoz. Végül vegye figyelembe, hogy rendelkezik-e egy új Azure-terheléselosztó az Azure SQL-3-régió.

>[!NOTE]
> Azure rendelkezésre állási kötelező, ha egynél több virtuális gép ugyanabban a régióban van. Ha csak egy virtuális gépet a régióban van, majd a rendelkezésre állási csoport nem kötelező. Csak egy virtuális gépet elhelyezheti egy rendelkezésre állási csoportot a létrehozás időpontjában. Ha a virtuális gép már van egy rendelkezésre állási csoportban, később további replika virtuális gép is hozzáadhat.

Ebben az architektúrában a replikát a távoli régióban megfelelően konfigurálva az aszinkron véglegesítésű rendelkezésre állási mód és kézi feladatátvételi módra.

Ha rendelkezésre állási csoport replikái az Azure virtuális gépek eltérő Azure-régióban, minden régióban van szükség:

* A virtuális hálózati átjáró
* Egy virtuális hálózati átjárókapcsolat

Az alábbi ábrán látható a hálózatok közötti kommunikáció módját az adatközpontok között.

   ![Rendelkezésre állási csoport](./media/virtual-machines-windows-portal-sql-availability-group-dr/01-vpngateway-example.png)

>[!IMPORTANT]
>Ez az architektúra a kimenő adatforgalmi díjat az Azure-régiók között replikált adatok tekintetében. Lásd: [sávszélesség díjszabás](https://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Távoli replika létrehozása

A replika létrehozásához egy távoli adatközpontban, tegye a következőket:

1. [Virtuális hálózat létrehozása az új régióban](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

1. [Az Azure Portalon a virtuális hálózatok közötti kapcsolat konfigurálása](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >Bizonyos esetekben előfordulhat, hogy rendelkezik a virtuális hálózatok közötti kapcsolat létrehozása a PowerShell használatával. Például különböző Azure-fiókok használatakor nem konfigurálhatja a kapcsolatot a portálon. Ebben az esetben láthatja, [az Azure Portalon a virtuális hálózatok közötti kapcsolat konfigurálása](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Az új régióban hozzon létre egy olyan tartományvezérlő](../../../active-directory/active-directory-new-forest-virtual-machine.md).

   Ez a tartományvezérlő hitelesítést biztosít, ha az elsődleges helyen a tartományvezérlő nem érhető el.

1. [Az SQL Server virtuális gép létrehozása az új régióban](virtual-machines-windows-portal-sql-server-provision.md).

1. [Hozzon létre egy Azure load balancert az új régióban lévő hálózat](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

   Ez a terheléselosztó kell:

   - Lennie ugyanazt a hálózatot és alhálózatot az új virtuális gépet.
   - A rendelkezésre állási csoport figyelőjének statikus IP-címmel rendelkezik.
   - Csak a terheléselosztó az azonos régióban lévő virtuális gépek álló háttérkészlet tartalmazza.
   - TCP-port mintavételt adott IP-címet használja.
   - Terheléselosztási szabály adott, az SQL Server ugyanabban a régióban van.  
   - Standard Load Balancer akkor lehet, ha a háttérkészletben lévő virtuális gépek nem részei egyetlen rendelkezésre állási csoportban vagy virtuálisgép-méretezési csoportot. További információkat a [áttekintése az Azure Load Balancer Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

1. [Vegye fel a Feladatátvételi fürtszolgáltatást az új SQL Server](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Az új SQL-kiszolgáló csatlakoztatása a tartományhoz](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

1. [Állítsa be az új SQL Server szolgáltatásfiókja tartományi fiók használatára](virtual-machines-windows-portal-sql-availability-group-prereq.md#setServiceAccount).

1. [Az új SQL-kiszolgáló hozzáadása a Windows Server feladatátvételi fürt](virtual-machines-windows-portal-sql-availability-group-tutorial.md#addNode).

1. Hozzon létre egy IP-cím erőforrás a fürtön.

   Az IP-cím erőforrás a Feladatátvevőfürt-kezelő hozhat létre. Kattintson a jobb gombbal az rendelkezésre állási csoport **erőforrás hozzáadása**, **további erőforrások**, és kattintson a **IP-cím**.

   ![IP-cím létrehozása](./media/virtual-machines-windows-portal-sql-availability-group-dr/20-add-ip-resource.png)

   Az IP-cím a következőképpen konfigurálja:

   - Használja a hálózatot a távoli adat központból.
   - Az IP-címet hozzárendelni az új Azure load balancert. 

1. Az új SQL-kiszolgálón az SQL Server Configuration Manager [engedélyezze az Always On rendelkezésre állási csoportok](https://msdn.microsoft.com/library/ff878259.aspx).

1. [Nyissa meg az új SQL Server tűzfalportok](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

   A portszámokat, meg kell nyitnia a környezettől függ. Portok megnyitása a tükrözési végpont és az Azure terheléselosztó állapotmintája betölteni.

1. [Adjon hozzá egy replikát a rendelkezésre állási csoporthoz, az új SQL Server](https://msdn.microsoft.com/library/hh213239.aspx).

   A replika egy távoli Azure-régióban állítsa a manuális feladatátvételt az aszinkron replikáció.  

1. Adja hozzá az IP-cím-erőforrást is felvettek a figyelő ügyfél-hozzáférési pont (hálózatnév) fürt.

   Az alábbi képernyőfelvételen egy megfelelően konfigurált IP-cím fürt erőforrás:

   ![Rendelkezésre állási csoport](./media/virtual-machines-windows-portal-sql-availability-group-dr/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >A fürterőforrás-csoport tartalmazza az IP-címe. IP-címe. a figyelő ügyfél-hozzáférési pont a függőségekkel. Használja a **vagy** operátor szerepel a fürtkonfiguráció függőség.

1. [A fürt paraméterek beállítása a PowerShell](virtual-machines-windows-portal-sql-availability-group-tutorial.md#setparam).

A PowerShell-parancsfájl futtatása a fürthálózat nevének, az IP-cím és a mintavételi portot a load balancer az új régióban konfigurált.

   ```PowerShell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = “<n.n.n.n>” # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

## <a name="set-connection-for-multiple-subnets"></a>Több alhálózattal kapcsolat beállítása

A távoli adatközpontban a replikát a rendelkezésre állási csoport része, de van egy másik alhálózatot. Ha ez a másodpéldány az elsődleges másodpéldány, alkalmazás-kapcsolat időtúllépésének fordulhat elő. Ez a viselkedés megegyezik a helyi rendelkezésre állási csoport több alhálózatos környezetben. Ahhoz, hogy alkalmazásokat az ügyféltől érkező kapcsolatot, vagy frissíteni az ügyfél-kapcsolatot, vagy gyorsítótárazása a hálózat neve fürterőforrás névfeloldást.

Lehetőleg, frissítse az ügyfél-kapcsolati karakterláncok beállítása `MultiSubnetFailover=Yes`. Lásd: [összeköti a MultiSubnetFailover](https://msdn.microsoft.com/library/gg471494#Anchor_0).

A kapcsolati karakterláncok nem módosítható, ha a név megoldási gyorsítótár is konfigurálhatja. Lásd: [kapcsolat időtúllépésének több alhálózatot a rendelkezésre állási csoportban](https://blogs.msdn.microsoft.com/alwaysonpro/2014/06/03/connection-timeouts-in-multi-subnet-availability-group/).

## <a name="fail-over-to-remote-region"></a>Feladatátvételt távoli régióba

A távoli régióba figyelő kapcsolat teszteléséhez, adhatók át a replikát a távoli régióba. Bár a replika aszinkron, feladatátvételi ki téve a potenciális adatvesztés. A feladatátvételt adatvesztés nélküli, módosítsa a rendelkezésre állási módot szinkron, és a feladatátvételi mód beállítása automatikusra. Ehhez a következő lépések szükségesek:

1. A **Object Explorer**, kapcsolódjon az elsődleges másodpéldányt futtató SQL Server-példányhoz.
1. A **AlwaysOn rendelkezésre állási csoportok**, **rendelkezésre állási csoportok**, kattintson a jobb gombbal a rendelkezésre állási csoport, és kattintson a **tulajdonságok**.
1. Az a **általános** lap **rendelkezésre állási másodpéldányok**, állítsa be a másodlagos replikát a DR-hely használandó **szinkron véglegesítés** rendelkezésre állási mód és  **Automatikus** feladatátvételi mód.
1. Ha rendelkezik egy másodlagos másodpéldány az elsődleges replika, a magas rendelkezésre álláshoz azonos helyen, állítsa a replika **aszinkron véglegesítés** és **manuális**.
1. Kattintson az OK gombra.
1. A **Object Explorer**, kattintson a jobb gombbal a rendelkezésre állási csoport, és kattintson a **megjelenítése irányítópult**.
1. Az irányítópulton győződjön meg arról, hogy a rendszer szinkronizálja a replikát a DR-helyen.
1. A **Object Explorer**, kattintson a jobb gombbal a rendelkezésre állási csoport, és kattintson a **feladatátvételi...** . Az SQL Server Management Studios a feladatátvételt az SQL Server egy varázsló megnyitása.  
1. Kattintson a **tovább**, és válassza ki az SQL Server-példány a DR-hely. Kattintson a **tovább** újra.
1. Az SQL Server-példányok a DR-helyhez csatlakozik, és kattintson a **tovább**.
1. Az a **összefoglalás** lapon ellenőrizze a beállításokat, és kattintson **Befejezés**.

Kapcsolat tesztelése után lépjen vissza az elsődleges replika az elsődleges adatközpont és a rendelkezésre állási mód állítsa vissza a rendes működési beállításait. Az alábbi táblázat a jelen dokumentumban ismertetett architektúra normál működési beállításai láthatók:

| Hely | Server-példány | Szerepkör | Rendelkezésre állási mód | Feladatátvételi mód
| ----- | ----- | ----- | ----- | -----
| Elsődleges adatközpont | SQL-1 | Elsődleges | Szinkron | Automatikus
| Elsődleges adatközpont | SQL-2 | Másodlagos | Szinkron | Automatikus
| Másodlagos vagy távoli adatközpontban | SQL-3 | Másodlagos | Aszinkron | Manuális


### <a name="more-information-about-planned-and-forced-manual-failover"></a>További információ a tervezett és a kényszerített manuális feladatátvétel

További információkért tekintse át a következők témaköröket:

- [Végezzen el egy tervezett kézi feladatátvételt egy rendelkezésre állási csoport (SQL Server)](https://msdn.microsoft.com/library/hh231018.aspx)
- [Egy rendelkezésre állási csoport (SQL Server) kényszerített kézi feladatátvétel végrehajtása](https://msdn.microsoft.com/library/ff877957.aspx)

## <a name="additional-links"></a>További hivatkozások

* [Always On rendelkezésre állási csoportok](https://msdn.microsoft.com/library/hh510230.aspx)
* [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/)
* [Az Azure Load Balancer Terheléselosztók](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)
* [Az Azure rendelkezésre állási csoportok](../manage-availability.md)
