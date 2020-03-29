---
title: Olvasási replikák kezelése - Azure CLI, REST API - Azure Database for MySQL
description: Ismerje meg, hogyan állíthatja be és kezelheti az olvasási replikákat az Azure Database for MySQL-ben az Azure CLI vagy REST API használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ed57003c7a9a5a1a9d87aa2e8934af8c48b1d819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063327"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli-and-rest-api"></a>Olvasási replikák létrehozása és kezelése a MySQL Azure Database-ben az Azure CLI és REST API használatával

Ebben a cikkben megtudhatja, hogyan hozhat létre és kezelhet olvasási replikákat az Azure Database for MySQL szolgáltatásban az Azure CLI és REST API használatával. Ha többet szeretne megtudni az olvasott replikákról, olvassa el az [áttekintést.](concepts-read-replicas.md)

## <a name="azure-cli"></a>Azure CLI
Az Azure CLI használatával olvasási replikákat hozhat létre és kezelhet.

### <a name="prerequisites"></a>Előfeltételek

- [Az Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Egy [Azure-adatbázis a MySQL-kiszolgálóhoz,](quickstart-create-mysql-server-database-using-azure-portal.md) amely főkiszolgálóként fog használni. 

> [!IMPORTANT]
> Az olvasási replika funkció csak az Azure Database for MySQL-kiszolgálók az általános célú vagy a memória optimalizált tarifacsomagok. Győződjön meg arról, hogy a főkiszolgáló a következő tarifacsomagok egyikében található.

### <a name="create-a-read-replica"></a>Olvasott kópia létrehozása

Az olvasási replikakiszolgáló a következő paranccsal hozható létre:

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

A `az mysql server replica create` parancshoz a következő paraméterek szükségesek:

| Beállítás | Példaérték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az az erőforráscsoport, ahová a replikakiszolgáló létre jön.  |
| név | mydemoreplicaserver | A létrehozott új replikakiszolgáló neve. |
| source-server | mydemoserver | A replikálandó meglévő főkiszolgáló neve vagy azonosítója. |

Régióközi olvasási replika `--location` létrehozásához használja a paramétert. Az alábbi CLI-példa létrehozza a replika az USA nyugati részén.

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Ha többet szeretne megtudni arról, hogy mely régiókban hozhat létre kópiát, olvassa el a [replikaolvasással kapcsolatos útmutatóról szóló cikket.](concepts-read-replicas.md) 

> [!NOTE]
> Az olvasási kópiák a főkiszolgálóval azonos kiszolgálókonfigurációval jönnek létre. A replikakiszolgáló konfigurációja a létrehozás után módosítható. Ajánlott, hogy a replikakiszolgáló konfigurációját a főkiszolgálóval azonos vagy nagyobb értékeken kell tartani annak biztosítása érdekében, hogy a replika lépést tud tartani a főkiszolgálóval.


### <a name="list-replicas-for-a-master-server"></a>Főkiszolgáló kópiáinak listázása

Egy adott főkiszolgáló összes replikájának megtekintéséhez futtassa a következő parancsot: 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

A `az mysql server replica list` parancshoz a következő paraméterek szükségesek:

| Beállítás | Példaérték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az az erőforráscsoport, ahová a replikakiszolgáló létre jön.  |
| server-name | mydemoserver | A főkiszolgáló neve vagy azonosítója. |

### <a name="stop-replication-to-a-replica-server"></a>A replikakiszolgálóra irányuló replikáció leállítása

> [!IMPORTANT]
> A kiszolgálóra irányuló replikáció leállítása visszafordíthatatlan. Ha a replikáció leállt a főkiszolgáló és a replika között, azt nem lehet visszavonni. A replikakiszolgáló ezután önálló kiszolgálóvá válik, és mostantól támogatja az olvasást és az írást is. Ez a kiszolgáló nem hozható elő újra kópiává.

Az olvasási replikakiszolgálóra történő replikáció a következő paranccsal leállítható:

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

A `az mysql server replica stop` parancshoz a következő paraméterek szükségesek:

| Beállítás | Példaérték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az az erőforráscsoport, ahol a replikakiszolgáló létezik.  |
| név | mydemoreplicaserver | A replikációt leállító replikakiszolgáló neve. |

### <a name="delete-a-replica-server"></a>Replikakiszolgáló törlése

Az olvasási replikakiszolgáló törlése az **[az mysql kiszolgáló törlési](/cli/azure/mysql/server)** parancsának futtatásával végezhető el.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-master-server"></a>Főkiszolgáló törlése

> [!IMPORTANT]
> A főkiszolgáló törlése leállítja a replikálást az összes replikakiszolgálón, magát a főkiszolgálót pedig törli. A replikakiszolgálókból különálló kiszolgálók lesznek, amelyek az olvasási és írási műveleteket egyaránt támogatják.

A főkiszolgáló törléséhez futtathatja az **[az mysql server delete](/cli/azure/mysql/server)** parancsot.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```


## <a name="rest-api"></a>REST API
Az [Azure REST API használatával](/rest/api/azure/)olvasási replikákat hozhat létre és kezelhet.

### <a name="create-a-read-replica"></a>Olvasott kópia létrehozása
Az [API-létrehozás imént](/rest/api/mysql/servers/create)létrehozhat egy olvasási replikát:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}"
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
A főkiszolgáló kópiáinak listáját a [replikalista API](/rest/api/mysql/replicas/listbyserver)használatával tekintheti meg:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>A replikakiszolgálóra irányuló replikáció leállítása
A főkiszolgáló és az olvasási kópia közötti replikációleállítható a [frissítési API használatával.](/rest/api/mysql/servers/update)

Miután leállította a replikációt a főkiszolgálóra és az olvasási replikára, azt nem lehet visszavonni. Az olvasási replika önálló kiszolgálóvá válik, amely támogatja az olvasást és az írást. Az önálló kiszolgáló nem alakítható újra kópiává.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>Fő- vagy replikakiszolgáló törlése
Fő- vagy replikakiszolgáló törléséhez használja a [delete API-t:](/rest/api/mysql/servers/delete)

Főkiszolgáló törlésekor az összes olvasási replikára történő replikáció leáll. Az olvasási replikák önálló kiszolgálókká válnak, amelyek mostantól támogatják az olvasást és az írást is.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>További lépések

- További információ az [olvasott a kópiákról](concepts-read-replicas.md)
