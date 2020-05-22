---
title: Példány-készletek útmutatója (előzetes verzió)
description: Ez a cikk bemutatja, hogyan hozhatók létre és kezelhetők Azure SQL Database példány-készletek (előzetes verzió).
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 005ed634830190a947045964ff01d126853bdc64
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773041"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>A Azure SQL Database instance Pools (előzetes verzió) útmutatója

Ez a cikk a [példány-készletek](sql-database-instance-pools.md)létrehozásának és kezelésének részleteit ismerteti.

## <a name="instance-pool-operations"></a>Példányok készletének műveletei

A következő táblázat a példány-készletekhez kapcsolódó elérhető műveleteket, valamint azok rendelkezésre állását mutatja be a Azure Portal és a PowerShellben.

|Parancs|Azure Portal|PowerShell|
|:---|:---|:---|
|Példánykészlet létrehozása|Nem|Igen|
|Példány frissítése (korlátozott számú tulajdonság)|Nem |Igen |
|Példány-készlet használatának és tulajdonságainak megtekintése|Nem|Igen |
|Példány-készlet törlése|Nem|Igen|
|Felügyelt példány létrehozása a példány-készleten belül|Nem|Igen|
|Felügyelt példány erőforrás-használatának frissítése|Igen |Igen|
|Felügyelt példányok használatának és tulajdonságainak keresése|Igen|Igen|
|Felügyelt példány törlése a készletből|Igen|Igen|
|Adatbázis létrehozása a készletbe helyezett felügyelt példányban|Igen|Igen|
|Adatbázis törlése a felügyelt példányból|Igen|Igen|

Elérhető [PowerShell-parancsok](https://docs.microsoft.com/powershell/module/az.sql/)

|Parancsmag |Leírás |
|:---|:---|
|[Új – AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Létrehoz egy Azure SQL Database példány-készletet. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Az Azure SQL-példány készletével kapcsolatos információkat ad vissza. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Egy Azure SQL Database-példány tulajdonságainak beállítása. |
|[Remove-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Eltávolít egy Azure SQL Database példány-készletet. |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Az Azure SQL-példányok készletének használatáról ad vissza adatokat. |


A PowerShell használatához [telepítse a PowerShell Core legújabb verzióját](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell), és kövesse az utasításokat az [Azure PowerShell modul telepítéséhez](https://docs.microsoft.com/powershell/azure/install-az-ps).

A készleteken és az önálló példányokon belüli példányokkal kapcsolatos műveletekhez használja a szabványos [felügyelt példányok parancsait](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances), de a *példány neve* tulajdonságot fel kell tölteni, ha egy készlet egy példányához használja ezeket a parancsokat.

## <a name="how-to-deploy-managed-instances-into-pools"></a>Felügyelt példányok készletekbe telepítése

A példányok készletbe történő központi telepítésének folyamata a következő két lépésből áll:

1. A példányok egyszeri telepítése. Ez egy hosszan futó művelet, amelyben az időtartam ugyanaz, mint egy [üres alhálózaton létrehozott egyetlen példány](sql-database-managed-instance.md#managed-instance-management-operations)üzembe helyezése.

2. Ismétlődő példányok telepítése egy példány-készletben. A példány-készlet paraméterét explicit módon meg kell adni a művelet részeként. Ez egy viszonylag gyors művelet, amely általában akár 5 percet is igénybe vehet.

Nyilvános előzetes verzióban a két lépés csak a PowerShell és Resource Manager-sablonok használatával támogatott. A Azure Portal-élmény jelenleg nem érhető el.

A felügyelt példányok készletre való telepítése után a Azure Portal *segítségével módosíthatja* a tulajdonságait a díjszabási csomag lapon.


## <a name="create-an-instance-pool"></a>Példány-készlet létrehozása

Példány készletének létrehozása:

1. [Hozzon létre egy alhálózattal rendelkező virtuális hálózatot](#create-a-virtual-network-with-a-subnet).
2. [Hozzon létre egy példány-készletet](#create-an-instance-pool).


### <a name="create-a-virtual-network-with-a-subnet"></a>Virtuális hálózat létrehozása alhálózattal 

Ha több példányra vonatkozó készletet szeretne elhelyezni ugyanazon a virtuális hálózaton belül, tekintse meg a következő cikkeket:

- [Azure SQL Database felügyelt példányhoz tartozó VNet-alhálózat méretének meghatározása](sql-database-managed-instance-determine-size-vnet-subnet.md).
- Hozzon létre új virtuális hálózatot és alhálózatot a [Azure Portal sablonnal](sql-database-managed-instance-create-vnet-subnet.md) , vagy kövesse a [meglévő virtuális hálózat előkészítésének](sql-database-managed-instance-configure-vnet-subnet.md)utasításait.
 


### <a name="create-an-instance-pool"></a>Példány-készlet létrehozása 

Az előző lépések elvégzése után készen áll egy példány-készlet létrehozására.

A következő korlátozások vonatkoznak a példány-készletekre:

- Nyilvános előzetes verzióban csak általános célú és Gen5 érhető el.
- A készlet neve csak kisbetűket, számokat és kötőjelet tartalmazhat, és nem kezdődhet kötőjeltel.
- Ha a AHB (Azure Hybrid Benefit) szeretné használni, azt a rendszer a példány készlet szintjén alkalmazza. A licenc típusát a készlet létrehozása során állíthatja be, vagy a létrehozás után bármikor frissítheti.

> [!IMPORTANT]
> A példányok üzembe helyezése hosszú ideig futó művelet, amely körülbelül 4,5 órát vesz igénybe.

Hálózati paraméterek beolvasása:

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

Példány készletének létrehozása:

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
> Mivel egy példány-készlet üzembe helyezése hosszú ideig futó művelet, meg kell várnia, amíg be nem fejeződik a jelen cikkben ismertetett lépések bármelyikének futtatása.

## <a name="create-a-managed-instance-inside-the-pool"></a>Felügyelt példány létrehozása a készleten belül 

A példány-készlet sikeres üzembe helyezése után itt az ideje, hogy hozzon létre egy példányt.

Felügyelt példány létrehozásához hajtsa végre a következő parancsot:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

Egy példány egy készleten belüli üzembe helyezése néhány percet vesz igénybe. Az első példány létrehozása után további példányok is létrehozhatók:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>Adatbázis létrehozása egy példányon belül 

Egy készleten belüli felügyelt példányban lévő adatbázisok létrehozásához és kezeléséhez használja az Egypéldányos parancsokat.

Adatbázis létrehozása felügyelt példányon belül:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>Példány-készlet használatának beolvasása 
 
A készleten belüli példányok listájának lekérése:

```powershell
$instancePool | Get-AzSqlInstance
```


A készlet erőforrásai használatának beolvasása:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


A készlet és a benne található példányok részletes használatának áttekintése:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

Példányban lévő adatbázisok listázása:

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> A készletben legfeljebb 100 adatbázis lehet (nem példány).


## <a name="scale-a-managed-instance-inside-a-pool"></a>Felügyelt példány skálázása egy készleten belül 


A felügyelt példány adatbázisokkal való feltöltése után a tárolóra vagy a teljesítményre vonatkozó korlátozásokat is elérheti. Ebben az esetben, ha nem lépték túl a készlet használatát, méretezheti a példányt.
A felügyelt példányok készleten belüli skálázása egy művelet, amely néhány percet vesz igénybe. A skálázás előfeltétele, hogy a virtuális mag és a tárterület elérhető legyen a példányok készletének szintjén.

A virtuális mag és a tárterület méretének frissítése:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Csak a tárolási méret frissítése:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>Kapcsolódás felügyelt példányhoz egy készleten belül

A készletben lévő felügyelt példányokhoz való kapcsolódáshoz a következő két lépés szükséges:

1. [Engedélyezze a példány nyilvános végpontját](#enable-the-public-endpoint-for-the-instance).
2. [Adjon hozzá egy bejövő szabályt a hálózati biztonsági csoporthoz (NSG)](#add-an-inbound-rule-to-the-network-security-group).

Mindkét lépés befejezése után csatlakozhat a példányhoz a példány létrehozásakor megadott nyilvános végponti címen, porton és hitelesítő adatokon keresztül. 

### <a name="enable-the-public-endpoint-for-the-instance"></a>A példány nyilvános végpontjának engedélyezése

Egy példány nyilvános végpontjának engedélyezése a Azure Portalon vagy a következő PowerShell-parancs használatával végezhető el:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Ez a paraméter a példány létrehozásakor is beállítható.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Bejövő szabály hozzáadása a hálózati biztonsági csoporthoz 

Ez a lépés a Azure Portalon vagy a PowerShell-parancsok használatával végezhető el, és bármikor elvégezhető az alhálózat felügyelt példányra való előkészítése után.

Részletekért lásd: [nyilvános végponti forgalom engedélyezése a hálózati biztonsági csoportban](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>Egy meglévő példány áthelyezése egy példány-készleten belül 
 
A készleten belüli és kívüli példányok áthelyezése a nyilvános előzetes verzióra vonatkozó korlátozások egyike. Egy olyan megkerülő megoldás, amely a készleten kívüli példányok adatbázisainak adott időpontra történő visszaállítására támaszkodik egy már egy készletben lévő példányra. 

Mindkét példánynak ugyanabban az előfizetésben és régióban kell lennie. A régiók közötti és az előfizetések közötti visszaállítás jelenleg nem támogatott.

Ez a folyamat leállási idővel rendelkezik.

Meglévő adatbázisok áthelyezése:

1. A munkaterhelések felfüggesztése az áttelepíteni kívánt felügyelt példányon.
2. Parancsfájlok létrehozásával rendszeradatbázisokat hozhat létre, és végrehajthatja azokat a példányon belül.
3. Végezze el az egyes adatbázisok adott időponthoz való visszaállítását a készletben lévő példányok között.

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

4. Mutasson az alkalmazásra az új példányra, és folytassa a számítási feladatokat.

Ha több adatbázis van, ismételje meg a folyamatot az egyes adatbázisokhoz.


## <a name="next-steps"></a>További lépések

- A szolgáltatások és az összehasonlítások listájáért lásd: [általános SQL-szolgáltatások](sql-database-features.md).
- A VNet-konfigurációval kapcsolatos további információkért lásd: [felügyelt példányok VNet konfigurációja](sql-database-managed-instance-connectivity-architecture.md).
- Egy felügyelt példányt létrehozó gyors útmutató, valamint egy adatbázis biztonságimásolat-fájlból való visszaállítása: [felügyelt példány létrehozása](sql-database-managed-instance-get-started.md).
- A Azure Database Migration Service (DMS) áttelepítésre való használatát ismertető oktatóanyagért lásd: a [felügyelt példányok áttelepítése a DMS használatával](../dms/tutorial-sql-server-to-managed-instance.md).
- A felügyelt példányok adatbázisának teljesítményének speciális figyelése a beépített hibaelhárítási intelligenciával: [Azure SQL Database figyelése Azure SQL Analytics használatával](../azure-monitor/insights/azure-sql.md).
- A díjszabással kapcsolatos információkért lásd: [SQL Database felügyelt példány díjszabása](https://azure.microsoft.com/pricing/details/sql-database/managed/).
