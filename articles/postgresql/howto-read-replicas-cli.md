---
title: Olvasási replikák kezelése – Azure CLI, REST API-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ismerje meg, hogyan kezelheti az olvasási replikákat Azure Database for PostgreSQL egyetlen kiszolgálón az Azure CLI-ből és REST API
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: e9be14548704557b4bdd39119294671852040348
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84636580"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Olvasási replikák létrehozása és kezelése az Azure CLI-ből, REST API

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet olvasási replikákat Azure Database for PostgreSQL az Azure CLI és a REST API használatával. Az olvasási replikákkal kapcsolatos további tudnivalókért tekintse meg az [áttekintést](concepts-read-replicas.md).

## <a name="azure-replication-support"></a>Azure-beli replikálás támogatása
Az [olvasási replikák](concepts-read-replicas.md) és a [logikai dekódolás](concepts-logical.md) a postgres írási előre megadott naplójától (Wal) függ. Ehhez a két szolgáltatáshoz különböző naplózási szintek szükségesek a postgres. A logikai dekódolás magasabb szintű naplózást igényel, mint az olvasási replikák.

A megfelelő naplózási szint konfigurálásához használja az Azure-replikáció támogatási paraméterét. Az Azure-beli replikáció támogatásának három beállítási lehetősége van:

* **Off** – a legkevesebb információt helyezi el a Wal-ben. Ez a beállítás nem érhető el a legtöbb Azure Database for PostgreSQL kiszolgálón.  
* **Replika** – részletesebb, mint a **kikapcsolás**. Az [olvasási replikák](concepts-read-replicas.md) működéséhez szükséges naplózás minimális szintje. Ez a beállítás az alapértelmezett a legtöbb kiszolgálón.
* **Logikai** – részletesebb, mint a **replika**. Ez a minimális szintű naplózás a logikai dekódolás működéséhez. Ebben a beállításban az olvasási replikák is működnek.

A kiszolgálót újra kell indítani a paraméter módosítása után. Belsőleg ez a paraméter a és a postgres paramétereket állítja be `wal_level` `max_replication_slots` `max_wal_senders` .

## <a name="azure-cli"></a>Azure CLI
Az olvasási replikákat az Azure CLI használatával hozhatja létre és kezelheti.

### <a name="prerequisites"></a>Előfeltételek

- [Az Azure CLI 2,0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Egy [Azure Database for PostgreSQL kiszolgáló](quickstart-create-server-up-azure-cli.md) a főkiszolgálóként.


### <a name="prepare-the-master-server"></a>A fő kiszolgáló előkészítése

1. Keresse meg a főkiszolgáló `azure.replication_support` értékét. Az olvasási replikák működéséhez legalább REPLIKÁnak kell lennie.

   ```azurecli-interactive
   az postgres server configuration show --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support
   ```

2. Ha `azure.replication_support` a nem legalább replika, állítsa be. 

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

3. A módosítás alkalmazásához indítsa újra a kiszolgálót.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Olvasási replika létrehozása

Az az [postgres Server replika Create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) paranccsal a következő paramétereket kell megadni:

| Beállítás | Példaérték | Description  |
| --- | --- | --- |
| resource-group | myResourceGroup |  Az az erőforráscsoport, amelyben a replika-kiszolgáló létre lesz hozva.  |
| name | mydemoserver – replika | A létrehozott új replika-kiszolgáló neve. |
| source-server | mydemoserver | A replikálni kívánt létező főkiszolgáló neve vagy erőforrás-azonosítója. Ha azt szeretné, hogy a replika és a főkiszolgáló erőforráscsoportok eltérőek legyenek, használja az erőforrás-azonosítót. |

Az alábbi CLI-példában a replika a főkiszolgálóval megegyező régióban jön létre.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Egy több régióból származó olvasási replika létrehozásához használja a `--location` paramétert. Az alábbi CLI-példa létrehozza a replikát az USA nyugati régiójában.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Ha többet szeretne megtudni arról, hogy mely régiókban hozhat létre replikát, látogasson el a [replika áttekintése című cikkben](concepts-read-replicas.md). 

Ha nem állította be a `azure.replication_support` paramétert egy általános célú vagy memóriára optimalizált főkiszolgálón lévő **replika** számára, és újraindítja a kiszolgálót, hibaüzenetet kap. A replika létrehozása előtt végezze el a két lépést.

A replika ugyanazokkal a számítási és tárolási beállításokkal jön létre, mint a főkiszolgáló. A replika létrehozása után több beállítás is módosítható a főkiszolgálótól függetlenül: számítási generáció, virtuális mag, tárterület és biztonsági mentési megőrzési időszak. Az árképzési szint külön is módosítható, kivéve az alapszintű csomagból vagy abból.

> [!IMPORTANT]
> A főkiszolgálói beállítás új értékre való frissítése előtt frissítse a replika beállításait egy egyenlő vagy nagyobb értékre. Ez a művelet segíti a replikát a főkiszolgálón végrehajtott bármilyen módosítással.

### <a name="list-replicas"></a>Replikák listázása
A főkiszolgáló replikáinak listáját az [az postgres Server replika List](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) parancs használatával tekintheti meg.

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Replikálás megszakítása egy másodpéldány-kiszolgálón
Az [az postgres Server replika leállítása](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) paranccsal leállíthatja a replikációt a főkiszolgáló és egy olvasási replika között.

Miután leállította a replikálást egy főkiszolgálóra és egy olvasási replikára, nem vonható vissza. Az olvasási replika önálló kiszolgáló lesz, amely támogatja az olvasást és az írást is. Az önálló kiszolgáló nem hozható létre újra replikába.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-master-or-replica-server"></a>Fő-vagy replika-kiszolgáló törlése
A fő-vagy a replika-kiszolgáló törléséhez használja az az [postgres Server delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) parancsot.

Főkiszolgáló törlésekor a rendszer leállítja a replikálást az összes olvasási replikára. Az olvasási replikák olyan önálló kiszolgálók lesznek, amelyek már támogatják az olvasást és az írást is.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST API
Az olvasási replikákat az [Azure REST API](/rest/api/azure/)használatával hozhatja létre és kezelheti.

### <a name="prepare-the-master-server"></a>A fő kiszolgáló előkészítése

1. Keresse meg a főkiszolgáló `azure.replication_support` értékét. Az olvasási replikák működéséhez legalább REPLIKÁnak kell lennie.

   ```http
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

2. Ha `azure.replication_support` a nem legalább replika, állítsa be.

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

   ```JSON
   {
    "properties": {
    "value": "replica"
    }
   }
   ```

2. A módosítás alkalmazásához [indítsa újra a kiszolgálót](/rest/api/postgresql/servers/restart) .

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>Olvasási replika létrehozása
Olvasási replikát a [create API](/rest/api/postgresql/servers/create)használatával hozhat létre:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}"
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
A Master Server replikáinak listáját a [replika lista API](/rest/api/postgresql/replicas/listbyserver)használatával tekintheti meg:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Replikálás megszakítása egy másodpéldány-kiszolgálón
A [frissítési API](/rest/api/postgresql/servers/update)használatával leállíthatja a replikációt a főkiszolgáló és az olvasási replika között.

Miután leállította a replikálást egy főkiszolgálóra és egy olvasási replikára, nem vonható vissza. Az olvasási replika önálló kiszolgáló lesz, amely támogatja az olvasást és az írást is. Az önálló kiszolgáló nem hozható létre újra replikába.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>Fő-vagy replika-kiszolgáló törlése
A fő-vagy a replika-kiszolgáló törléséhez használja a [delete API](/rest/api/postgresql/servers/delete)-t:

Főkiszolgáló törlésekor a rendszer leállítja a replikálást az összes olvasási replikára. Az olvasási replikák olyan önálló kiszolgálók lesznek, amelyek már támogatják az olvasást és az írást is.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>További lépések
* További információ [az olvasási replikákkal kapcsolatban Azure Database for PostgreSQL](concepts-read-replicas.md).
* Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat a Azure Portalban](howto-read-replicas-portal.md).
