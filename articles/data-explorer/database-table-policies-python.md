---
title: 'Szabályzatok létrehozása az Azure Adatkezelő-fürthöz és-adatbázishoz az Azure Adatkezelő Python Library használatával '
description: Ebből a cikkből megtudhatja, hogyan hozhat létre szabályzatokat a Python használatával.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: a0fe86e2dcb802b822cb08ed0922b5da9c5cfd1c
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667280"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-by-using-python"></a>Adatbázis-és Table-szabályzatok létrehozása az Azure Adatkezelőhoz a Python használatával

> [!div class="op_single_selector"]
> * [C#](database-table-policies-csharp.md)
> * [Python](database-table-policies-python.md)
>

Az Azure Data Explorer egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Ebben a cikkben a Python használatával hoz létre adatbázis-és Table-házirendeket az Azure Adatkezelőhoz.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Egy tesztfürt és egy adatbázis](create-cluster-database-python.md)
* [Egy teszt tábla](python-ingest-data.md#create-a-table-on-your-cluster)

## <a name="install-the-data-libraries"></a>Az adattárak telepítése

```
pip install azure-common
pip install azure-mgmt-kusto
pip install azure-kusto-data (Optional, for changing table's policies)
```

## <a name="authentication"></a>Hitelesítés
A cikkben szereplő példák futtatásához szükség van egy Azure AD-alkalmazásra és egy egyszerű szolgáltatásra, amely hozzáférhet az erőforrásokhoz. Ugyanazt az Azure AD-alkalmazást használhatja a [tesztelési fürtből és az adatbázisból](create-cluster-database-csharp.md#authentication)történő hitelesítéshez is. Ha másik Azure AD-alkalmazást szeretne használni, tekintse meg az [Azure ad](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) -alkalmazás létrehozása az ingyenes Azure ad-alkalmazás létrehozásához és a szerepkör-hozzárendelés hozzáadása az előfizetési hatókörben című témakört. Azt is bemutatja, hogyan kérhető le a `Directory (tenant) ID`, a `Application ID`és a `Client Secret`. Előfordulhat, hogy az új Azure AD-alkalmazást az adatbázis rendszerbiztonsági tagjának kell felvennie, lásd: az [azure adatkezelő Database-engedélyek kezelése](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions).    

## <a name="alter-database-retention-policy"></a>Adatbázis adatmegőrzési szabályzatának módosítása
Egy 10 napos törlési időszakot tartalmazó adatmegőrzési szabályzatot állít be.

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabaseUpdate
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg";
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster";
database_name = "mykustodatabase";

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kustoManagementClient.databases.update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name,
                                           parameters=DatabaseUpdate(soft_delete_period=datetime.timedelta(days=10)))
```

## <a name="alter-database-cache-policy"></a>Az adatbázis-gyorsítótárazási szabályzat módosítása
Beállítja az adatbázishoz tartozó gyorsítótár-szabályzatot, amelynek az utolsó öt napja a fürt SSD-lemeze lesz.

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabaseUpdate
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg";
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster";
database_name = "mykustodatabase";

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kustoManagementClient.databases.update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name,
                                           parameters=DatabaseUpdate(hot_cache_period=datetime.timedelta(days=5)))
```

## <a name="alter-table-cache-policy"></a>A tábla gyorsítótár-házirendjének módosítása
Beállítja a táblázathoz tartozó gyorsítótár-szabályzatot, amely szerint az utolsó öt nap a fürt SSD-lemezén lesz.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
kusto_uri = "https://<ClusterName>.<Region>.kusto.windows.net"
database_name = "<DatabaseName>"
#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
#Application ID
client_id_to_add = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

kusto_connection_string_builder = KustoConnectionStringBuilder.with_aad_application_key_authentication(connection_string=kusto_uri, aad_app_id=client_id, app_key=client_secret, authority_id=tenant_id)

#The table that is created as part of the Prerequisites
table_name = "<TableName>"
caching_policy = r'hot = 5d'
command = '.alter table {} policy caching '.format(table_name) +  caching_policy
kusto_client.execute_mgmt(database_name, command)
```

## <a name="add-a-new-principal-for-database"></a>Új rendszerbiztonsági tag hozzáadása az adatbázishoz
Új Azure AD-alkalmazás hozzáadása rendszergazdai rendszerbiztonsági tagként az adatbázishoz

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabasePrincipal
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
#Application ID
client_id_to_add = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg";
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster";
database_name = "mykustodatabase";
role = "Admin"
principle_name = "<database_principle_name>";
type_name = "App"
kustoManagementClient.databases.add_principals(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name,
                         value=[DatabasePrincipal(role=role, name=principle_name, type=type_name, app_id=client_id_to_add, tenant_name=tenant_id)])
```

## <a name="next-steps"></a>Következő lépések

* [További információ az adatbázis-és táblázat-házirendekről](https://docs.microsoft.com/azure/kusto/management/policies)
