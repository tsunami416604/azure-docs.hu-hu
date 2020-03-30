---
title: Példánykészletek útmutatója (előzetes verzió)
description: Ez a cikk ismerteti, hogyan hozhat létre és kezelhet Azure SQL Database-példánykészletek (előzetes verzió).
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 4a27165d929cc9bc5f18e372f7f108887e466e43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299362"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>Azure SQL Database-példánykészletek (előzetes verzió) útmutató

Ez a cikk a példánykészletek létrehozásának és [kezelésének](sql-database-instance-pools.md)részleteit ismerteti.

## <a name="instance-pool-operations"></a>Példánykészlet-műveletek

Az alábbi táblázat a példánykészletekkel és azok azure portalon és a PowerShellben való elérhetőségével kapcsolatos elérhető műveleteket mutatja be.

|Parancs|Azure portál|PowerShell|
|:---|:---|:---|
|Példánykészlet létrehozása|Nem|Igen|
|Példánykészlet frissítése (korlátozott számú tulajdonság)|Nem |Igen |
|A példánykészlet használatának és tulajdonságainak ellenőrzése|Nem|Igen |
|Példánykészlet törlése|Nem|Igen|
|Felügyelt példány létrehozása a példánykészleten belül|Nem|Igen|
|Felügyelt példány erőforrás-használatának frissítése|Igen |Igen|
|Felügyelt példányhasználatának és tulajdonságainak ellenőrzése|Igen|Igen|
|Felügyelt példány törlése a készletből|Igen|Igen|
|Adatbázis létrehozása a készletbe helyezett felügyelt példányban|Igen|Igen|
|Adatbázis törlése a felügyelt példányból|Igen|Igen|

Elérhető [PowerShell-parancsok](https://docs.microsoft.com/powershell/module/az.sql/)

|Parancsmag |Leírás |
|:---|:---|
|[Új-AzSqlInstancepool](/powershell/module/az.sql/new-azsqlinstancepool/) | Létrehoz egy Azure SQL Database-példánykészletet. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Az Azure SQL-példánykészletével kapcsolatos információkat adja vissza. |
|[Set-AzSqlInstancepool](/powershell/module/az.sql/set-azsqlinstancepool/) | Beállítja egy Azure SQL Database-példánykészlet tulajdonságait. |
|[Eltávolítás-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Eltávolítja az Azure SQL Database-példánykészletet. |
|[Get-AzSqlInstancepoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Az Azure SQL-példánykészlet használatának adatait adja vissza. |


A PowerShell használatához [telepítse a PowerShell Core legújabb verzióját,](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell)és kövesse [az utasításokat az Azure PowerShell-modul telepítéséhez.](https://docs.microsoft.com/powershell/azure/install-az-ps)

A példányokhoz kapcsolódó műveletek mind a készleteken belül, mind az egypéldányokon belül, használja a szabványos [felügyelt példányparancsokat,](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances)de a *példánykészlet névtulajdonságát* fel kell tölteni, amikor ezeket a parancsokat egy készlet egy példányához használja.

## <a name="how-to-deploy-managed-instances-into-pools"></a>Felügyelt példányok telepítése készletekbe

Egy példány készletbe való üzembe helyezésének folyamata a következő két lépésből áll:

1. Egyszeri példánykészlet telepítése. Ez egy hosszú ideig futó művelet, ahol az időtartam megegyezik egy [üres alhálózatban létrehozott egyetlen példány telepítésével.](sql-database-managed-instance.md#managed-instance-management-operations)

2. Ismétlődő példánytelepítés egy példánykészletben. A példánykészlet paraméterét a művelet részeként explicit módon meg kell adni. Ez egy viszonylag gyors művelet, amely általában akár 5 percet is igénybe vesz.

A nyilvános előzetes verzióban mindkét lépés csak a PowerShell és az Erőforrás-kezelő sablonok használatával támogatott. Az Azure Portal felület jelenleg nem érhető el.

Miután egy felügyelt példány telepítve van egy készletben, az Azure Portal *segítségével* módosíthatja a tulajdonságait a tarifacsomag-lapon.


## <a name="create-an-instance-pool"></a>Példánykészlet létrehozása

Példánykészlet létrehozása:

1. [Hozzon létre egy alhálózatot.](#create-a-virtual-network-with-a-subnet)
2. [Példánykészlet létrehozása](#create-an-instance-pool).


### <a name="create-a-virtual-network-with-a-subnet"></a>Virtuális hálózat létrehozása alhálózattal 

Ha több példánykészletet szeretne elhelyezni ugyanazon a virtuális hálózaton belül, olvassa el az alábbi cikkeket:

- [Határozza meg az Azure SQL Database felügyelt példányának virtuálishálózat-alhálózatának méretét.](sql-database-managed-instance-determine-size-vnet-subnet.md)
- Hozzon létre új virtuális hálózatot és alhálózatot az [Azure Portal sablon](sql-database-managed-instance-create-vnet-subnet.md) használatával, vagy kövesse a meglévő virtuális hálózat [előkészítésére](sql-database-managed-instance-configure-vnet-subnet.md)vonatkozó utasításokat.
 


### <a name="create-an-instance-pool"></a>Példánykészlet létrehozása 

Az előző lépések végrehajtása után készen áll egy példánykészlet létrehozására.

A következő korlátozások vonatkoznak a példánykészletekre:

- Csak az Általános cél és a Gen5 érhető el nyilvános előzetes verzióban.
- A készlet neve csak kisbetűs, számokat és kötőjelet tartalmazhat, és nem kezdheti elválasztással.
- Ha a b-t (Azure Hybrid Benefit) szeretné használni, a példánykészlet szintjén alkalmazza. A licenctípusát a készlet létrehozása során beállíthatja, vagy bármikor frissítheti a létrehozás után.

> [!IMPORTANT]
> Egy példánykészlet telepítése egy hosszú ideig futó művelet, amely körülbelül 4,5 órát vesz igénybe.

Hálózati paraméterek beszerezése:

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

Példánykészlet létrehozása:

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId $subnet.Id `
  -LicenseType "LicenseIncluded" `
  -VCore 80 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Mivel egy példánykészlet telepítése hosszú ideig futó művelet, meg kell várnia, amíg befejeződik, mielőtt futtatja a cikk következő lépéseit.

## <a name="create-a-managed-instance-inside-the-pool"></a>Felügyelt példány létrehozása a készleten belül 

A példánykészlet sikeres üzembe helyezése után itt az ideje, hogy hozzon létre egy példányt benne.

Felügyelt példány létrehozásához hajtsa végre a következő parancsot:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

Egy példány üzembe helyezése a készleten belül néhány percet vesz igénybe. Az első példány létrehozása után további példányok hozhatók létre:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>Adatbázis létrehozása példányon belül 

Adatbázisok létrehozásához és kezeléséhez egy készleten belüli felügyelt példányban használja az egypéldányos parancsokat.

Adatbázis létrehozása felügyelt példányon belül:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>Példánykészlet használatának beszereznie 
 
A készleten belüli példányok listájának leése:

```powershell
$instancePool | Get-AzSqlInstance
```


Készlet erőforrás-használatának leése:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


A készlet és a benne lévő példányok részletes használati áttekintésének betekintése:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

Az adatbázisok felsorolása egy példányban:

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> Készletenként legfeljebb 100 adatbázis lehet (nem példányonként).


## <a name="scale-a-managed-instance-inside-a-pool"></a>Felügyelt példány méretezése készleten belül 


Miután felnagyít egy felügyelt példányt az adatbázisokkal, előfordulhat, hogy a tárolási vagy teljesítményi példánykorlátokat. Ebben az esetben, ha a készlet használatát nem lépte túl, skálázhatja a példányt.
Egy felügyelt példány méretezése egy készleten belül olyan művelet, amely néhány percet vesz igénybe. A skálázás előfeltétele elérhető virtuális magok és a példánykészlet szintjén tároló.

A virtuális magok számának és a tárhely méretének frissítése:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Csak a tárhely méretének frissítése:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>Csatlakozás egy készleten belüli felügyelt példányhoz

A készlet ben lévő felügyelt példányhoz való csatlakozáshoz a következő két lépésre van szükség:

1. [Engedélyezze a példány nyilvános végpontja .](#enable-the-public-endpoint-for-the-instance)
2. [Vegyen fel egy bejövő szabályt a hálózati biztonsági csoportba (NSG).](#add-an-inbound-rule-to-the-network-security-group)

Miután mindkét lépés befejeződött, csatlakozhat a példányhoz egy nyilvános végpontcím, port és a példány létrehozása során megadott hitelesítő adatok használatával. 

### <a name="enable-the-public-endpoint-for-the-instance"></a>A nyilvános végpont engedélyezése a példányhoz

A nyilvános végpont engedélyezése egy példányhoz az Azure Portalon keresztül vagy a következő PowerShell-paranccsal végezhető el:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Ez a paraméter a példány létrehozása kor is beállítható.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Bejövő szabály hozzáadása a hálózati biztonsági csoporthoz 

Ez a lépés az Azure Portalon keresztül vagy a PowerShell-parancsok használatával végezhető el, és bármikor elvégezhető, miután az alhálózat elő van készítve a felügyelt példányhoz.

További információt a [Nyilvános végponti forgalom engedélyezése a hálózati biztonsági csoportban című témakörben talál.](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group)


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>Meglévő példány áthelyezése egy példánykészleten belül 
 
A példányok készletbe és készletből való áthelyezése a nyilvános előnézetegyik korlátozása. A használható megoldás az adatbázisok adott időpontban történő visszaállítására támaszkodik egy készleten kívüli példányból egy készletben már lévő példányra. 

Mindkét példánynak ugyanabban az előfizetésben és régióban kell lennie. A régiók közötti és az előfizetések közötti visszaállítás jelenleg nem támogatott.

Ennek a folyamatnak van egy állásideje.

Meglévő adatbázisok áthelyezése:

1. A számítási feladatok szüneteltetése azon a felügyelt példányon, amelyről áttelepíti az áttelepítést.
2. Parancsfájlok létrehozása rendszeradatbázisok létrehozásához és a példánykészleten belüli példányon való végrehajtásához.
3. Végezze el az egyes adatbázisok egy példányból a készletben lévő példányra történő időpont-idő visszaállítását.

    ```powershell
    $resourceGroupName = "my resource group name"
    $managedInstanceName = "my managed instance name"
    $databaseName = "my source database name"
    $pointInTime = "2019-08-21T08:51:39.3882806Z"
    $targetDatabase = "name of the new database that will be created"
    $targetResourceGroupName = "resource group of instance pool"
    $targetInstanceName = "pool instance name"
       
    Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
      -ResourceGroupName $resourceGroupName `
      -InstanceName $managedInstanceName `
      -Name $databaseName `
      -PointInTime $pointInTime `
      -TargetInstanceDatabaseName $targetDatabase `
      -TargetResourceGroupName $targetResourceGroupName `
      -TargetInstanceName $targetInstanceName
    ```

4. Irányítsa az alkalmazást az új példányra, és folytassa a számítási feladatokat.

Ha több adatbázis van, ismételje meg a folyamatot az egyes adatbázisokesetében.


## <a name="next-steps"></a>További lépések

- A szolgáltatások és az összehasonlító lista az SQL általános szolgáltatásai című témakörben [található.](sql-database-features.md)
- A virtuális hálózat konfigurációjáról további információt a [felügyelt példány virtuális hálózatának konfigurálása](sql-database-managed-instance-connectivity-architecture.md)című témakörben talál.
- Felügyelt példányt létrehozó és biztonsági másolatból adatbázist visszaállító rövidútmutatóról a [Felügyelt példány létrehozása](sql-database-managed-instance-get-started.md)című témakörben található.
- Az Azure Database Migration Service (DMS) áttelepítési szolgáltatását használó oktatóanyagról a [Felügyelt példányáttelepítés DMS használatával című](../dms/tutorial-sql-server-to-managed-instance.md)témakörben található.
- A felügyelt példányok adatbázis-teljesítményének speciális figyelése beépített hibaelhárítási intelligenciával az [Azure SQL Database monitorozása az Azure SQL Analytics használatával című](../azure-monitor/insights/azure-sql.md)témakörben található.
- A díjszabásról az [SQL Database által felügyelt példányok díjszabása](https://azure.microsoft.com/pricing/details/sql-database/managed/)című témakörben talál.
