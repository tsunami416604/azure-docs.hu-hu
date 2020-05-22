---
title: Felügyelt példányok kapcsolatainak típusai
description: Tudnivalók a felügyelt példányok kapcsolatainak típusairól
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: a7df26590ec1514edcab24a1af9d85048b332d92
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773643"
---
# <a name="azure-sql-database-managed-instance-connection-types"></a>Azure SQL Database felügyelt példányok kapcsolatainak típusai

Ez a cikk azt ismerteti, hogyan csatlakozhatnak az ügyfelek Azure SQL Database felügyelt példányhoz a kapcsolat típusától függően. A kapcsolati típusok módosítására szolgáló parancsfájl-mintákat alább találja, valamint az alapértelmezett kapcsolati beállítások módosításával kapcsolatos szempontokat.

## <a name="connection-types"></a>Kapcsolattípusok

Azure SQL Database felügyelt példány a következő két kapcsolattípus használatát támogatja:

- **Átirányítás (ajánlott):** Az ügyfelek közvetlen kapcsolatot létesít az adatbázist futtató csomóponttal. Az átirányítás használatával történő csatlakozás engedélyezéséhez meg kell nyitnia a tűzfalakat és a hálózati biztonsági csoportokat (NSG), hogy engedélyezze a hozzáférést a 1433-es és 11000-11999-es portokon. A csomagok közvetlenül az adatbázisba kerülnek, ezért a proxyn keresztüli átirányítás a késés és az átviteli teljesítmény terén is javul.
- **Proxy (alapértelmezett):** Ebben a módban minden kapcsolat egy proxy átjáró összetevőt használ. A kapcsolat engedélyezéséhez csak a 1433-es portot kell megnyitni a magánhálózati és a 3342-es porthoz a nyilvános kapcsolathoz. Ha ezt a módot választja, a számítási feladatok természetétől függően nagyobb késést és alacsonyabb átviteli sebességet eredményezhet. A legalacsonyabb késés és a legmagasabb átviteli sebesség érdekében javasoljuk, hogy a proxy kapcsolati házirendjét átirányítsa az átirányítás kapcsolati házirendre.

## <a name="redirect-connection-type"></a>A kapcsolattípus átirányítása

A kapcsolat átirányítása azt jelenti, hogy a TCP-munkamenet az SQL-motorhoz való létrehozása után az ügyfél-munkamenet a terheléselosztó virtuális fürtjének célként megadott virtuális IP-címét szerzi be. A következő csomagok közvetlenül a virtuális fürt csomópontjára áramlanak, és megkerülik az átjárót. A következő ábra szemlélteti ezt a forgalmat.

![átirányítás. png](media/sql-database-managed-instance-connection-types/redirect.png)

> [!IMPORTANT]
> Az átirányítási kapcsolattípus jelenleg csak privát végponton működik. A kapcsolat típusának beállításától függetlenül a nyilvános végponton keresztül érkező kapcsolatok egy proxyn keresztül történnek.

## <a name="proxy-connection-type"></a>Proxy kapcsolattípus

A proxy kapcsolat típusa azt jelenti, hogy a TCP-munkamenet az átjáróval és az azt követő összes további csomaggal lesz létrehozva. A következő ábra szemlélteti ezt a forgalmat.

![proxy. png](media/sql-database-managed-instance-connection-types/proxy.png)

## <a name="script-to-change-connection-type-settings-using-powershell"></a>A kapcsolattípus beállításainak a PowerShell használatával történő módosítására szolgáló parancsfájl

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő PowerShell-szkript bemutatja, hogyan módosíthatja a felügyelt példányok kapcsolódási típusát az átirányításhoz.

```powershell
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
# Get your SubscriptionId from the Get-AzSubscription command
Get-AzSubscription
# Use your SubscriptionId in place of {subscription-id} below
Select-AzSubscription -SubscriptionId {subscription-id}
# Replace {rg-name} with the resource group for your managed instance, and replace {mi-name} with the name of your managed instance
$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}
$mi = $mi | Set-AzSqlInstance -ProxyOverride "Redirect" -force
```

## <a name="next-steps"></a>További lépések

- [Adatbázis visszaállítása egy felügyelt példányra](sql-database-managed-instance-get-started-restore.md)
- Megtudhatja, hogyan [konfigurálhat nyilvános végpontot a felügyelt példányon](sql-database-managed-instance-public-endpoint-configure.md)
- A [felügyelt példány kapcsolati architektúrájának](sql-database-managed-instance-connectivity-architecture.md) megismerése