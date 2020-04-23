---
title: Olvasási replikák kezelése - Azure CLI, REST API - Azure Database for MariaDB
description: Ez a cikk ismerteti, hogyan állíthatja be és kezelheti az olvasási replikákat az Azure Database for MariaDB-ben az Azure CLI és REST API használatával.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/21/2020
ms.openlocfilehash: c5062bce572fbeda4143902ae6a04b31b9a89754
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025050"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli-and-rest-api"></a>Olvasási replikák létrehozása és kezelése a MariaDB Azure Database-ben az Azure CLI és REST API használatával

Ebben a cikkben megtudhatja, hogyan hozhat létre és kezelhet olvasási replikákat az Azure Database for MariaDB szolgáltatásban az Azure CLI és REST API használatával.

## <a name="azure-cli"></a>Azure CLI
Az Azure CLI használatával olvasási replikákat hozhat létre és kezelhet.

### <a name="prerequisites"></a>Előfeltételek

- [Az Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- A Főkiszolgálóként használt [Azure-adatbázis a MariaDB-kiszolgálóhoz.](quickstart-create-mariadb-server-database-using-azure-portal.md) 

> [!IMPORTANT]
> Az olvasási replika funkció csak az Azure Database for MariaDB-kiszolgálók az általános célú vagy a memória optimalizált tarifacsomagok hoz érhető el. Győződjön meg arról, hogy a főkiszolgáló a következő tarifacsomagok egyikében található.

### <a name="create-a-read-replica"></a>Olvasott kópia létrehozása

Az olvasási replikakiszolgáló a következő paranccsal hozható létre:

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

A `az mariadb server replica create` parancshoz a következő paraméterek szükségesek:

| Beállítás | Példaérték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az az erőforráscsoport, ahová a replikakiszolgáló létre jön.  |
| név | mydemoreplicaserver | A létrehozott új replikakiszolgáló neve. |
| source-server | mydemoserver | A replikálandó meglévő főkiszolgáló neve vagy azonosítója. |

Régióközi olvasási replika `--location` létrehozásához használja a paramétert. 

Az alábbi CLI-példa létrehozza a replika az USA nyugati részén.

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Ha többet szeretne megtudni arról, hogy mely régiókban hozhat létre kópiát, olvassa el a [replikaolvasással kapcsolatos útmutatóról szóló cikket.](concepts-read-replicas.md) 

> [!NOTE]
> Az olvasási kópiák a főkiszolgálóval azonos kiszolgálókonfigurációval jönnek létre. A replikakiszolgáló konfigurációja a létrehozás után módosítható. Ajánlott, hogy a replikakiszolgáló konfigurációját a főkiszolgálóval azonos vagy nagyobb értékeken kell tartani annak biztosítása érdekében, hogy a replika lépést tud tartani a főkiszolgálóval.

### <a name="list-replicas-for-a-master-server"></a>Főkiszolgáló kópiáinak listázása

Egy adott főkiszolgáló összes replikájának megtekintéséhez futtassa a következő parancsot: 

```azurecli-interactive
az mariadb server replica list --server-name mydemoserver --resource-group myresourcegroup
```

A `az mariadb server replica list` parancshoz a következő paraméterek szükségesek:

| Beállítás | Példaérték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az az erőforráscsoport, ahová a replikakiszolgáló létre jön.  |
| server-name | mydemoserver | A főkiszolgáló neve vagy azonosítója. |

### <a name="stop-replication-to-a-replica-server"></a>A replikakiszolgálóra irányuló replikáció leállítása

> [!IMPORTANT]
> A kiszolgálóra irányuló replikáció leállítása visszafordíthatatlan. Ha a replikáció leállt a főkiszolgáló és a replika között, azt nem lehet visszavonni. A replikakiszolgáló ezután önálló kiszolgálóvá válik, és mostantól támogatja az olvasást és az írást is. Ez a kiszolgáló nem hozható elő újra kópiává.

Az olvasási replikakiszolgálóra történő replikáció a következő paranccsal leállítható:

```azurecli-interactive
az mariadb server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

A `az mariadb server replica stop` parancshoz a következő paraméterek szükségesek:

| Beállítás | Példaérték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az az erőforráscsoport, ahol a replikakiszolgáló létezik.  |
| név | mydemoreplicaserver | A replikációt leállító replikakiszolgáló neve. |

### <a name="delete-a-replica-server"></a>Replikakiszolgáló törlése

Az olvasási replikakiszolgáló törlése az **[az mariadb kiszolgáló törlési](/cli/azure/mariadb/server)** parancsának futtatásával végezhető el.

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-master-server"></a>Főkiszolgáló törlése

> [!IMPORTANT]
> A főkiszolgáló törlése leállítja a replikálást az összes replikakiszolgálón, magát a főkiszolgálót pedig törli. A replikakiszolgálókból különálló kiszolgálók lesznek, amelyek az olvasási és írási műveleteket egyaránt támogatják.

Főkiszolgáló törléséhez futtassa az **[az mariadb kiszolgáló törlési](/cli/azure/mariadb/server)** parancsát.

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="rest-api"></a>REST API
Az [Azure REST API használatával](/rest/api/azure/)olvasási replikákat hozhat létre és kezelhet.

### <a name="create-a-read-replica"></a>Olvasott kópia létrehozása
Az [API-létrehozás imént](/rest/api/mariadb/servers/create)létrehozhat egy olvasási replikát:

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
> Ha többet szeretne megtudni arról, hogy mely régiókban hozhat létre kópiát, olvassa el a [replikaolvasással kapcsolatos útmutatóról szóló cikket.](concepts-read-replicas.md) 

Ha nem állította be `azure.replication_support` a **paramétert a REPLICA-ra** egy általános célú vagy memóriaoptimalizált főkiszolgálón, és újraindította a kiszolgálót, hibaüzenet jelenik meg. A kópia létrehozása előtt hajtsa végre ezt a két lépést.

A kópia a főkiszolgálóval azonos számítási és tárolási beállításokkal jön létre. A replika létrehozása után számos beállítás módosítható a fő kiszolgálótól függetlenül: számítási generálás, virtuális magok, tárolás és biztonsági másolat megőrzési időszak. A tarifacsomag is lehet változtatni egymástól függetlenül, kivéve, hogy vagy az alapszintű szintről.


> [!IMPORTANT]
> Mielőtt a főkiszolgáló-beállítás új értékre frissülne, frissítse a replikabeállítást azonos vagy nagyobb értékre. Ez a művelet segít a kópiának lépést tartani a főkiszolgálón végzett módosításokkal.

### <a name="list-replicas"></a>Replikák listázása
A főkiszolgáló kópiáinak listáját a [replikalista API](/rest/api/mariadb/replicas/listbyserver)használatával tekintheti meg:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>A replikakiszolgálóra irányuló replikáció leállítása
A főkiszolgáló és az olvasási kópia közötti replikációleállítható a [frissítési API használatával.](/rest/api/mariadb/servers/update)

Miután leállította a replikációt a főkiszolgálóra és az olvasási replikára, azt nem lehet visszavonni. Az olvasási replika önálló kiszolgálóvá válik, amely támogatja az olvasást és az írást. Az önálló kiszolgáló nem alakítható újra kópiává.

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

### <a name="delete-a-master-or-replica-server"></a>Fő- vagy replikakiszolgáló törlése
Fő- vagy replikakiszolgáló törléséhez használja a [delete API-t:](/rest/api/mariadb/servers/delete)

Főkiszolgáló törlésekor az összes olvasási replikára történő replikáció leáll. Az olvasási replikák önálló kiszolgálókká válnak, amelyek mostantól támogatják az olvasást és az írást is.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>További lépések

- További információ az [olvasott a kópiákról](concepts-read-replicas.md)
