---
title: "Az SQL Server rendelkezésre állási csoportok – az Azure Virtual Machines - vészhelyreállítás |} Microsoft Docs"
description: "Ez a cikk azt ismerteti, hogyan egy SQL Server rendelkezésre állási csoport konfigurálásához és egy másik régióban egy replika Azure virtuális gépeken."
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: jhubbard
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
ms.openlocfilehash: 1ce90cf4bae66bfd6387a2698fd9b1ba7fc64595
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-always-on-availability-group-on-azure-virtual-machines-in-different-regions"></a>Always On rendelkezésre állási csoport konfigurálása az Azure virtuális gépeken különböző régiókban

Ez a cikk azt ismerteti, hogyan egy SQL Server Always On rendelkezésre állási csoportreplikákhoz konfigurálása a távoli Azure-beli hely Azure virtuális gépeken. Ez a konfiguráció használatát a vész-helyreállítási támogatásához.

Ez a cikk az Azure virtuális gépek erőforrás-kezelő módban vonatkozik.

Az alábbi ábrán egy rendelkezésre állási csoport gyakori telepítési Azure virtuális gépeken:

   ![Rendelkezésre állási csoport](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic.png)

A központi telepítésben lévő összes virtuális gépet egy Azure-régiót szerepelnek. A rendelkezésre állási csoport replikái szinkron véglegesítésre SQL-1 és SQL-2 automatikus feladatátvétellel is rendelkezhetnek. Build ebbe az architektúrába, lásd: [rendelkezésre állási csoport sablon vagy oktatóanyag](virtual-machines-windows-portal-sql-availability-group-overview.md).

Ez az architektúra téve az állásidő, ha az Azure-régió elérhetetlenné válik. Megoldásához, a biztonsági rést, adja hozzá a replika egy másik Azure-régiót. Az alábbi ábra bemutatja, hogyan új architektúrájának lenne:

   ![Rendelkezésre állási csoport vész-Helyreállítási](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic-dr.png)

A fenti ábrán látható SQL-3 nevű új virtuális gépet. SQL-3 egy másik Azure-régióban van. SQL-3 kerül a Windows Server feladatátvételi fürtszolgáltatás. SQL-3 rendelkezhet egy rendelkezésre állási csoportreplikákhoz. Végül figyelje meg, hogy az Azure-régió, az SQL-3 rendelkezik-e egy új Azure terheléselosztó.

>[!NOTE]
> Egy Azure rendelkezésre állási csoportot, ha egynél több virtuális gép ugyanabban a régióban van. Ha csak egy virtuális gépet a régióban van, majd a rendelkezésre állási csoport nincs szükség. Csak egy virtuális gép elhelyezheti rendelkezésre állási készlet létrehozása során. Ha a virtuális gép már a rendelkezésre állási csoportok, később további replika virtuális gép is hozzáadhat.

Ebben a rendszerben a replika a távoli régióban általában konfigurálják, az aszinkron véglegesítésű rendelkezésre állási mód és a feladatátvételi módot kézire.

Ha rendelkezésre állási csoport replikái az Azure virtuális gépek különböző Azure-régiókban, minden egyes régió van szükség:

* A virtuális hálózati átjáró
* A virtuális hálózati átjáró kapcsolat

Az alábbi ábrán látható a hálózatok adatközpontok közötti kommunikációra.

   ![Rendelkezésre állási csoport](./media/virtual-machines-windows-portal-sql-availability-group-dr/01-vpngateway-example.png)

>[!IMPORTANT]
>Ez az architektúra terhel kimenő adatok Azure-régiók közötti replikált adatokat. Lásd: [sávszélesség árképzési](http://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Távoli replika létrehozása

A replika létrehozásához egy távoli adatközpontban, hajtsa végre az alábbi lépéseket:

1. [Új virtuális hálózat létrehozása](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

1. [Konfigurálja a VNet – VNet-kapcsolatot az Azure portál használatával](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >Bizonyos esetekben előfordulhat, hogy a VNet – VNet-kapcsolat létrehozása a PowerShell használatával. Például különböző Azure számlák használatakor nem konfigurálhatja a kapcsolat a portálon. Ebben az esetben megtekintéséhez [konfigurálja a VNet – VNet-kapcsolatot az Azure portál használatával](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Új tartományvezérlőt hozzon létre](../../../active-directory/active-directory-new-forest-virtual-machine.md).

   Ez a tartományvezérlő biztosítja a hitelesítést, ha a tartományvezérlő az elsődleges helyen nem érhető el.

1. [SQL Server virtuális gép létrehozása az új](virtual-machines-windows-portal-sql-server-provision.md).

1. [Hozzon létre egy Azure terheléselosztó a régió, új hálózat](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

   Ez a terheléselosztó kell:

   - Az új virtuális gépként kell azonos hálózati és alhálózati.
   - Egy statikus IP-címet beállítani a rendelkezésre állási csoport figyelőjét.
   - Amely csak a virtuális gépek ugyanabban a régióban, mint a terheléselosztó háttérkészletének tartalmazza.
   - Egy adott IP-címre TCP port mintavételt használja.
   - Terheléselosztási szabály adott, az SQL Server ugyanabban a régióban van.  

1. [Vegye fel a Feladatátvételi fürtszolgáltatást az új SQL Server](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Az új SQL-kiszolgáló csatlakoztatása a tartományhoz](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

1. [Állítsa be az új SQL Server szolgáltatásfiókja egy tartományi fiók használatára](virtual-machines-windows-portal-sql-availability-group-prereq.md#setServiceAccount).

1. [Az új SQL-kiszolgáló hozzáadása a Windows Server feladatátvételi fürtszolgáltatás](virtual-machines-windows-portal-sql-availability-group-tutorial.md#addNode).

1. Hozzon létre egy IP-cím erőforrás a fürtön.

   Az IP-cím erőforrás a Feladatátvevőfürt-kezelő hozhat létre. A rendelkezésre állási csoport ellenőrzéshez kattintson jobb gombbal kattintson **erőforrás hozzáadása**, **több erőforrások**, és kattintson a **IP-cím**.

   ![IP-cím létrehozása](./media/virtual-machines-windows-portal-sql-availability-group-dr/20-add-ip-resource.png)

   Az IP-cím konfigurálása a következőképpen:

   - A távoli adatközpontból a hálózatot használja.
   - Az IP-címet hozzárendelni az új Azure load balancer. 

1. Az új SQL-kiszolgálón az SQL Server Configuration Manager [engedélyezze az Always On rendelkezésre állási csoportok](http://msdn.microsoft.com/library/ff878259.aspx).

1. [Nyissa meg az új SQL Server tűzfalportjai](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

   A portszámokat, meg kell nyitnia a környezettől függ. Megnyitott portok a tükrözési végpont és az Azure terheléselosztó betölteni.

1. [Adja hozzá a replikát a rendelkezésre állási csoporthoz, az új SQL Server](http://msdn.microsoft.com/library/hh213239.aspx).

   A replika a távoli Azure-régió állítsa kézi feladatátvételre aszinkron replikációra.  

1. Adja hozzá az IP-cím erőforrás a figyelő ügyfél-hozzáférési pont (hálózatnév) fürt függőségei.

   Az alábbi képernyőfelvételen látható egy megfelelően konfigurált IP-cím fürt erőforrás:

   ![Rendelkezésre állási csoport](./media/virtual-machines-windows-portal-sql-availability-group-dr/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >A fürterőforrás-csoport mindkét IP-címeket tartalmaz. Mindkét IP-címei a figyelő ügyfél-hozzáférési pont a függőségeit. Használja a **vagy** operátor szerepel a fürtkonfiguráció függőségi.

1. [A fürt paraméterek beállítása a PowerShell](virtual-machines-windows-portal-sql-availability-group-tutorial.md#setparam).

Futtassa a PowerShell-parancsfájlt a fürt hálózati név, IP-cím és a terheléselosztón új konfigurált mintavételi portot.

   ```PowerShell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = “<n.n.n.n>” # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

## <a name="set-connection-for-multiple-subnets"></a>Set-kapcsolatot a több alhálózattal

A távoli adatközpontban a replikát a rendelkezésre állási csoport része, de egy másik alhálózat van. Ha ez a másodpéldány az elsődleges másodpéldány, alkalmazás időtúllépésnek fordulhat elő. Ez a viselkedés megegyezik a helyi rendelkezésre állási csoport egy több alhálózatos környezetben. Lehetővé teszik az alkalmazások ügyfél érkező kapcsolatokat, vagy az ügyfél-kapcsolat frissítése, vagy konfigurálhatja a névfeloldást a fürt hálózati név típusú erőforráshoz gyorsítótárazás.

Lehetőleg, frissítse az ügyfél kapcsolati karakterláncok beállítása `MultiSubnetFailover=Yes`. Lásd: [kapcsolódás a MultiSubnetFailover](http://msdn.microsoft.com/library/gg471494#Anchor_0).

A kapcsolati karakterláncok nem módosítható, ha a megoldás-gyorsítótárazás is konfigurálhatja. Lásd: [kapcsolat időtúllépése több alhálózatot a rendelkezésre állási csoportban](http://blogs.msdn.microsoft.com/alwaysonpro/2014/06/03/connection-timeouts-in-multi-subnet-availability-group/).

## <a name="fail-over-to-remote-region"></a>Feladatok átadása a távoli régió

A távoli régióban figyelő kapcsolat tesztelése, feladatokat átveheti a replikát a távoli régióban. Bár a replika aszinkron, a feladatátvétel során téve az esetleges adatvesztés. Feladatok átadása a adatvesztés nélkül, állítsa át a rendelkezésre állási módot szinkron, és a feladatátvételi módot automatikusra van állítva. Ehhez a következő lépések szükségesek:

1. A **Object Explorer**, kapcsolódjon az elsődleges másodpéldányt futtató SQL Server-példányhoz.
1. A **AlwaysOn rendelkezésre állási csoportok**, **rendelkezésre állási csoportok**, kattintson a jobb gombbal a rendelkezésre állási csoportot, és kattintson a **tulajdonságok**.
1. Az a **általános** lap **rendelkezésre állási másodpéldányok**, állítsa be a másodlagos replika használata a vész-Helyreállítási helyen **szinkron véglegesítés** rendelkezésre állási módjának és **automatikus** feladatátvételi mód.
1. Ha egy másodlagos másodpéldány az elsődleges replika, a magas rendelkezésre állású azonos helyen van, állítson be ennek a replikának **aszinkron véglegesítés** és **manuális**.
1. Kattintson az OK gombra.
1. A **Object Explorer**, kattintson a jobb gombbal a rendelkezésre állási csoportot, és kattintson a **irányítópult megjelenítése**.
1. Az irányítópulton győződjön meg arról, hogy a replika a vész-Helyreállítási helyen szinkronizálva.
1. A **Object Explorer**, kattintson a jobb gombbal a rendelkezésre állási csoportot, és kattintson a **feladatátvételi...** . SQL Server Management Studios a feladatátvételt az SQL Server egy varázsló megnyitása.  
1. Kattintson a **következő**, és válassza ki az SQL Server-példány a vész-Helyreállítási helyen. Kattintson a **következő** újra.
1. A vész-Helyreállítási hely SQL Server-példányhoz való csatlakozáshoz, és kattintson a **következő**.
1. Az a **összegzés** lapon ellenőrizze a beállításokat, majd kattintson **Befejezés**.

Kapcsolat tesztelése után helyezze vissza az elsődleges másodpéldány az elsődleges adatközpont, és állítsa be a rendelkezésre állási mód az normál működési értékekre. Az alábbi táblázat a jelen dokumentumban leírt architektúra a rendes működési beállításait:

| Hely | Server-példány | Szerepkör | Rendelkezésre állási mód | Feladatátvételi mód
| ----- | ----- | ----- | ----- | -----
| Elsődleges adatközpont | SQL-1 | Elsődleges | Szinkron | Automatikus
| Elsődleges adatközpont | SQL-2 | Másodlagos | Szinkron | Automatikus
| Távoli vagy másodlagos adatközpont | SQL-3 | Másodlagos | Aszinkron | Manuális


### <a name="more-information-about-planned-and-forced-manual-failover"></a>További információ a tervezett és a rendszer kényszerített kézi feladatátvétel

További információkért tekintse át a következők témaköröket:

- [Végezzen el egy tervezett kézi feladatátvételt egy rendelkezésre állási csoport (SQL Server)](http://msdn.microsoft.com/library/hh231018.aspx)
- [Hajtsa végre a kényszerített kézi feladatátvétel egy rendelkezésre állási csoport (SQL Server)](http://msdn.microsoft.com/library/ff877957.aspx)

## <a name="additional-links"></a>További hivatkozások

* [Always On rendelkezésre állási csoportok](http://msdn.microsoft.com/library/hh510230.aspx)
* [Az Azure virtuális gépek](http://docs.microsoft.com/azure/virtual-machines/windows/)
* [Azure Load Balancer Terheléselosztók](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)
* [Az Azure rendelkezésre állási csoportok](../manage-availability.md)
