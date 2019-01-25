---
title: Létrehozása és kezelése olvasható replikák az Azure Database for MySQL-hez
description: Ez a cikk ismerteti, hogyan beállítása és kezelése olvasható replikák az Azure Database for MySQL-hez az Azure CLI használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 01/23/2019
ms.openlocfilehash: 9992d93f689663e859315dd55d248866d88b4a25
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54903600"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli"></a>Létrehozása és kezelése olvasható replikák az Azure Database MySQL-hez az Azure CLI használatával

> [!IMPORTANT]
> Az olvasási replika funkció nyilvános előzetes verzióban érhető el.

Ebben a cikkben, megtudhatja, hogyan hozhat létre és kezelhet olvasható replikák az Azure Database for MySQL-szolgáltatás az Azure CLI-vel a fő Azure ugyanazon a régión belül.

## <a name="prerequisites"></a>Előfeltételek

- [Telepítse az Azure CLI 2.0-t.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Egy [, Azure Database for MySQL-kiszolgáló](quickstart-create-mysql-server-database-using-azure-portal.md) , amely a fölérendelt kiszolgáló lesz. 

> [!IMPORTANT]
> A olvasható replika funkció érhető csak az Azure Database for MySQL-kiszolgálók, az általános célú és memóriahasználatra optimalizált tarifacsomagok az. Győződjön meg, hogy a fölérendelt kiszolgáló árképzési szintek egyikét.

## <a name="create-a-read-replica"></a>Hozzon létre egy olvasható replika

Egy olvasási adatbázisreplika-kiszolgáló a következő parancs használatával hozható létre:

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

A `az mysql server replica create` parancs paraméterei a következők:

| Beállítás | Példaérték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az erőforráscsoport, ahol az adatbázisreplika-kiszolgálót hoz létre.  |
| név | mydemoreplicaserver | A létrehozott új replikakiszolgáló neve. |
| source-server | mydemoserver | A neve vagy azonosítója meglévő főkiszolgálójának a replikáláshoz. |

> [!NOTE]
> A kiszolgáló konfigurációval megegyező a fő olvasható replikák jönnek létre. A másodpéldány konfigurációjának a létrehozása után módosítható. Javasoljuk, hogy az adatbázisreplika-kiszolgáló konfigurációs kell tárolni annak érdekében, hogy a replika nem tudják tartani a főkiszolgálóval a főkiszolgáló-nál nagyobb vagy egyenlő értéken.

## <a name="stop-replication-to-a-replica-server"></a>Az adatbázisreplika-kiszolgáló replikáció leállítása

> [!IMPORTANT]
> A kiszolgáló replikációjának leállítása nem vonható vissza. Ha a replikáció leállt, a master és a replika között, nem lehet visszavonni. Az adatbázisreplika-kiszolgáló ezután lesz egy önálló kiszolgáló, és már támogatja az olvasási és írási műveletek. Ez a kiszolgáló nem hajtható végre egy replika be újra.

Replikáció egy olvasási adatbázisreplika-kiszolgálóhoz a következő paranccsal állítható le:

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

A `az mysql server replica stop` parancs paraméterei a következők:

| Beállítás | Példaérték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az erőforráscsoport, ahol az adatbázisreplika-kiszolgálón található.  |
| név | mydemoreplicaserver | Replikációleállítási az adatbázisreplika-kiszolgáló neve. |

## <a name="delete-a-replica-server"></a>Adatbázisreplika-kiszolgáló törlése

Ehhez futtassa egy olvasási adatbázisreplika-kiszolgáló törlése végezhető a **[az mysql server delete](/cli/azure/mysql/server#az_mysql_server_delete)** parancsot.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

## <a name="delete-a-master-server"></a>Egy fölérendelt kiszolgáló törlése

> [!IMPORTANT]
> Egy fölérendelt kiszolgáló törlése az összes replika kiszolgálók replikálását, és törli magát a főkiszolgáló. Replikakiszolgáló önálló kiszolgálók által mostantól támogatják az olvasási és írási műveletek válnak.

Egy fölérendelt kiszolgáló törléséhez futtassa a **[az mysql server delete](/cli/azure/mysql/server#az_mysql_server_delete)** parancsot.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="list-replicas-for-a-master-server"></a>Egy fölérendelt kiszolgáló replikáit listája

Egy adott főkiszolgáló összes replika megtekintéséhez futtassa a következő parancsot: 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

A `az mysql server replica list` parancs paraméterei a következők:

| Beállítás | Példaérték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az erőforráscsoport, ahol az adatbázisreplika-kiszolgálót hoz létre.  |
| server-name | mydemoserver | A neve vagy a fölérendelt kiszolgáló azonosítója. |

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [replikák olvasása](concepts-read-replicas.md)
