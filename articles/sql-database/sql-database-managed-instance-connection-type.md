---
title: Felügyelt példány kapcsolattípusai
description: További információ a felügyelt példánykapcsolat-típusokról
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: 46223d1701b930d93de7c49c1e216a41045dda16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73819458"
---
# <a name="azure-sql-database-managed-instance-connection-types"></a>Az Azure SQL Database felügyelt példányának kapcsolattípusai

Ez a cikk bemutatja, hogyan ügyfelek csatlakoznak az Azure SQL Database felügyelt példány a kapcsolat típusától függően. A kapcsolattípusok módosításához a parancsfájlmintákat az alábbiakban találjuk, valamint az alapértelmezett kapcsolódási beállítások módosításával kapcsolatos szempontokat.

## <a name="connection-types"></a>Kapcsolattípusok

Az Azure SQL Database felügyelt példánya a következő két kapcsolattípust támogatja:

- **Átirányítás (ajánlott):** Az ügyfelek közvetlenül az adatbázist üzemeltető csomóponthoz létesítenek kapcsolatot. Ha engedélyezni szeretné a kapcsolatot az átirányításhasználatával, meg kell nyitnia a tűzfalakat és a hálózati biztonsági csoportokat (NSG), hogy az 1433-as és az 11000-11999-es portokon is engedélyezze a hozzáférést. A csomagok közvetlenül az adatbázisba kerülnek, ezért a proxyn keresztüli átirányítás használatával a késés és az átviteli teljesítmény javul.
- **Proxy (alapértelmezett):** Ebben a módban minden kapcsolat proxyátjáró-összetevőt használ. A kapcsolat engedélyezéséhez csak a magánhálózatok 1433-as és a nyilvános kapcsolathoz szükséges 3342-es portot kell megnyitni. Ennek a módnak a kiválasztása nagyobb késést és alacsonyabb átviteli terhelést eredményezhet a munkaterhelés jellegétől függően. A legalacsonyabb késés és a legmagasabb átviteli képesség esetén erősen javasoljuk az átirányítási kapcsolatházirendet a proxykapcsolat-házirenden keresztül.

## <a name="redirect-connection-type"></a>Kapcsolattípus átirányítása

Az átirányítási kapcsolat típusa azt jelenti, hogy a TCP-munkamenet SQL-motorhoz való létrehozása után az ügyfélmunkamenet a terheléselosztótól szerzi be a virtuális fürtcsomópont célvirtuális IP-címét. Az ezt követő csomagok közvetlenül a virtuális fürtcsomópontra áramlanak, megkerülve az átjárót. Az alábbi ábra ezt a forgalmat mutatja be.

![átirányítás.png](media/sql-database-managed-instance-connection-types/redirect.png)

> [!IMPORTANT]
> Az átirányítási kapcsolat típusa jelenleg csak a magánvégpontokesetében működik. A kapcsolattípus beállításától függetlenül a nyilvános végponton keresztül érkező kapcsolatok proxyn keresztül érkeznek.

## <a name="proxy-connection-type"></a>Proxykapcsolat típusa

A proxykapcsolat típusa azt jelenti, hogy a TCP-munkamenet az átjáró használatával jön létre, és az összes további csomag átáramlik rajta. Az alábbi ábra ezt a forgalmat mutatja be.

![proxy.png](media/sql-database-managed-instance-connection-types/proxy.png)

## <a name="script-to-change-connection-type-settings-using-powershell"></a>Parancsfájl a kapcsolattípus-beállítások módosításához a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő PowerShell-parancsfájl bemutatja, hogyan módosíthatja a felügyelt példány kapcsolattípusát átirányításra.

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
- További információ a [nyilvános végpont okainak konfigurálásáról felügyelt példányon](sql-database-managed-instance-public-endpoint-configure.md)
- További információ a [felügyelt példányok kapcsolódási architektúrájáról](sql-database-managed-instance-connectivity-architecture.md)