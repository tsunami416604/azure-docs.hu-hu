---
title: VNN konfigurálása Azure Load Balancer HADR
description: Megtudhatja, hogyan konfigurálhat egy Azure Load balancert úgy, hogy átirányítsa a forgalmat a rendelkezésre állási csoport vagy a Feladatátvevőfürt SQL Server-példány (VNN) számára az Azure-beli virtuális gépeken a magas rendelkezésre álláshoz és a vész-helyreállításhoz (HADR).
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 10ff324e85082a4a5911e2c949744e7df1d9ad0b
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85965519"
---
# <a name="configure-vnn-with-azure-load-balancer-sql-server-on-azure-vms"></a>VNN konfigurálása Azure Load Balancer (SQL Server Azure-beli virtuális gépeken)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Az Azure Virtual Machines a fürtök terheléselosztást használnak egy olyan IP-cím tárolására, amely egyszerre egy fürtcsomópont számára szükséges. Ebben a megoldásban a terheléselosztó tárolja az Azure-ban fürtözött erőforrás által használt virtuális hálózati név (VNN) IP-címét. 

Ebből a cikkből megtudhatja, hogyan konfigurálhat terheléselosztó-t a Azure Load Balancer szolgáltatás használatával. A terheléselosztó átirányítja a forgalmat a [rendelkezésre állási csoport (AG) figyelőre](availability-group-overview.md) vagy a [feladatátvevő fürt példányaira (FCIs)](failover-cluster-instance-overview.md) az Azure-beli virtuális gépeken futó SQL Serverekkel a magas rendelkezésre állás és a vész-helyreállítás (HADR) érdekében. 



## <a name="prerequisites"></a>Előfeltételek

A cikk lépéseinek elvégzése előtt a következőket kell tennie:

- Úgy döntött, hogy Azure Load Balancer a megfelelő [kapcsolódási lehetőség a HADR-megoldáshoz](hadr-cluster-best-practices.md#connectivity).
- Konfigurálta a [rendelkezésre állási csoport figyelőjét](availability-group-overview.md) vagy a [feladatátvevő fürtök példányait](failover-cluster-instance-overview.md). 
- Telepítette a [PowerShell](/powershell/azure/install-az-ps?view=azps-4.2.0)legújabb verzióját. 


## <a name="create-load-balancer"></a>Terheléselosztó létrehozása

A terheléselosztó létrehozásához használja a [Azure Portal](https://portal.azure.com) :

1. A Azure Portal lépjen a virtuális gépeket tartalmazó erőforráscsoporthoz.

1. Válassza a **Hozzáadás** elemet. **Load Balancer**keresése az Azure Marketplace-en. Válassza a **Load Balancer**lehetőséget.

1. Válassza a **Létrehozás** lehetőséget.

1. Állítsa be a terheléselosztó értékét a következő értékek használatával:

   - **Előfizetés**: az Azure-előfizetése.
   - **Erőforráscsoport**: a virtuális gépeket tartalmazó erőforráscsoport.
   - **Name (név**): a terheléselosztó azonosítására szolgáló név.
   - **Régió**: az Azure-beli hely, amely a virtuális gépeket tartalmazza.
   - **Típus**: nyilvános vagy magánjellegű. A magánhálózati terheléselosztó a virtuális hálózaton belülről érhető el. A legtöbb Azure-alkalmazás saját Load balancert használhat. Ha az alkalmazásnak közvetlenül az interneten keresztül kell SQL Servera, használjon nyilvános Load balancert.
   - **SKU**: standard.
   - **Virtuális hálózat**: ugyanaz a hálózat, mint a virtuális gépek.
   - **IP-cím hozzárendelése**: statikus. 
   - **Magánhálózati IP-cím**: a fürtözött hálózati erőforráshoz rendelt IP-cím.

   A következő képen a **Load Balancer** felhasználói felületének létrehozása látható:

   ![A terheléselosztó beállítása](./media/failover-cluster-instance-premium-file-share-manually-configure/30-load-balancer-create.png)
   

## <a name="configure-backend-pool"></a>Háttérbeli készlet konfigurálása

1. Térjen vissza az Azure-erőforráscsoporthoz, amely tartalmazza a virtuális gépeket, és keresse meg az új Load balancert. Előfordulhat, hogy frissítenie kell a nézetet az erőforráscsoporthoz. Válassza ki a Load balancert.

1. Válassza ki a **háttérbeli készletek**elemet, majd kattintson a **Hozzáadás**gombra.

1. Társítsa a háttér-készletet a virtuális gépeket tartalmazó rendelkezésre állási csoporttal.

1. A **célként megadott hálózati IP-konfigurációk**területen válassza a **virtuális gép** lehetőséget, és válassza ki azokat a virtuális gépeket, amelyek fürtcsomópontokként fognak részt venni. Ügyeljen arra, hogy minden olyan virtuális gépet tartalmazzon, amely a (z) vagy a rendelkezésre állási csoportot fogja tárolni.

1. A háttér-készlet létrehozásához kattintson **az OK gombra** .

## <a name="configure-health-probe"></a>Állapot-mintavétel konfigurálása

1. A terheléselosztó ablaktáblán válassza az **állapot**-mintavételek elemet.

1. Válassza a **Hozzáadás** elemet.

1. Az állapot-mintavétel **hozzáadása** panelen állítsa be <span id="probe"> </span> a következő állapot-mintavételi paramétereket:

   - **Name (név**): az állapot mintavételének neve.
   - **Protokoll**: TCP.
   - **Port**: a [virtuális gép előkészítésekor](failover-cluster-instance-prepare-vm.md#uninstall-sql-server-1)a tűzfalban létrehozott port az állapot-mintavételhez. Ebben a cikkben a példa a TCP-portot használja `59999` .
   - **Intervallum**: 5 másodperc.
   - Nem megfelelő **állapot küszöbértéke**: 2 egymást követő hiba.

1. Válassza az **OK** lehetőséget.

## <a name="set-load-balancing-rules"></a>Terheléselosztási szabályok beállítása

1. A terheléselosztó panelen válassza a **terheléselosztási szabályok**elemet.

1. Válassza a **Hozzáadás** elemet.

1. Állítsa be a terheléselosztási szabály paramétereit:

   - **Name (név**): a terheléselosztási szabályok neve.
   - Előtérbeli **IP-cím**: az SQL Server vagy az AG-figyelő fürtözött hálózati ERŐFORRÁSÁNAK IP-címe.
   - **Port**: a SQL Server TCP-port. Az alapértelmezett példány portja 1433.
   - **Háttér-port**: ugyanaz a port, mint a **port** értéke, ha engedélyezi a **lebegőpontos IP-címet (a közvetlen kiszolgáló visszaadása)**.
   - **Háttér-készlet**: a korábban konfigurált háttér-készlet neve.
   - **Állapot**mintavétele: a korábban konfigurált állapot-mintavétel.
   - **Munkamenet-megőrzés**: nincs.
   - **Üresjárati időkorlát (perc)**: 4.
   - **Lebegőpontos IP-cím (közvetlen kiszolgáló visszaadása)**: engedélyezve.

1. Válassza az **OK** lehetőséget.

## <a name="configure-cluster-probe"></a>A fürt mintavételének konfigurálása

Állítsa be a fürt mintavételi portjának paraméterét a PowerShellben.

A fürt mintavételi portjának paraméterének beállításához módosítsa a következő parancsfájl változóit a környezetében lévő értékekkel. Távolítsa el a szögletes zárójeleket ( `<` és `>` ) a parancsfájlból.

```powershell
$ClusterNetworkName = "<Cluster Network Name>"
$IPResourceName = "<SQL Server FCI / AG Listener IP Address Resource Name>" 
$ILBIP = "<n.n.n.n>" 
[int]$ProbePort = <nnnnn>

Import-Module FailoverClusters

Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
```

A következő táblázat ismerteti a frissíteni kívánt értékeket:


|**Érték**|**Leírás**|
|---------|---------|
|`Cluster Network Name`| A hálózat Windows Server feladatátvevő fürtjének neve. **Feladatátvevőfürt-kezelő**  >  **hálózatok**területen kattintson a jobb gombbal a hálózatra, és válassza a **Tulajdonságok**lehetőséget. A helyes érték az **általános** lap **név** területén található.|
|`SQL Server FCI/AG listener IP Address Resource Name`|Az SQL Server a vagy AG figyelő IP-címéhez tartozó erőforrás neve. **Feladatátvevőfürt-kezelő**  >  **szerepkörökben**, a **kiszolgáló neve**alatt, a SQL Server-es verzió szerepkör alatt kattintson a jobb gombbal az IP-cím erőforrásra, és válassza a **Tulajdonságok**lehetőséget. A helyes érték az **általános** lap **név** területén található.|
|`ILBIP`|A belső terheléselosztó (ILB) IP-címe. Ez a címe a Azure Portal a ILB frontend-címeként van konfigurálva. Ez az SQL Server-es IP-cím is. **Feladatátvevőfürt-kezelő** megtalálhatja ugyanazon tulajdonságlapon, ahol a található `<SQL Server FCI/AG listener IP Address Resource Name>` .|
|`nnnnn`|A terheléselosztó állapotának mintavételében konfigurált mintavételi port. A fel nem használt TCP-portok érvényesek.|
|SubnetMask| A fürt paraméteréhez tartozó alhálózati maszk. A TCP IP-szórási címnek kell lennie: `255.255.255.255` .| 


A fürt mintavételének beállítása után a PowerShellben láthatja a fürt összes paraméterét. Futtassa a következő parancsfájlt:

```powershell
Get-ClusterResource $IPResourceName | Get-ClusterParameter
```


## <a name="test-failover"></a>Feladatátvétel tesztelése


A fürtözött erőforrás feladatátvételi tesztje a fürt működésének ellenőrzéséhez. 

# <a name="failover-cluster-instance"></a>[Feladatátvevőfürt-példány](#tab/fci)

Hajtsa végre a következő lépéseket:

1. Kapcsolódjon az egyik SQL Server fürtcsomópontok valamelyikéhez RDP használatával.
1. Nyissa meg **Feladatátvevőfürt-kezelő**. Válassza a **szerepkörök**lehetőséget. Figyelje meg, hogy melyik csomópont tulajdonosa a SQL Server.
1. Kattintson a jobb gombbal a SQL Server. 
1. Válassza az **Áthelyezés**, majd a **lehető legjobb csomópont**lehetőséget.

**Feladatátvevőfürt-kezelő** megjeleníti a szerepkört, és az erőforrásai offline állapotba kerülnek. Ezután az erőforrások átkerülnek a másik csomópontba, majd ismét elérhetővé válnak.



# <a name="ag-listener"></a>[AG-figyelő](#tab/ag)

Hajtsa végre a következő lépéseket:

1. Nyissa meg [SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms) , és kapcsolódjon a rendelkezésre állási csoport figyelőhöz. 

1. Bontsa ki az **Always On rendelkezésre állási csoportot** **Object Explorer**. 
1. Kattintson a jobb gombbal a rendelkezésre állási csoportra, majd válassza a **feladatátvétel**lehetőséget. 
1. Kövesse a varázsló utasításait a rendelkezésre állási csoport másodlagos replikára történő feladatátvételéhez. 

A feladatátvétel akkor sikeres, ha a replikák átváltják a szerepköröket, és szinkronizálva vannak. 

---

## <a name="test-connectivity"></a>Kapcsolat tesztelése

A kapcsolat teszteléséhez jelentkezzen be egy másik virtuális gépre ugyanabban a virtuális hálózaton. Nyissa meg **SQL Server Management Studiot** , és kapcsolódjon a SQL Server vagy a rendelkezésre állási csoport figyelőhöz.

>[!NOTE]
>Ha szükséges, [letöltheti SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).



## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure SQL Server HADR szolgáltatásairól, tekintse meg a [rendelkezésre állási csoportok](availability-group-overview.md) és a [feladatátvevő fürt példánya](failover-cluster-instance-overview.md)című témakört. Megtudhatja, [Hogyan](hadr-cluster-best-practices.md) konfigurálhatja a környezetet a magas rendelkezésre álláshoz és a vész-helyreállításhoz. 



