---
title: Kapcsolattípusok
titleSuffix: Azure SQL Managed Instance
description: Tudnivalók az Azure SQL felügyelt példányok kapcsolatainak típusairól
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e46c6d1c14d226522a1d534418b91076efeaaccf
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89070717"
---
# <a name="azure-sql-managed-instance-connection-types"></a>Felügyelt Azure SQL-példány kapcsolattípusai
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a cikk azt ismerteti, hogyan csatlakozhatnak az ügyfelek az Azure SQL felügyelt példányaihoz a kapcsolat típusától függően. A kapcsolati típusok módosítására szolgáló parancsfájl-mintákat alább találja, valamint az alapértelmezett kapcsolati beállítások módosításával kapcsolatos szempontokat.

## <a name="connection-types"></a>Kapcsolattípusok

Az Azure SQL felügyelt példánya a következő két kapcsolattípus használatát támogatja:

- **Átirányítás (ajánlott):** Az ügyfelek közvetlen kapcsolatot létesít az adatbázist futtató csomóponttal. Az átirányítás használatával történő csatlakozás engedélyezéséhez meg kell nyitnia a tűzfalakat és a hálózati biztonsági csoportokat (NSG), hogy engedélyezze a hozzáférést a 1433-es és 11000-11999-es portokon. A csomagok közvetlenül az adatbázisba kerülnek, ezért a proxyn keresztüli átirányítás a késés és az átviteli teljesítmény terén is javul.
- **Proxy (alapértelmezett):** Ebben a módban minden kapcsolat egy proxy átjáró összetevőt használ. A kapcsolat engedélyezéséhez csak a 1433-es portot kell megnyitni a magánhálózati és a 3342-es porthoz a nyilvános kapcsolathoz. Ha ezt a módot választja, a számítási feladatok természetétől függően nagyobb késést és alacsonyabb átviteli sebességet eredményezhet. A legalacsonyabb késés és a legmagasabb átviteli sebesség érdekében javasoljuk, hogy a proxy kapcsolati házirendjét átirányítsa az átirányítás kapcsolati házirendre.

## <a name="redirect-connection-type"></a>A kapcsolattípus átirányítása

Az átirányítás kapcsolat típusa beállításnál a TCP-munkamenet SQL-motorhoz való létrehozása után az ügyfél-munkamenet a terheléselosztó virtuális fürtjének célként megadott virtuális IP-címét szerzi be. A következő csomagok közvetlenül a virtuális fürt csomópontjára áramlanak, és megkerülik az átjárót. A következő ábra szemlélteti ezt a forgalmat.

![redirect.png](./media/connection-types-overview/redirect.png)

> [!IMPORTANT]
> Az átirányítási kapcsolattípus jelenleg csak privát végpont esetén működik. A kapcsolat típusának beállításától függetlenül a nyilvános végponton keresztül érkező kapcsolatok egy proxyn keresztül történnek.

## <a name="proxy-connection-type"></a>Proxy kapcsolattípus

A proxy kapcsolati típusában a TCP-munkamenet az átjáróval és az azt követő összes további csomaggal lesz létrehozva. A következő ábra szemlélteti ezt a forgalmat.

![proxy.png](./media/connection-types-overview/proxy.png)

## <a name="changing-connection-type"></a>Kapcsolattípus módosítása

- **A portál használata:** A kapcsolattípus a Azure Portal használatával történő módosításához nyissa meg a Virtual Network lapot, és a **kapcsolattípus** beállítással módosítsa a kapcsolattípus, és mentse a módosításokat.

- **A kapcsolattípus beállításainak a PowerShell használatával történő módosítására szolgáló parancsfájl:**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A következő PowerShell-szkript bemutatja, hogyan módosíthatja a felügyelt példányok kapcsolattípus-típusát `Redirect` .

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

## <a name="next-steps"></a>Következő lépések

- [Adatbázis visszaállítása SQL felügyelt példányra](restore-sample-database-quickstart.md)
- Megtudhatja, hogyan [konfigurálhat nyilvános végpontot az SQL felügyelt példányain](public-endpoint-configure.md)
- További információ az [SQL felügyelt példányának kapcsolati architektúráról](connectivity-architecture-overview.md)
