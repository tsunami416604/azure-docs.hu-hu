---
title: Olvasási replikák kezelése - Azure CLI, REST API - Azure Database for PostgreSQL - Single Server
description: Ismerje meg, hogyan kezelheti a replikák olvasását az Azure Database for PostgreSQL - Single Server szolgáltatásban az Azure CLI és REST API-ból
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: b10ac3b4bc9dacd723b8b1265911df721b781189
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774807"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Olvasási replikák létrehozása és kezelése az Azure CLI, REST API-ból

Ebben a cikkben megtudhatja, hogyan hozhat létre és kezelhet olvasási replikákat az Azure Database for PostgreSQL-ben az Azure CLI és REST API használatával. Ha többet szeretne megtudni az olvasott replikákról, olvassa el az [áttekintést.](concepts-read-replicas.md)

## <a name="azure-cli"></a>Azure CLI
Az Azure CLI használatával olvasási replikákat hozhat létre és kezelhet.

### <a name="prerequisites"></a>Előfeltételek

- [Az Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Egy [Azure-adatbázis a PostgreSQL-kiszolgálóhoz](quickstart-create-server-up-azure-cli.md) a fő kiszolgáló.


### <a name="prepare-the-master-server"></a>A főkiszolgáló előkészítése
Ezeket a lépéseket kell használni, hogy készítsen egy fő kiszolgáló az általános célú vagy memória optimalizált szintek.

A `azure.replication_support` paramétert **a főkiszolgálón a REP-re** kell állítani. Ha ez a statikus paraméter megváltozik, a módosítás érvénybe léptetéséhez kiszolgáló-újraindításszükséges.

1. A `azure.replication_support` REPLICA beállítás.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

> [!NOTE]
> Ha az azure.replication_support beállítása közben az "Érvénytelen érték adott" hibaüzenet jelenik meg az Azure CLI-ből, valószínű, hogy a kiszolgáló alapértelmezés szerint már rendelkezik REPLIKÁval. A hiba megakadályozza, hogy ez a beállítás megfelelően tükröződjön az újabb kiszolgálókon, ahol a REP A belső alapértelmezett. <br><br>
> Kihagyhatja a fő lépések előkészítését, és a kópia létrehozásához. <br><br>
> Ha meg szeretné erősíteni, hogy a kiszolgáló ebbe a kategóriába tartozik, keresse fel a kiszolgáló replikációs lapját az Azure Portalon. A "Replikáció letiltása" szürkén jelenik meg, és a "Replika hozzáadása" aktív lesz az eszköztáron.

2. A módosítás alkalmazásához indítsa újra a kiszolgálót.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Olvasott kópia létrehozása

Az [az postgres kiszolgálóreplika létrehozási](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) parancsához a következő paraméterek szükségesek:

| Beállítás | Példaérték | Leírás  |
| --- | --- | --- |
| resource-group | myResourceGroup |  Az az erőforráscsoport, amelyhez a replikakiszolgáló létre jön.  |
| név | mydemoserver-replika | A létrehozott új replikakiszolgáló neve. |
| source-server | mydemoserver | A replikálandó meglévő főkiszolgáló neve vagy erőforrásazonosítója. |

Az alábbi CLI-példában a kópia ugyanabban a régióban jön létre, mint a főkiszolgáló.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Régióközi olvasási replika `--location` létrehozásához használja a paramétert. Az alábbi CLI-példa létrehozza a replika az USA nyugati részén.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Ha többet szeretne megtudni arról, hogy mely régiókban hozhat létre kópiát, olvassa el a [replikaolvasással kapcsolatos útmutatóról szóló cikket.](concepts-read-replicas.md) 

Ha nem állította be `azure.replication_support` a **paramétert a REPLICA-ra** egy általános célú vagy memóriaoptimalizált főkiszolgálón, és újraindította a kiszolgálót, hibaüzenet jelenik meg. A kópia létrehozása előtt hajtsa végre ezt a két lépést.

A kópia a főkiszolgálóval azonos számítási és tárolási beállításokkal jön létre. A replika létrehozása után számos beállítás módosítható a fő kiszolgálótól függetlenül: számítási generálás, virtuális magok, tárolás és biztonsági másolat megőrzési időszak. A tarifacsomag is lehet változtatni egymástól függetlenül, kivéve, hogy vagy az alapszintű szintről.

> [!IMPORTANT]
> Mielőtt a főkiszolgáló-beállítás új értékre frissülne, frissítse a replikabeállítást azonos vagy nagyobb értékre. Ez a művelet segít a kópiának lépést tartani a főkiszolgálón végzett módosításokkal.

### <a name="list-replicas"></a>Replikák listázása
A főkiszolgáló kópiáinak listáját az [az postgres kiszolgálóreplikalista](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) parancsával tekintheti meg.

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>A replikakiszolgálóra irányuló replikáció leállítása
A főkiszolgáló és az olvasási kópia közötti replikáció leállíthatja az [az postgres kiszolgáló replikaleállító](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) parancsával.

Miután leállította a replikációt a főkiszolgálóra és az olvasási replikára, azt nem lehet visszavonni. Az olvasási replika önálló kiszolgálóvá válik, amely támogatja az olvasást és az írást. Az önálló kiszolgáló nem alakítható újra kópiává.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-master-or-replica-server"></a>Fő- vagy replikakiszolgáló törlése
Fő- vagy replikakiszolgáló törléséhez használja az [az postgres kiszolgáló törlési](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) parancsát.

Főkiszolgáló törlésekor az összes olvasási replikára történő replikáció leáll. Az olvasási replikák önálló kiszolgálókká válnak, amelyek mostantól támogatják az olvasást és az írást is.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST API
Az [Azure REST API használatával](/rest/api/azure/)olvasási replikákat hozhat létre és kezelhet.

### <a name="prepare-the-master-server"></a>A főkiszolgáló előkészítése
Ezeket a lépéseket kell használni, hogy készítsen egy fő kiszolgáló az általános célú vagy memória optimalizált szintek.

A `azure.replication_support` paramétert **a főkiszolgálón a REP-re** kell állítani. Ha ez a statikus paraméter megváltozik, a módosítás érvénybe léptetéséhez kiszolgáló-újraindításszükséges.

1. A `azure.replication_support` REPLICA beállítás.

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

2. [A](/rest/api/postgresql/servers/restart) módosítás alkalmazásához indítsa újra a kiszolgálót.

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>Olvasott kópia létrehozása
Az [API-létrehozás imént](/rest/api/postgresql/servers/create)létrehozhat egy olvasási replikát:

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
> Ha többet szeretne megtudni arról, hogy mely régiókban hozhat létre kópiát, olvassa el a [replikaolvasással kapcsolatos útmutatóról szóló cikket.](concepts-read-replicas.md) 

Ha nem állította be `azure.replication_support` a **paramétert a REPLICA-ra** egy általános célú vagy memóriaoptimalizált főkiszolgálón, és újraindította a kiszolgálót, hibaüzenet jelenik meg. A kópia létrehozása előtt hajtsa végre ezt a két lépést.

A kópia a főkiszolgálóval azonos számítási és tárolási beállításokkal jön létre. A replika létrehozása után számos beállítás módosítható a fő kiszolgálótól függetlenül: számítási generálás, virtuális magok, tárolás és biztonsági másolat megőrzési időszak. A tarifacsomag is lehet változtatni egymástól függetlenül, kivéve, hogy vagy az alapszintű szintről.


> [!IMPORTANT]
> Mielőtt a főkiszolgáló-beállítás új értékre frissülne, frissítse a replikabeállítást azonos vagy nagyobb értékre. Ez a művelet segít a kópiának lépést tartani a főkiszolgálón végzett módosításokkal.

### <a name="list-replicas"></a>Replikák listázása
A főkiszolgáló kópiáinak listáját a [replikalista API](/rest/api/postgresql/replicas/listbyserver)használatával tekintheti meg:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>A replikakiszolgálóra irányuló replikáció leállítása
A főkiszolgáló és az olvasási kópia közötti replikációleállítható a [frissítési API használatával.](/rest/api/postgresql/servers/update)

Miután leállította a replikációt a főkiszolgálóra és az olvasási replikára, azt nem lehet visszavonni. Az olvasási replika önálló kiszolgálóvá válik, amely támogatja az olvasást és az írást. Az önálló kiszolgáló nem alakítható újra kópiává.

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

### <a name="delete-a-master-or-replica-server"></a>Fő- vagy replikakiszolgáló törlése
Fő- vagy replikakiszolgáló törléséhez használja a [delete API-t:](/rest/api/postgresql/servers/delete)

Főkiszolgáló törlésekor az összes olvasási replikára történő replikáció leáll. Az olvasási replikák önálló kiszolgálókká válnak, amelyek mostantól támogatják az olvasást és az írást is.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>További lépések
* További információ a [kópiák olvasásáról az Azure Database for PostgreSQL-ben.](concepts-read-replicas.md)
* Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat az Azure Portalon.](howto-read-replicas-portal.md)
