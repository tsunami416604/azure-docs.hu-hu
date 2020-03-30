---
title: A virtuális hálózat és a tűzfal mögötti tárfiók naplózása
description: Naplózás konfigurálása adatbázis-események írásához a virtuális hálózat és tűzfal mögötti tárfiókba
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/19/2020
ms.custom: azure-synapse
ms.openlocfilehash: 6345d210e26747f921595039a2a3c8e11be11fda
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387631"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>Naplózás írása virtuális hálózat és tűzfal mögötti tárfiókba

Az [Azure SQL Database](sql-database-technical-overview.md) és az Azure [Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) naplózása támogatja az adatbázis-események írását egy Azure [Storage-fiókba](../storage/common/storage-account-overview.md) egy virtuális hálózat és tűzfal mögött. 

Ez a cikk az Azure SQL Server és az Azure storage-fiók konfigurálásának két módját ismerteti ehhez a beállításhoz. Az első az Azure Portalt használja, a második pedig a REST-et.

### <a name="background"></a>Háttér

[Az Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md) az Azure-beli magánhálózat alapvető építőköve. A virtuális hálózat számos Azure-erőforrást, például az Azure virtuális gépeket (VM) teszi lehetővé, hogy biztonságosan kommunikáljanak egymással, az interneten és a helyszíni hálózatokon. A virtuális hálózat hasonló a saját adatközponthagyományos hálózatához, de az Azure-infrastruktúra további előnyeit, például a méretezést, a rendelkezésre állást és az elkülönítést is magával hozza.

Ha többet szeretne megtudni a virtuális hálózatok fogalmak, gyakorlati tanácsok és még sok más, olvassa el [a Mi az Azure virtuális hálózat.](../virtual-network/virtual-networks-overview.md)

A virtuális hálózat létrehozásáról a Rövid [útmutató: Virtuális hálózat létrehozása az Azure Portalon című](../virtual-network/quick-create-portal.md)témakörben olvashat bővebben.

## <a name="prerequisites"></a>Előfeltételek

A naplózás a virtuális hálózat vagy tűzfal mögötti tárfiókba történő íráshoz a következő előfeltételek szükségesek:

> [!div class="checklist"]
> * Általános célú v2-es tárfiók. Ha általános célú v1- vagy blobtár-fiókkal rendelkezik, [frissítsen egy általános célú v2-es tárfiókra.](../storage/common/storage-account-upgrade.md) További információ: [Types of storage accounts](../storage/common/storage-account-overview.md#types-of-storage-accounts).
> * A tárfióknak ugyanazon az előfizetésen és ugyanazon a helyen kell lennie, mint az Azure SQL Database-kiszolgáló. 
> * Az Azure Storage-fiók megköveteli. `Allow trusted Microsoft services to access this storage account` Állítsa be ezt a tárfiók **tűzfalai és a virtuális hálózatok**.
> * A kijelölt `Microsoft.Authorization/roleAssignments/write` tárfiókhoz engedéllyel kell rendelkeznie. További információ: [Azure beépített szerepkörök.](../role-based-access-control/built-in-roles.md)

## <a name="configure-in-azure-portal"></a>Konfigurálás az Azure Portalon

Csatlakozzon az [Azure Portalhoz](https://portal.azure.com) az előfizetésével. Keresse meg az erőforráscsoportot és az Azure SQL adatbázis-kiszolgálót.

1. Kattintson a **Naplózás gombra** a Biztonság fejléc alatt. Válassza **a Be**lehetőséget.

2. Válassza a **Tárolás**lehetőséget. Válassza ki azt a tárfiókot, ahová a naplókat menti. A tárfióknak meg kell felelnie az Előfeltételek listában felsorolt [követelményeknek.](#prerequisites)

3. **A tár részleteinek megnyitása** 

  > [!NOTE]
  > Ha a kiválasztott tárfiók a virtuális hálózat mögött van, a következő üzenet jelenik meg:
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage: requires an Active Directory admin on the server; enables 'Allow trusted Microsoft services to access this storage account' on the storage account; and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >Ha nem látja ezt az üzenetet, majd a tárfiók nem a virtuális hálózat mögött található.

4. Adja meg a megőrzési időszak napjainak számát. Ezt követően kattintson az **OK** gombra. A megőrzési időszaknál régebbi naplók törlődnek.

5. Válassza a **Mentés** a naplózási beállításokat lehetőséget.

Sikeresen konfigurálta a naplózást, hogy egy virtuális hálózat vagy tűzfal mögötti tárfiókba írjon. 

## <a name="configure-with-rest-commands"></a>Konfigurálás REST parancsokkal

Az Azure Portal használatával a REST-parancsok használatával konfigurálhatja a naplózást, hogy adatbázis-eseményeket írjon egy virtuális hálózat és tűzfal mögötti tárfiókra. 

Az ebben a szakaszban található mintaparancsfájlok futtatása előtt frissítenikell a parancsfájlt. Cserélje le a következő értékeket a parancsfájlokban:

|Mintaérték|A minta leírása|
|:-----|:-----|
|`<subscriptionId>`| Azure-előfizetés azonosítója|
|`<resource group>`| Erőforráscsoport|
|`<sql database server>`| Az Azure SQL adatbázis-kiszolgáló neve|
|`<administrator login>`| SQL-adatbázis rendszergazdai fiókja |
|`<complex password>`| A rendszergazdai fiók összetett jelszava|

Az SQL Audit beállítása események írására egy virtuális hálózat vagy tűzfal mögötti tárfiókba:

1. Regisztrálja az Azure SQL Database-kiszolgálót az Azure Active Directory (Azure AD) szolgáltatással. Használja a PowerShell vagy a REST API.Use either PowerShell or REST API.

   **Powershell**
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```
   
   [**REST API**](https://docs.microsoft.com/rest/api/sql/servers/createorupdate):

   Mintakérelem

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2015-05-01-preview
   ```

   A kérés törzse

   ```json
   {
   "identity": {
              "type": "SystemAssigned",
              },
   "properties": {
     "fullyQualifiedDomainName": "<azure server name>.database.windows.net",
     "administratorLogin": "<administrator login>",
     "administratorLoginPassword": "<complex password>",
     "version": "12.0",
     "state": "Ready"
   }
   ```

2. Nyissa meg az [Azure Portalt](https://portal.azure.com). Nyissa meg a tárfiókot. Keresse meg **a hozzáférés-vezérlési (IAM)** elem, és kattintson **a Szerepkör-hozzárendelés hozzáadása gombra.** **Rendeljen Storage Blob Data Contributor** RBAC szerepkört az Azure SQL Server üzemelteti az Azure Active Directory (Azure AD) az előző lépésben regisztrált Azure SQL-adatbázis.

   > [!NOTE]
   > Ezt a lépést csak tulajdonosi jogosultsággal rendelkező tagok hajthatják végre. Az Azure-erőforrások különböző beépített szerepköreiről az [Azure beépített szerepkörei](../role-based-access-control/built-in-roles.md)című részt.

3. Konfigurálja [az Azure SQL server blobnaplózási házirendje](/rest/api/sql/server%20auditing%20settings/createorupdate), anélkül, hogy megadna egy *storageAccountAccessKey-t:*

   Mintakérelem

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2017-03-01-preview
   ```

   A kérés törzse

   ```json
   {
     "properties": {
      "state": "Enabled",
      "storageEndpoint": "https://<storage account>.blob.core.windows.net"
     }
   }
   ```

## <a name="next-steps"></a>További lépések

- [A PowerShell segítségével hozzon létre egy virtuális hálózati szolgáltatás végpontját, majd egy virtuális hálózati szabályt az Azure SQL Database számára.](sql-database-vnet-service-endpoint-rule-powershell.md)
- [Virtuális hálózati szabályok: REST API-kkal végzett műveletek](/rest/api/sql/virtualnetworkrules)
- [Virtuális hálózati szolgáltatás végpontjainak és szabályainak használata adatbázis-kiszolgálókhoz](sql-database-vnet-service-endpoint-rule-overview.md)
