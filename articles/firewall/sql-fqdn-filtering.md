---
title: Az Azure Firewall alkalmazásszabályainak konfigurálása SQL fqdn-ekkel
description: Ebben a cikkben megtudhatja, hogyan konfigurálhatja az SQL-fqdns az Azure Firewall alkalmazásszabályok.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/19/2019
ms.author: victorh
ms.openlocfilehash: 858cfc9a8c15f1e33e688bb5086a58f194e7173f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501495"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Az Azure Firewall alkalmazásszabályainak konfigurálása SQL fqdn-ekkel

> [!IMPORTANT]
> Az Azure Firewall alkalmazásszabályok SQL fqdns jelenleg nyilvános előzetes verzióban.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Most már konfigurálhatja az Azure Firewall alkalmazásszabályok SQL fqdns. Ez lehetővé teszi, hogy a virtuális hálózatokból való hozzáférést csak a megadott SQL-kiszolgálópéldányokra korlátozza.

Az SQL teljes tartománynokkal szűrheti a forgalmat:

- A virtuális hálózatok egy Azure SQL-adatbázis vagy egy Azure SQL Data Warehouse. Például: Csak *a sql-server1.database.windows.net*való hozzáférést engedélyezze.
- A helyszíni Azure SQL felügyelt példányok vagy a virtuális hálózatokban futó SQL IaaS között.
- A küllős a küllős az Azure SQL felügyelt példányok vagy az SQL IaaS fut a virtuális hálózatokban.

A nyilvános előzetes verzió során az SQL FQDN-szűrés csak [proxy módban](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) (1433-as port) támogatott. Ha az SQL-t az alapértelmezett átirányítási módban használja, a [hálózati szabályok](overview.md#network-traffic-filtering-rules)részeként az SQL szolgáltatáscímkéje alapján szűrheti a hozzáférést.
Ha nem alapértelmezett portokat használ az SQL IaaS-forgalomhoz, konfigurálhatja ezeket a portokat a tűzfalalkalmazás-szabályokban.

Az SQL-fqdns alkalmazásszabályok jelenleg az Azure Portalon, az Azure CLI-n, a REST-en és a sablonokon keresztül érhetők el az összes régióban.

## <a name="configure-using-azure-cli"></a>Konfigurálás az Azure CLI használatával

1. Telepítsen egy [Azure-tűzfalat az Azure CLI használatával.](deploy-cli.md)
2. Ha az Azure SQL Database, az SQL Data Warehouse vagy az SQL Felügyelt példány forgalmát szűri, győződjön meg arról, hogy az SQL-kapcsolat mód **proxy**ra van állítva. Az SQL-kapcsolatmód közötti váltásról az [Azure SQL-kapcsolat beállításai című témakörben olvashat.](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli)

   > [!NOTE]
   > Az SQL *proxy* mód az *átirányításhoz*képest több késést eredményezhet. Ha továbbra is szeretné használni az átirányítási módot, amely az Azure-on belül csatlakozó ügyfelek alapértelmezett módja, szűrheti a hozzáférést az SQL [szolgáltatáscímkéjével](service-tags.md) a tűzfal [hálózati szabályokban.](tutorial-firewall-deploy-portal.md#configure-a-network-rule)

3. Alkalmazásszabály konfigurálása SQL fqdn-nel az SQL-kiszolgálóhoz való hozzáférés engedélyezéséhez:

   ```azurecli
   az extension add -n azure-firewall

   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>Konfigurálás az Azure Portal használatával
1. Telepítsen egy [Azure-tűzfalat az Azure CLI használatával.](deploy-cli.md)
2. Ha az Azure SQL Database, az SQL Data Warehouse vagy az SQL Felügyelt példány forgalmát szűri, győződjön meg arról, hogy az SQL-kapcsolat mód **proxy**ra van állítva. Az SQL-kapcsolatmód közötti váltásról az [Azure SQL-kapcsolat beállításai című témakörben olvashat.](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli)  

   > [!NOTE]
   > Az SQL *proxy* mód az *átirányításhoz*képest több késést eredményezhet. Ha továbbra is szeretné használni az átirányítási módot, amely az Azure-on belül csatlakozó ügyfelek alapértelmezett módja, szűrheti a hozzáférést az SQL [szolgáltatáscímkéjével](service-tags.md) a tűzfal [hálózati szabályokban.](tutorial-firewall-deploy-portal.md#configure-a-network-rule)
3. Adja hozzá az alkalmazásszabályt a megfelelő protokollal, porttal és SQL teljes tartománynnnel, majd válassza a **Mentés gombot.**
   ![alkalmazásszabály SQL fqdn-nel](media/sql-fqdn-filtering/application-rule-sql.png)
4. Sql-hozzáférés egy virtuális gép ről egy virtuális hálózat, amely szűri a forgalmat a tűzfalon keresztül. 
5. Ellenőrizze, hogy [az Azure Firewall-naplók a](log-analytics-samples.md) forgalom engedélyezett.

## <a name="next-steps"></a>További lépések

Az SQL-proxy- és átirányítási módokról az [Azure SQL adatbázis-kapcsolatarchitektúrájában olvashat.](../sql-database/sql-database-connectivity-architecture.md)