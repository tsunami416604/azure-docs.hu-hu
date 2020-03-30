---
title: Rendelkezésre állási csoport konfigurálása különböző régiók között
description: Ez a cikk bemutatja, hogyan konfigurálhat egy SQL Server-rendelkezésre állási csoportot az Azure virtuális gépeken egy másik régióban lévő replikával.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 8ca871a6f525d4e68ce70060e6faddbcfc8e1f3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060115"
---
# <a name="configure-an-availability-group-on-azure-sql-server-virtual-machines-in-different-regions"></a>Rendelkezésre állási csoport konfigurálása azure SQL Server virtuális gépeken különböző régiókban

Ez a cikk bemutatja, hogyan konfigurálhatja az SQL Server Mindig rendelkezésre állási csoport replika az Azure virtuális gépek en egy távoli Azure helyen. Ezzel a konfigurációval támogatja a vészhelyreállítást.

Ez a cikk az Azure virtuális gépek erőforrás-kezelő módban vonatkozik.

Az alábbi képen egy rendelkezésre állási csoport gyakori üzembe helyezése látható az Azure virtuális gépeken:

   ![Rendelkezésreállási csoport](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic.png)

Ebben a központi telepítésben az összes virtuális gép egy Azure-régióban található. A rendelkezésre állási csoport replikák szinkron véglegesítése az automatikus feladatátvétel az SQL-1 és az SQL-2. Az architektúra létrehozásáról a [Rendelkezésre állási csoport sablonvagy oktatóanyag](virtual-machines-windows-portal-sql-availability-group-overview.md)olvashat.

Ez az architektúra ki van téve az állásidő, ha az Azure-régió elérhetetlenné válik. A biztonsági rés megszüntetéséhez adjon hozzá egy replikát egy másik Azure-régióban. Az alábbi ábra bemutatja, hogyan nézne ki az új architektúra:

   ![Rendelkezésre állási csoport DR](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic-dr.png)

Az előző ábrán egy új virtuális gép, az SQL-3 látható. Az SQL-3 egy másik Azure-régióban található. Az SQL-3 hozzáadódik a Windows Server feladatátvevő fürthöz. Az SQL-3 üzemeltethet egy rendelkezésre állási csoport replikát. Végül figyelje meg, hogy az Azure-régió SQL-3 rendelkezik egy új Azure-terheléselosztó.

>[!NOTE]
> Az Azure rendelkezésre állási készlete akkor szükséges, ha egynél több virtuális gép ugyanabban a régióban van. Ha csak egy virtuális gép van a régióban, majd a rendelkezésre állási készlet nem szükséges. A virtuális gépet csak a létrehozás időpontjában helyezheti el egy rendelkezésre állási készletben. Ha a virtuális gép már rendelkezésre állási csoportban van, később hozzáadhat egy virtuális gépet egy további kópiához.

Ebben az architektúrában a távoli régióban a replika általában aszinkron véglegesítési rendelkezésre állási móddal és manuális feladatátvételi móddal van konfigurálva.

Ha a rendelkezésre állási csoport replikái különböző Azure-régiókban lévő Azure-virtuális gépeken találhatók, minden régiónak szüksége van a következőkre:

* Virtuális hálózati átjáró
* Virtuális hálózati átjárókapcsolat

Az alábbi ábra bemutatja, hogyan kommunikálnak a hálózatok az adatközpontok között.

   ![Rendelkezésreállási csoport](./media/virtual-machines-windows-portal-sql-availability-group-dr/01-vpngateway-example.png)

>[!IMPORTANT]
>Ez az architektúra kimenő adatforgalmi díjakat von maga után az Azure-régiók között replikált adatokért. Lásd: [Sávszélesség-díjszabás](https://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Távoli kópia létrehozása

Ha távoli adatközpontban szeretne kópiát létrehozni, tegye a következő lépéseket:

1. [Hozzon létre egy virtuális hálózatot az új régióban.](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network)

1. [VNet-vnet kapcsolat konfigurálása az Azure Portal használatával.](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)

   >[!NOTE]
   >Bizonyos esetekben előfordulhat, hogy a Virtuálishálózat és a virtuális hálózat között kapcsolat létrehozásához powershell használatával kell létrehoznia. Ha például különböző Azure-fiókokat használ, nem konfigurálhatja a kapcsolatot a portálon. Ebben az esetben [tekintse meg: Virtuális hálózat és virtuális hálózat között kapcsolat konfigurálása az Azure Portal használatával.](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

1. [Hozzon létre egy tartományvezérlőt az új régióban](../../../active-directory/active-directory-new-forest-virtual-machine.md).

   Ez a tartományvezérlő hitelesítést biztosít, ha az elsődleges helyen lévő tartományvezérlő nem érhető el.

1. [Sql Server virtuális gép létrehozása az új régióban.](virtual-machines-windows-portal-sql-server-provision.md)

1. [Hozzon létre egy Azure-terheléselosztót a hálózatban az új régióban.](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)

   Ennek a terheléselosztónak:

   - Legyen ugyanabban a hálózatban és alhálózatban, mint az új virtuális gép.
   - A rendelkezésre állási csoport figyelője statikus IP-címet.
   - Include egy háttérkészlet, amely csak a virtuális gépek ugyanabban a régióban, mint a terheléselosztó.
   - Az IP-címre jellemző TCP-portmintavétel használata.
   - Ugyanabban a régióban az SQL Serverre vonatkozó terheléselosztási szabály.  
   - Standard terheléselosztó legyen, ha a háttérkészletben lévő virtuális gépek nem részei egyetlen rendelkezésre állási csoportnak vagy a virtuálisgép-méretezési csoportnak. További információkért tekintse át az [Azure Load Balancer Standard áttekintését.](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

1. [Adja hozzá a feladatátvevő fürtözési szolgáltatást az új SQL Server kiszolgálóhoz.](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms)

1. [Csatlakozzon az új SQL Server kiszolgálóhoz a tartományhoz](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

1. [Állítsa be az új SQL Server szolgáltatásfiókot tartományi fiók használatára.](virtual-machines-windows-portal-sql-availability-group-prereq.md#setServiceAccount)

1. [Adja hozzá az új SQL Server rendszert a Windows Server feladatátvevő fürthöz](virtual-machines-windows-portal-sql-availability-group-tutorial.md#addNode).

1. IP-címerőforrás hozzáadása a fürthöz.

   Az IP-cím erőforrást a Feladatátvevőfürt-kezelőben hozhatja létre. Jelölje ki a fürt nevét, majd kattintson a jobb gombbal a fürt nevére a **Fürtalapvető erőforrások csoportban,** és válassza a **Tulajdonságok parancsot:** 

   ![Fürt tulajdonságai](./media/virtual-machines-windows-portal-sql-availability-group-dr/cluster-name-properties.png)

   A **Tulajdonságok** párbeszédpanelen válassza a **Hozzáadás** az **IP-cím**csoportban lehetőséget, majd adja hozzá a fürtnév IP-címét a távoli hálózati régióból. Az ÚJ **IP-cím** mentéséhez kattintson az **OK** gombra az IP-cím **párbeszédpanelen,** majd ismét az **OK** gombra. 

   ![Fürt IP-címének hozzáadása](./media/virtual-machines-windows-portal-sql-availability-group-dr/add-cluster-ip-address.png)


1. Adja hozzá az IP-címet a központi fürt nevének függőségeként.

   Nyissa meg még egyszer a fürttulajdonságokat, és válassza a **Függőségek** lapot. 

   ![Fürt tulajdonságai](./media/virtual-machines-windows-portal-sql-availability-group-dr/cluster-ip-dependencies.png)

1. IP-címerőforrás hozzáadása a fürt rendelkezésre állási csoportszerepköréhez. 

   Kattintson a jobb gombbal a feladatátvevőfürt-kezelőben az elérhetőségi csoport szerepkörére, válassza **az Erőforrás hozzáadása**, További **erőforrások**lehetőséget, és válassza az **IP-cím parancsot.**

   ![IP-cím létrehozása](./media/virtual-machines-windows-portal-sql-availability-group-dr/20-add-ip-resource.png)

   Konfigurálja ezt az IP-címet az alábbiak szerint:

   - Használja a hálózatot a távoli adatközpontból.
   - Rendelje hozzá az IP-címet az új Azure-terheléselosztó. 

1. Adja hozzá az IP-cím erőforrást a figyelő ügyfél hozzáférési pontjának (hálózatnév) fürtjének függőségeként.

   Az alábbi képernyőképen egy megfelelően konfigurált IP-címfürt-erőforrás látható:

   ![Rendelkezésreállási csoport](./media/virtual-machines-windows-portal-sql-availability-group-dr/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >A fürterőforrás-csoport mindkét IP-címet tartalmazza. Mindkét IP-cím a figyelő ügyfél hozzáférési pontjának függőségei. Használja a **VAGY** operátort a fürtfüggőségi konfigurációban.

1. [Állítsa be a fürt paramétereit a PowerShellben.](virtual-machines-windows-portal-sql-availability-group-tutorial.md#setparam)

Futtassa a PowerShell-parancsfájlt a fürt hálózatának nevével, IP-címével és mintavételi portjával, amelyet az új régió terheléselosztóján konfigurált.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = "<n.n.n.n>" # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

1. Az SQL Server Configuration Manager új SQL Server kiszolgálóján [engedélyezze a Mindig rendelkezésre állási csoportok engedélyezése](/sql/database-engine/availability-groups/windows/enable-and-disable-always-on-availability-groups-sql-server).

1. [Nyissa meg a tűzfalportokat az új SQL Server kiszolgálón.](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall)

   A megnyitandó portszámok a környezettől függenek. Nyissa meg a portokat a tükrözési végponthoz és az Azure terheléselosztó állapotmintához.


1. [Kópia hozzáadása az új SQL Server rendelkezésre állási csoportjához](/sql/database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio).

   Egy távoli Azure-régióban lévő kópia, állítsa be az aszinkron replikáció kézi feladatátvétel.  

## <a name="set-connection-for-multiple-subnets"></a>Több alhálózat kapcsolatának beállítása

A távoli adatközpontban lévő replika a rendelkezésre állási csoport része, de egy másik alhálózatban található. Ha ez a replika lesz az elsődleges replika, az alkalmazás kapcsolatának időtúljáratai fordulhatnak elő. Ez a viselkedés megegyezik a többalhálózatos központi telepítés ben a helyszíni rendelkezésre állási csoportéval. Az ügyfélalkalmazásokból származó kapcsolatok engedélyezéséhez frissítse az ügyfélkapcsolatot, vagy konfigurálja a névfeloldási gyorsítótárazást a fürt hálózati néverőforrásán.

Lehetőleg frissítse az ügyfélkapcsolati `MultiSubnetFailover=Yes`karakterláncokat a beállításhoz. Lásd: [Csatlakozás a MultiSubnetFailover.](https://msdn.microsoft.com/library/gg471494#Anchor_0)

Ha nem tudja módosítani a kapcsolati karakterláncokat, beállíthatja a névfeloldási gyorsítótárazást. Lásd: [Időelési hiba, és nem tud csatlakozni az SQL Server 2012 AlwaysOn rendelkezésre állási csoportfigyelőjéhez többalhálózati környezetben.](https://support.microsoft.com/help/2792139/time-out-error-and-you-cannot-connect-to-a-sql-server-2012-alwayson-av)

## <a name="fail-over-to-remote-region"></a>Feladatátvétel a távoli régióba

A figyelő kapcsolatának tesztelése a távoli régióban, feladatátvételi a távoli régióban. Bár a replika aszinkron, a feladatátvétel ki van téve a lehetséges adatvesztés. Adatvesztés nélküli feladatátvételhez módosítsa a rendelkezésre állási módot szinkronra, és állítsa a feladatátvételi módot automatikusra. Ehhez a következő lépések szükségesek:

1. Az **Objektumkezelőben**csatlakozzon az SQL Server elsődleges replikát tartalmazó példányához.
1. Az **AlwaysOn rendelkezésre állási csoportok csoportban kattintson** **a**jobb gombbal a rendelkezésre állási csoportra, és kattintson a Tulajdonságok **parancsra.**
1. Az **Általános** lap **Rendelkezésre állási replikák**csoportban állítsa be a másodlagos replikát a VÉSZ-helyen a **szinkron véglegesítés** rendelkezésre állási mód és **az automatikus** feladatátvételi mód használatára.
1. Ha a magas rendelkezésre állás érdekében az elsődleges kópiával azonos helyen van másodlagos replika, állítsa ezt a **replikát Aszinkron véglegesítés** és **kézi lesz.**
1. Kattintson az OK gombra.
1. Az **Objektumkezelőben**kattintson a jobb gombbal az elérhetőségi csoportra, majd kattintson az **Irányítópult megjelenítése**parancsra.
1. Az irányítópulton ellenőrizze, hogy a vész-és fejlesztési hely replikája szinkronizálva van-e.
1. Az **Objektumkezelőben**kattintson a jobb gombbal a rendelkezésre állási csoportra, és válassza a **Feladatátvétel...**. Az SQL Server Management Studios megnyit egy varázslót, amely feladatátvételt ad az SQL Server kiszolgálónak.  
1. Kattintson a **Tovább**gombra, és jelölje ki az SQL Server-példányt a VÉSZ-helyen. Kattintson ismét a **Next** (Tovább) gombra.
1. Csatlakozzon a VÉSZ-hely SQL Server-példányához, és kattintson a **Tovább**gombra.
1. Az **Összegzés** lapon ellenőrizze a beállításokat, és kattintson a **Befejezés gombra.**

A kapcsolat tesztelése után helyezze vissza az elsődleges kópiát az elsődleges adatközpontba, és állítsa vissza a rendelkezésre állási módot a normál működési beállításokra. Az alábbi táblázat a jelen dokumentumban ismertetett architektúra szokásos működési beállításait mutatja be:

| Hely | Kiszolgálópéldány | Szerepkör | Elérhetőségi mód | Feladatátvételi mód
| ----- | ----- | ----- | ----- | -----
| Elsődleges adatközpont | SQL-1 | Elsődleges | Szinkron | Automatikus
| Elsődleges adatközpont | SQL-2 | Másodlagos | Szinkron | Automatikus
| Másodlagos vagy távoli adatközpont | SQL-3 | Másodlagos | Aszinkron | Kézi


### <a name="more-information-about-planned-and-forced-manual-failover"></a>További információ a tervezett és kényszerített manuális feladatátvételről

További információkért tekintse át a következők témaköröket:

- [Rendelkezésre állási csoport (SQL Server) tervezett manuális feladatátvételének végrehajtása](https://msdn.microsoft.com/library/hh231018.aspx)
- [Rendelkezésre állási csoport (SQL Server) kényszerített manuális feladatátvételének végrehajtása](https://msdn.microsoft.com/library/ff877957.aspx)

## <a name="additional-links"></a>További hivatkozások

* [Mindig a rendelkezésre állási csoportok](https://msdn.microsoft.com/library/hh510230.aspx)
* [Azure virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/windows/)
* [Azure terheléselosztók](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)
* [Azure rendelkezésre állási készletei](../manage-availability.md)
