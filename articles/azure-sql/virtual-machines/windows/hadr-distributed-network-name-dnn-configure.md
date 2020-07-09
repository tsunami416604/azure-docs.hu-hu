---
title: Elosztott hálózat nevének konfigurálása (DNN)
description: Megtudhatja, hogyan konfigurálhat egy elosztott hálózati nevet (DNN) az Azure-beli virtuális gépen futó feladatátvevő fürtön lévő SQL Server forgalmának átirányításához.
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
ms.openlocfilehash: ae9b6bf41d90b0a9111414302b2eafea3c8332d3
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85965524"
---
# <a name="configure-a-distributed-network-name-for-an-fci"></a>Elosztott hálózatnév konfigurálása a következőhöz: 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Az Azure Virtual Machines az elosztott hálózat neve (DNN) használatával irányítja át a forgalmat a megfelelő fürtözött erőforrásra. A szolgáltatás megkönnyíti a virtuális hálózat nevével (VNN) való kapcsolódást a SQL Server feladatátvevő fürt példányához (az Azure Load Balancer nélkül). Ez a funkció jelenleg előzetes verzióban érhető el, és csak SQL Server 2019 CU2 és újabb, valamint a Windows Server 2016-es és újabb verzióiban érhető el. 

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy DNN úgy, hogy átirányítsa a forgalmat a FCIs SQL Server Azure-beli virtuális gépeken a magas rendelkezésre állás és a vész-helyreállítási (HADR) 

## <a name="prerequisites"></a>Előfeltételek

A cikk lépéseinek elvégzése előtt a következőket kell tennie:

- Úgy döntött, hogy az elosztott hálózat neve a megfelelő [kapcsolódási lehetőség a HADR-megoldáshoz](hadr-cluster-best-practices.md#connectivity).
- Konfigurálta a [feladatátvevő fürt példányait](failover-cluster-instance-overview.md). 
- Telepítette a [PowerShell](/powershell/azure/install-az-ps)legújabb verzióját. 

## <a name="create-dnn-resource"></a>DNN-erőforrás létrehozása 

A DNN erőforrás ugyanabban a fürtben jön létre, mint a SQL Server-es verzió. A PowerShell használatával hozza létre a DNN-erőforrást a (z)-os fürtön belül. 

A következő PowerShell-parancs egy DNN-erőforrást hoz létre a SQL Server és a (z) egy erőforrás nevével `<dnnResourceName>` . Az erőforrás neve az erőforrás egyedi azonosítására szolgál. Használja az egyiket, amely logikus, és egyedi a fürtben. Az erőforrástípus csak a következő lehet: `Distributed Network Name` . 

Az `-Group` értéknek meg kell egyeznie annak a fürtnek a nevével, amely ahhoz a SQL Server, amelybe az elosztott hálózat nevét hozzá kívánja adni. Az alapértelmezett példány esetében a jellemző formátum a következő: `SQL Server (MSSQLSERVER)` . 


```powershell
Add-ClusterResource -Name <dnnResourceName> `
-ResourceType "Distributed Network Name" -Group "<WSFC role of SQL server instance>"
```

Ha például a DNN-erőforrást az `dnn-demo` alapértelmezett SQL Server a következő PowerShell-paranccsal szeretné létrehozni, használja az alábbi PowerShell-parancsot:

```powershell
Add-ClusterResource -Name dnn-demo `
-ResourceType "Distributed Network Name" -Group "SQL Server (MSSQLSERVER)"

```

## <a name="set-cluster-dnn-dns-name"></a>Fürt DNN DNS-nevének megadása

Állítsa be a DNN-erőforrás DNS-nevét a fürtben. A fürt ezután ezt az értéket használja arra, hogy átirányítsa a forgalmat a SQL Server-t jelenleg üzemeltető csomópontra. 
 
Az ügyfelek a DNS-név használatával csatlakoznak a SQL Server. Egyedi értéket is kiválaszthat. Ha már van meglévő, és nem szeretné frissíteni az ügyfél-kapcsolódási karakterláncokat, beállíthatja, hogy a DNN az ügyfelek által már használt aktuális VNN használja. Ehhez [át kell neveznie a VNN](#rename-the-vnn) , mielőtt BEÁLLÍTJA a DNN a DNS-ben.


Ezzel a paranccsal állíthatja be a DNN DNS-nevét: 

```powershell
Get-ClusterResource -Name <dnnResourceName> | `
Set-ClusterParameter -Name DnsName -Value <DNSName>
```

Az az `DNSName` érték, amellyel az ügyfelek csatlakoznak a SQL Server. Ahhoz például, hogy az ügyfelek csatlakozzanak a `FCIDNN` szolgáltatáshoz, használja a következő PowerShell-parancsot:

```powershell
Get-ClusterResource -Name dnn-demo | `
Set-ClusterParameter -Name DnsName -Value FCIDNN
```

Az ügyfelek mostantól `FCIDNN` a kapcsolati sztringbe lépnek, amikor csatlakoznak a SQL Server-es verzióhoz. 

### <a name="rename-the-vnn"></a>VNN átnevezése 

Ha meglévő virtuális hálózata van, és azt szeretné, hogy az ügyfelek továbbra is ezzel az értékkel csatlakozzanak a SQL Server-es adatkapcsolathoz, át kell neveznie a jelenlegi VNN egy helyőrző értékre. Az aktuális VNN átnevezve megadhatja a DNS-név értékét a DNN a VNN. 

Bizonyos korlátozások vonatkoznak a VNN átnevezésére. További információkért lásd: [a-os átnevezése](/sql/sql-server/failover-clusters/install/rename-a-sql-server-failover-cluster-instance).

Ha a jelenlegi VNN használata nem szükséges a vállalata számára, ugorja át ezt a szakaszt. Miután átnevezte a VNN, [állítsa be a fürt DNN DNS-nevét](#set-cluster-dnn-dns-name). 

   
## <a name="set-dnn-resource-online"></a>DNN-erőforrás online állapotának beállítása

Miután a DNN-erőforrás megfelelő névvel rendelkezik, és beállította a DNS-név értékét a fürtben, a PowerShell használatával állítsa be a DNN-erőforrást online állapotba a fürtben: 

```powershell
Start-ClusterResource -Name <dnnResourceName>
```

A DNN-erőforrás elindításához például `dnn-demo` használja a következő PowerShell-parancsot: 

```powershell
Start-ClusterResource -Name dnn-demo
```

## <a name="configure-possible-owners"></a>Lehetséges tulajdonosok konfigurálása

Alapértelmezés szerint a fürt a fürt összes csomópontja számára a DNN DNS-nevet köti. Azonban a fürt azon csomópontjait, amelyek nem részei a (SQL Server) verziónak, ki kell zárni a lehetséges tulajdonosok DNN listájából. 

A lehetséges tulajdonosok frissítéséhez kövesse az alábbi lépéseket:

1. Lépjen a DNN-erőforráshoz Feladatátvevőfürt-kezelő. 
1. Kattintson a jobb gombbal a DNN-erőforrásra, és válassza a **Tulajdonságok**lehetőséget. 
   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/fci-dnn-properties.png" alt-text="A DNN erőforrás helyi menüje, a tulajdonságok paranccsal kiemelve.":::
1. Törölje a jelet a feladatátvevő fürt példányában nem részt vevő csomópontok jelölőnégyzetéből. A DNN-erőforrás lehetséges tulajdonosainak listáját meg kell egyeznie a SQL Server példány-erőforrás lehetséges tulajdonosainak listájával. Tegyük fel például, hogy a data3 nem vesz részt a (z)-es verzióban, az alábbi képen egy példa a DNN-erőforrás lehetséges tulajdonosainak listájáról való data3 eltávolítására: 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/clear-check-for-nodes-not-in-fci.png" alt-text="Törölje a jelölőnégyzet jelölését azon csomópontok mellett, amelyek nem vesznek részt a DNN-erőforrás lehetséges tulajdonosainak":::

1. A beállítások mentéséhez kattintson **az OK gombra** . 


## <a name="restart-sql-server-instance"></a>SQL Server példány újraindítása 

A SQL Server példány újraindításához használja a Feladatátvevőfürt-kezelő. Kövesse az alábbi lépéseket:

1. Nyissa meg Feladatátvevőfürt-kezelő SQL Server erőforrását.
1. Kattintson a jobb gombbal a SQL Server erőforrásra, és kapcsolja offline állapotba. 
1. Miután az összes kapcsolódó erőforrás offline állapotba került, kattintson a jobb gombbal a SQL Server erőforrásra, majd újra online állapotba. 

## <a name="update-connection-string"></a>A kapcsolatok karakterláncának frissítése

A feladatátvétel gyors elérésének biztosítása érdekében vegye fel `MultiSubnetFailover=True` a kapcsolatot a kapcsolati karakterláncba, ha az SQL-ügyfél verziója a 4.6.1-nál korábbi. 

Ha a DNN nem az eredeti VNN használja, akkor a SQL Server-os-hez csatlakozó SQL-ügyfeleknek frissíteniük kell a kapcsolati karakterláncot a DNN DNS-nevére. A követelmény elkerüléséhez frissítse a DNS-név értéket a VNN nevére. [A meglévő VNN](#rename-the-vnn) azonban először a helyőrzőre kell cserélni. 

## <a name="test-failover"></a>Feladatátvétel tesztelése

A fürtözött erőforrás feladatátvételi tesztje a fürt működésének ellenőrzéséhez. 

A feladatátvétel teszteléséhez kövesse az alábbi lépéseket: 

1. Kapcsolódjon az egyik SQL Server fürtcsomópontok valamelyikéhez RDP használatával.
1. Nyissa meg **Feladatátvevőfürt-kezelő**. Válassza a **szerepkörök**lehetőséget. Figyelje meg, hogy melyik csomópont tulajdonosa a SQL Server.
1. Kattintson a jobb gombbal a SQL Server. 
1. Válassza az **Áthelyezés**, majd a **lehető legjobb csomópont**lehetőséget.

**Feladatátvevőfürt-kezelő** megjeleníti a szerepkört, és az erőforrásai offline állapotba kerülnek. Ezután az erőforrások átkerülnek a másik csomópontba, majd ismét elérhetővé válnak.

## <a name="test-connectivity"></a>Kapcsolat tesztelése

A kapcsolat teszteléséhez jelentkezzen be egy másik virtuális gépre ugyanabban a virtuális hálózaton. Nyissa meg **SQL Server Management Studio** , és kapcsolódjon a SQL Server a DNN DNS-név használatával.

Ha szükséges, [letöltheti SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="limitations"></a>Korlátozások

- Jelenleg a DNN csak SQL Server 2019 CU2 és újabb verziókban támogatott a Windows Server 2016 rendszeren. 
- A DNN jelenleg csak az Azure-beli virtuális gépeken SQL Server rendszerű feladatátvevő fürtök esetében támogatott. Használja a virtuális hálózat nevét Azure Load Balancer a rendelkezésre állási csoport figyelőkhöz.
- További szempontokat is figyelembe kell venni, ha más SQL Server-funkciókkal dolgozik, és egy DNN-vel rendelkező egy-egy-egy-egy-egy További információ: [DNN együttműködési képesség](failover-cluster-instance-dnn-interoperability.md). 

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure SQL Server HADR szolgáltatásairól, tekintse meg a [rendelkezésre állási csoportok](availability-group-overview.md) és a [feladatátvevő fürt példánya](failover-cluster-instance-overview.md)című témakört. Megtudhatja, [Hogyan](hadr-cluster-best-practices.md) konfigurálhatja a környezetet a magas rendelkezésre álláshoz és a vész-helyreállításhoz. 

A DNN és a-vel való használat esetén további konfigurációs követelmények is megadhatók bizonyos SQL Server-funkciókhoz. További információért lásd: [DNN együttműködési képesség](failover-cluster-instance-dnn-interoperability.md) . 

