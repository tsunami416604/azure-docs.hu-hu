---
title: Olvasási replikák létrehozása és kezelése a Azure Database for MariaDBban – AZURE CLI, REST API
description: Ez a cikk bemutatja, hogyan állíthatja be és kezelheti az olvasási replikákat Azure Database for MariaDB az Azure CLI és a REST API használatával.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 8b3572182832dc7692f6475be44281f56cf58571
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122761"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli-and-rest-api"></a>Olvasási replikák létrehozása és kezelése a Azure Database for MariaDB az Azure CLI és a REST API használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet olvasási replikákat a Azure Database for MariaDB szolgáltatásban az Azure CLI és a REST API használatával.

## <a name="azure-cli"></a>Azure CLI
Az olvasási replikákat az Azure CLI használatával hozhatja létre és kezelheti.

### <a name="prerequisites"></a>Előfeltételek

- [Telepítse az Azure CLI 2.0-t.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Egy [Azure Database for MariaDB kiszolgáló](quickstart-create-mariadb-server-database-using-azure-portal.md) , amely főkiszolgálóként lesz felhasználva. 

> [!IMPORTANT]
> Az olvasási replika funkció csak a általános célú vagy a memória optimalizált árképzési szintjein Azure Database for MariaDB-kiszolgálókon érhető el. Győződjön meg, hogy a fölérendelt kiszolgáló árképzési szintek egyikét.

### <a name="create-a-read-replica"></a>Hozzon létre egy olvasható replika

Egy olvasási adatbázisreplika-kiszolgáló a következő parancs használatával hozható létre:

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

A `az mariadb server replica create` parancs paraméterei a következők:

| Beállítás | Példaérték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az erőforráscsoport, ahol az adatbázisreplika-kiszolgálót hoz létre.  |
| név | mydemoreplicaserver | A létrehozott új replikakiszolgáló neve. |
| source-server | mydemoserver | A neve vagy azonosítója meglévő főkiszolgálójának a replikáláshoz. |

Egy több régióból származó olvasási replika létrehozásához használja `--location` a paramétert. 

> [!NOTE]
> A régiók közötti replikáció előzetes verzióban érhető el.

Az alábbi CLI-példa létrehozza a replikát az USA nyugati régiójában.

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Ha többet szeretne megtudni arról, hogy mely régiókban hozhat létre replikát, látogasson el a [replika áttekintése című cikkben](concepts-read-replicas.md). 

> [!NOTE]
> A kiszolgáló konfigurációval megegyező a fő olvasható replikák jönnek létre. A másodpéldány konfigurációjának a létrehozása után módosítható. Javasoljuk, hogy az adatbázisreplika-kiszolgáló konfigurációs kell tárolni annak érdekében, hogy a replika nem tudják tartani a főkiszolgálóval a főkiszolgáló-nál nagyobb vagy egyenlő értéken.

### <a name="list-replicas-for-a-master-server"></a>Egy fölérendelt kiszolgáló replikáit listája

Egy adott főkiszolgáló összes replika megtekintéséhez futtassa a következő parancsot: 

```azurecli-interactive
az mariadb server replica list --server-name mydemoserver --resource-group myresourcegroup
```

A `az mariadb server replica list` parancs paraméterei a következők:

| Beállítás | Példaérték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az erőforráscsoport, ahol az adatbázisreplika-kiszolgálót hoz létre.  |
| server-name | mydemoserver | A neve vagy a fölérendelt kiszolgáló azonosítója. |

### <a name="stop-replication-to-a-replica-server"></a>Az adatbázisreplika-kiszolgáló replikáció leállítása

> [!IMPORTANT]
> A kiszolgáló replikációjának leállítása nem vonható vissza. Ha a replikáció leállt, a master és a replika között, nem lehet visszavonni. Az adatbázisreplika-kiszolgáló ezután lesz egy önálló kiszolgáló, és már támogatja az olvasási és írási műveletek. Ez a kiszolgáló nem hajtható végre egy replika be újra.

Replikáció egy olvasási adatbázisreplika-kiszolgálóhoz a következő paranccsal állítható le:

```azurecli-interactive
az mariadb server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

A `az mariadb server replica stop` parancs paraméterei a következők:

| Beállítás | Példaérték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az erőforráscsoport, ahol az adatbázisreplika-kiszolgálón található.  |
| név | mydemoreplicaserver | Replikációleállítási az adatbázisreplika-kiszolgáló neve. |

### <a name="delete-a-replica-server"></a>Adatbázisreplika-kiszolgáló törlése

Az olvasási replika kiszolgálójának törléséhez futtassa az az **[MariaDB Server delete](/cli/azure/mariadb/server)** parancsot.

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-master-server"></a>Egy fölérendelt kiszolgáló törlése

> [!IMPORTANT]
> Egy fölérendelt kiszolgáló törlése az összes replika kiszolgálók replikálását, és törli magát a főkiszolgáló. Replikakiszolgáló önálló kiszolgálók által mostantól támogatják az olvasási és írási műveletek válnak.

A főkiszolgálók törléséhez futtassa az az **[MariaDB Server delete](/cli/azure/mariadb/server)** parancsot.

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="rest-api"></a>REST API
Az olvasási replikákat az [Azure REST API](/rest/api/azure/)használatával hozhatja létre és kezelheti.

### <a name="create-a-read-replica"></a>Hozzon létre egy olvasható replika
Olvasási replikát a [create API](/rest/api/mariadb/servers/create)használatával hozhat létre:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Ha többet szeretne megtudni arról, hogy mely régiókban hozhat létre replikát, látogasson el a [replika áttekintése című cikkben](concepts-read-replicas.md). 

Ha nem állította be a `azure.replication_support` paramétert egy általános célú vagy memóriára optimalizált főkiszolgálón lévő **replika** számára, és újraindítja a kiszolgálót, hibaüzenetet kap. A replika létrehozása előtt végezze el a két lépést.

A replika ugyanazokkal a számítási és tárolási beállításokkal jön létre, mint a főkiszolgáló. A replika létrehozása után több beállítás is módosítható a főkiszolgálótól függetlenül: számítási generáció, virtuális mag, tárterület és biztonsági mentési megőrzési időszak. Az árképzési szint külön is módosítható, kivéve az alapszintű csomagból vagy abból.


> [!IMPORTANT]
> A főkiszolgálói beállítás új értékre való frissítése előtt frissítse a replika beállításait egy egyenlő vagy nagyobb értékre. Ez a művelet segíti a replikát a főkiszolgálón végrehajtott bármilyen módosítással.

### <a name="list-replicas"></a>Replikák listázása
A Master Server replikáinak listáját a [replika lista API](/rest/api/mariadb/replicas/listbyserver)használatával tekintheti meg:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Az adatbázisreplika-kiszolgáló replikáció leállítása
A [frissítési API](/rest/api/mariadb/servers/update)használatával leállíthatja a replikációt a főkiszolgáló és az olvasási replika között.

Miután leállította a replikálást egy főkiszolgálóra és egy olvasási replikára, nem vonható vissza. Az olvasási replika önálló kiszolgáló lesz, amely támogatja az olvasást és az írást is. Az önálló kiszolgáló nem hozható létre újra replikába.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>Fő-vagy replika-kiszolgáló törlése
A fő-vagy a replika-kiszolgáló törléséhez használja a [delete API](/rest/api/mariadb/servers/delete)-t:

Főkiszolgáló törlésekor a rendszer leállítja a replikálást az összes olvasási replikára. Az olvasási replikák olyan önálló kiszolgálók lesznek, amelyek már támogatják az olvasást és az írást is.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>További lépések

- Tudjon meg többet [replikák olvasása](concepts-read-replicas.md)
