---
title: "Azure CLI: SQL-adatbázis létrehozása | Microsoft Docs"
description: "Ismerje meg, hogyan hozhat létre SQL Database logikai kiszolgálót, kiszolgálószintű tűzfalszabályokat és adatbázisokat az Azure Portal használatával."
keywords: "oktatóanyag az SQL Database használatához, SQL-adatbázis létrehozása"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: cli
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: f1e07d232328c35a43497c5a0ed6661a4277423d
ms.lasthandoff: 03/17/2017

---

# <a name="create-a-single-azure-sql-database-using-the-azure-cli"></a>Önálló Azure SQL-adatbázis létrehozása az Azure CLI használatával

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez az útmutató azt ismerteti, hogyan helyezhet üzembe Azure SQL-adatbázist az Azure CLI használatával egy [Azure SQL Database logikai kiszolgáló](sql-database-features.md) [Azure-erőforráscsoportjában](../azure-resource-manager/resource-group-overview.md).

A kezdés előtt győződjön meg arról, hogy az Azure CLI telepítve van. További információért lásd az [Azure CLI telepítési útmutatóját](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe az [az login](/cli/azure/#login) paranccsal, és kövesse a képernyőn látható utasításokat.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/resource-group-overview.md) az [az group create](/cli/azure/group#create) paranccsal. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy `westeurope` nevű erőforráscsoportot a `myResourceGroup` helyen.

```azurecli
az group create --name myResourceGroup --location westeurope
```
## <a name="create-a-logical-server"></a>Hozzon létre egy logikai kiszolgálót

Hozzon létre egy [Azure SQL Database logikai kiszolgálót](sql-database-features.md) az [az sql server create](/cli/azure/sql/server#create) paranccsal. A logikai kiszolgálók adatbázisok egy csoportját tartalmazzák, amelyeket a rendszer egy csoportként kezel. A következő példában létrehozunk egy véletlenszerűen elnevezett kiszolgálót az erőforráscsoportban egy `ServerAdmin` nevű és `ChangeYourAdminPassword1` jelszavú rendszergazdai bejelentkezéssel. Igény szerint cserélje le ezeket az előre meghatározott értékeket.

```azurecli
servername=server-$RANDOM
az sql server create --name $servername --resource-group myResourceGroup --location westeurope \
    --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
```

## <a name="configure-a-server-firewall-rule"></a>Konfiguráljon egy kiszolgálói tűzfalszabályt

Hozzon létre egy [Azure SQL Database kiszolgálószintű tűzfalszabályt](sql-database-firewall-configure.md) az [az sql server firewall create](/cli/azure/sql/server/firewall#create) paranccsal. A kiszolgálószintű tűzfalszabályok lehetővé teszik, hogy külső alkalmazások, például az SQL Server Management Studio vagy az SQLCMD segédprogram az SQL Database szolgáltatás tűzfalán keresztül csatlakozzon egy SQL-adatbázishoz. A következő példában létrehozunk egy tűzfalszabályt egy előre meghatározott címtartományhoz, amely ebben a példában az IP-címek teljes lehetséges tartományát lefedi. Cserélje le ezeket az előre meghatározott értékeket a külső IP-címére vagy IP-címtartományára. 

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
    -n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="create-a-database-in-the-server"></a>Hozzon létre egy adatbázist a kiszolgálón

Hozzon létre egy [S0 teljesítményszintű](sql-database-service-tiers.md) adatbázist a kiszolgálón az [az sql db create](/cli/azure/sql/db#create) paranccsal. A következő példában egy `mySampleDatabase` nevű üres adatbázist hozunk létre. Igény szerint cserélje le ezt az előre meghatározott értéket.

```azurecli
az sql db create --resource-group myResourceGroup --server $servername \
    --name mySampleDatabase --service-objective S0
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A jelen gyűjteményben található **Csatlakozási** rövid útmutatók és az oktatóanyag-gyűjtemény oktatóanyagai is erre a rövid útmutatóra épülnek. Ha azt tervezi, hogy az ezt rövid útmutatókkal vagy az oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő paranccsal törölheti az ehhez a rövid útmutatóhoz létrehozott összes erőforrást.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

- Az SQL Server Management Studióval történő csatlakozáshoz és lekérdezéshez lásd [az SSMS segítségével történő csatlakozással és lekérdezéssel](sql-database-connect-query-ssms.md) foglalkozó témakört.
- Ha a Visual Studióval szeretne kapcsolódni, lásd a [Visual Studióval végzett csatlakozásról és lekérdezésről](sql-database-connect-query.md) szóló témakört.
- Az SQL Database műszaki áttekintését az [SQL Database szolgáltatást ismertető](sql-database-technical-overview.md) témakörben találja.

