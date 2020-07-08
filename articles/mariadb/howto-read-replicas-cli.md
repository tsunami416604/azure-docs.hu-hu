---
title: Olvasási replikák kezelése – Azure CLI, REST API-Azure Database for MariaDB
description: Ez a cikk bemutatja, hogyan állíthatja be és kezelheti az olvasási replikákat Azure Database for MariaDB az Azure CLI és a REST API használatával.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/10/2020
ms.openlocfilehash: ddcfea684a22c9ad06197086b3e74700df755da1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84707991"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli-and-rest-api"></a>Olvasási replikák létrehozása és kezelése a Azure Database for MariaDB az Azure CLI és a REST API használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet olvasási replikákat a Azure Database for MariaDB szolgáltatásban az Azure CLI és a REST API használatával.

## <a name="azure-cli"></a>Azure CLI
Az olvasási replikákat az Azure CLI használatával hozhatja létre és kezelheti.

### <a name="prerequisites"></a>Előfeltételek

- [Az Azure CLI 2,0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Egy [Azure Database for MariaDB kiszolgáló](quickstart-create-mariadb-server-database-using-azure-portal.md) , amely főkiszolgálóként lesz felhasználva. 

> [!IMPORTANT]
> Az olvasási replika funkció csak a általános célú vagy a memória optimalizált árképzési szintjein Azure Database for MariaDB-kiszolgálókon érhető el. Győződjön meg arról, hogy a főkiszolgáló a fenti díjszabási szintek egyikében van.

### <a name="create-a-read-replica"></a>Olvasási replika létrehozása

> [!IMPORTANT]
> Ha olyan mesteralakzathoz hoz létre replikát, amely nem rendelkezik meglévő replikákkal, a főkiszolgáló először újraindul, hogy felkészüljön a replikálásra. Ezt vegye figyelembe, és hajtsa végre ezeket a műveleteket egy leállási időszakon belül.

A következő paranccsal hozhat létre olvasási replika-kiszolgálót:

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

A `az mariadb server replica create` parancshoz a következő paraméterek szükségesek:

| Beállítás | Példaérték | Description  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az az erőforráscsoport, amelybe a replika-kiszolgáló létre lesz hozva.  |
| name | mydemoreplicaserver | A létrehozott új replika-kiszolgáló neve. |
| source-server | mydemoserver | A replikálni kívánt létező főkiszolgáló neve vagy azonosítója. |

Egy több régióból származó olvasási replika létrehozásához használja a `--location` paramétert. 

Az alábbi CLI-példa létrehozza a replikát az USA nyugati régiójában.

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Ha többet szeretne megtudni arról, hogy mely régiókban hozhat létre replikát, látogasson el a [replika áttekintése című cikkben](concepts-read-replicas.md). 

> [!NOTE]
> Az olvasási replikák ugyanazzal a kiszolgáló-konfigurációval jönnek létre, mint a főkiszolgáló. A replika-kiszolgáló konfigurációja a létrehozása után módosítható. Azt javasoljuk, hogy a replika-kiszolgáló konfigurációját a főkiszolgálónál egyenlő vagy nagyobb értékekkel kell megőrizni, hogy a replika képes legyen lépést tartani a főkiszolgálóval.

### <a name="list-replicas-for-a-master-server"></a>Főkiszolgáló replikáinak listázása

Egy adott főkiszolgáló összes replikájának megtekintéséhez futtassa a következő parancsot: 

```azurecli-interactive
az mariadb server replica list --server-name mydemoserver --resource-group myresourcegroup
```

A `az mariadb server replica list` parancshoz a következő paraméterek szükségesek:

| Beállítás | Példaérték | Description  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az az erőforráscsoport, amelybe a replika-kiszolgáló létre lesz hozva.  |
| server-name | mydemoserver | A főkiszolgáló neve vagy azonosítója. |

### <a name="stop-replication-to-a-replica-server"></a>Replikálás megszakítása egy másodpéldány-kiszolgálón

> [!IMPORTANT]
> A kiszolgálók replikálásának leállítása visszafordíthatatlan. Miután leállította a replikálást egy fő és egy replika között, nem vonható vissza. A replika-kiszolgáló ezután önálló kiszolgáló lesz, és már támogatja az olvasást és az írást is. Ez a kiszolgáló nem hozható létre újra replikába.

Az olvasási replika kiszolgálóra való replikálás a következő parancs használatával állítható le:

```azurecli-interactive
az mariadb server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

A `az mariadb server replica stop` parancshoz a következő paraméterek szükségesek:

| Beállítás | Példaérték | Description  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az erőforráscsoport, amelyben a replika-kiszolgáló létezik.  |
| name | mydemoreplicaserver | Annak a replika-kiszolgálónak a neve, amelyen a replikálást le kell állítani. |

### <a name="delete-a-replica-server"></a>Replika-kiszolgáló törlése

Az olvasási replika kiszolgálójának törléséhez futtassa az az **[MariaDB Server delete](/cli/azure/mariadb/server)** parancsot.

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-master-server"></a>Főkiszolgáló törlése

> [!IMPORTANT]
> A főkiszolgáló törlése leállítja a replikálást az összes replikakiszolgálón, magát a főkiszolgálót pedig törli. A replikakiszolgálókból különálló kiszolgálók lesznek, amelyek az olvasási és írási műveleteket egyaránt támogatják.

A főkiszolgálók törléséhez futtassa az az **[MariaDB Server delete](/cli/azure/mariadb/server)** parancsot.

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="rest-api"></a>REST API
Az olvasási replikákat az [Azure REST API](/rest/api/azure/)használatával hozhatja létre és kezelheti.

### <a name="create-a-read-replica"></a>Olvasási replika létrehozása
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

### <a name="stop-replication-to-a-replica-server"></a>Replikálás megszakítása egy másodpéldány-kiszolgálón
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

- További információ az [olvasási replikáról](concepts-read-replicas.md)
