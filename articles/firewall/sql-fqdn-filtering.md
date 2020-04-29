---
title: Azure Firewall alkalmazás-szabályok konfigurálása SQL FQDN-sel
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja az SQL FQDN-ket Azure Firewall alkalmazási szabályokban.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/19/2019
ms.author: victorh
ms.openlocfilehash: 858cfc9a8c15f1e33e688bb5086a58f194e7173f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79501495"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Azure Firewall alkalmazás-szabályok konfigurálása SQL FQDN-sel

> [!IMPORTANT]
> Azure Firewall SQL FQDN-sel rendelkező alkalmazás-szabályok jelenleg nyilvános előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mostantól az SQL FQDN-sel is konfigurálhatja Azure Firewall alkalmazási szabályokat. Ez lehetővé teszi, hogy a virtuális hálózatokról csak a megadott SQL Server-példányokra korlátozza a hozzáférést.

Az SQL FQDN használatával szűrheti a forgalmat:

- A virtuális hálózatok egy Azure SQL Database vagy egy Azure SQL Data Warehouse. Például: csak a *SQL-server1.database.Windows.net*elérésének engedélyezése.
- A helyszínről az Azure SQL felügyelt példányaira vagy a virtuális hálózatok futó SQL-IaaS.
- Az Azure SQL felügyelt példányain vagy a virtuális hálózatok futó SQL-IaaS.

A nyilvános előzetes verzióban az SQL FQDN-szűrés csak [proxy módban](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) támogatott (1433-es port). Ha az SQL-t az alapértelmezett átirányítási módban használja, a [hálózati szabályok](overview.md#network-traffic-filtering-rules)részeként szűrheti a hozzáférést az SQL-szolgáltatás címkével.
Ha nem alapértelmezett portokat használ az SQL IaaS-forgalomhoz, ezeket a portokat a tűzfal alkalmazási szabályaiban állíthatja be.

Az SQL FQDN-sel rendelkező alkalmazási szabályok jelenleg minden régióban elérhetők az Azure Portal, az Azure CLI, a REST és a sablonok használatával.

## <a name="configure-using-azure-cli"></a>Konfigurálás az Azure CLI használatával

1. Azure Firewall üzembe helyezése az [Azure CLI használatával](deploy-cli.md).
2. Ha Azure SQL Database, SQL Data Warehouse vagy SQL felügyelt példányra szűri a forgalmat, győződjön meg arról, hogy az SQL-kapcsolati mód **proxyra**van beállítva. Az SQL-kapcsolati mód váltásával kapcsolatos további információkért lásd: [Azure SQL-kapcsolati beállítások](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).

   > [!NOTE]
   > Az SQL *proxy* mód több késést eredményezhet az *átirányításhoz*képest. Ha továbbra is az átirányítási módot szeretné használni, amely az Azure-on keresztül csatlakozó ügyfelek esetében az alapértelmezett, akkor a hozzáférést a tűzfal [hálózati szabályaiban](tutorial-firewall-deploy-portal.md#configure-a-network-rule)található SQL- [szolgáltatás címkével](service-tags.md) szűrheti.

3. SQL FQDN-sel rendelkező alkalmazás-szabály konfigurálása SQL-kiszolgálóhoz való hozzáférés engedélyezéséhez:

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
1. Azure Firewall üzembe helyezése az [Azure CLI használatával](deploy-cli.md).
2. Ha Azure SQL Database, SQL Data Warehouse vagy SQL felügyelt példányra szűri a forgalmat, győződjön meg arról, hogy az SQL-kapcsolati mód **proxyra**van beállítva. Az SQL-kapcsolati mód váltásával kapcsolatos további információkért lásd: [Azure SQL-kapcsolati beállítások](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).  

   > [!NOTE]
   > Az SQL *proxy* mód több késést eredményezhet az *átirányításhoz*képest. Ha továbbra is az átirányítási módot szeretné használni, amely az Azure-on keresztül csatlakozó ügyfelek esetében az alapértelmezett, akkor a hozzáférést a tűzfal [hálózati szabályaiban](tutorial-firewall-deploy-portal.md#configure-a-network-rule)található SQL- [szolgáltatás címkével](service-tags.md) szűrheti.
3. Adja hozzá az alkalmazási szabályt a megfelelő protokoll-, port-és SQL FQDN- **fájlhoz**, majd válassza a mentés lehetőséget.
   ![alkalmazási szabály SQL FQDN-sel](media/sql-fqdn-filtering/application-rule-sql.png)
4. SQL-hozzáférés egy virtuális gépről egy olyan VNet, amely a tűzfalon keresztül szűri a forgalmat. 
5. Annak ellenőrzése, hogy a [Azure Firewall naplókban](log-analytics-samples.md) engedélyezett-e a forgalom.

## <a name="next-steps"></a>További lépések

Az SQL proxy és az átirányítási módok megismeréséhez tekintse meg az [Azure SQL Database kapcsolódási architektúráját](../sql-database/sql-database-connectivity-architecture.md)ismertető témakört.