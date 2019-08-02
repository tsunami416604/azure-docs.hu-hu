---
title: Olvasási replikák létrehozása és kezelése a Azure Database for MariaDBban
description: Ez a cikk bemutatja, hogyan állíthatja be és kezelheti az olvasási replikákat Azure Database for MariaDB az Azure CLI használatával.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: ce68e3ae7728108126b2994a0e065ac84eb894bb
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68641928"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli"></a>Olvasási replikák létrehozása és kezelése az Azure Database for MariaDB az Azure CLI használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet olvasási replikákat ugyanabban az Azure-régióban, mint a Azure Database for MariaDB szolgáltatásban található főkiszolgálót az Azure CLI használatával.

> [!IMPORTANT]
> Létrehozhat egy olvasási replikát a főkiszolgálóval megegyező régióban, vagy bármely más, az Ön által választott Azure-régióban is. Az olvasási replikák (azonos régió és régiók) jelenleg nyilvános előzetes verzióban érhetők el.

## <a name="prerequisites"></a>Előfeltételek

- [Telepítse az Azure CLI 2.0-t.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Egy [Azure Database for MariaDB kiszolgáló](quickstart-create-mariadb-server-database-using-azure-portal.md) , amely főkiszolgálóként lesz felhasználva. 

> [!IMPORTANT]
> Az olvasási replika funkció csak a általános célú vagy a memória optimalizált árképzési szintjein Azure Database for MariaDB-kiszolgálókon érhető el. Győződjön meg, hogy a fölérendelt kiszolgáló árképzési szintek egyikét.

## <a name="create-a-read-replica"></a>Hozzon létre egy olvasható replika

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

Egy több régióból származó olvasási replika létrehozásához használja `--location` a paramétert. Az alábbi CLI-példa létrehozza a replikát az USA nyugati régiójában.

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> A kiszolgáló konfigurációval megegyező a fő olvasható replikák jönnek létre. A másodpéldány konfigurációjának a létrehozása után módosítható. Javasoljuk, hogy az adatbázisreplika-kiszolgáló konfigurációs kell tárolni annak érdekében, hogy a replika nem tudják tartani a főkiszolgálóval a főkiszolgáló-nál nagyobb vagy egyenlő értéken.

## <a name="stop-replication-to-a-replica-server"></a>Az adatbázisreplika-kiszolgáló replikáció leállítása

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

## <a name="delete-a-replica-server"></a>Adatbázisreplika-kiszolgáló törlése

Az olvasási replika kiszolgálójának törléséhez futtassa az az **[MariaDB Server delete](/cli/azure/mariadb/server)** parancsot.

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

## <a name="delete-a-master-server"></a>Egy fölérendelt kiszolgáló törlése

> [!IMPORTANT]
> Egy fölérendelt kiszolgáló törlése az összes replika kiszolgálók replikálását, és törli magát a főkiszolgáló. Replikakiszolgáló önálló kiszolgálók által mostantól támogatják az olvasási és írási műveletek válnak.

A főkiszolgálók törléséhez futtassa az az **[MariaDB Server delete](/cli/azure/mariadb/server)** parancsot.

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="list-replicas-for-a-master-server"></a>Egy fölérendelt kiszolgáló replikáit listája

Egy adott főkiszolgáló összes replika megtekintéséhez futtassa a következő parancsot: 

```azurecli-interactive
az mariadb server replica list --server-name mydemoserver --resource-group myresourcegroup
```

A `az mariadb server replica list` parancs paraméterei a következők:

| Beállítás | Példaérték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az erőforráscsoport, ahol az adatbázisreplika-kiszolgálót hoz létre.  |
| server-name | mydemoserver | A neve vagy a fölérendelt kiszolgáló azonosítója. |

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [replikák olvasása](concepts-read-replicas.md)
