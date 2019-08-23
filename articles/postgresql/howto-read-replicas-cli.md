---
title: Azure Database for PostgreSQL – egyetlen kiszolgáló olvasási replikáinak kezelése az Azure CLI-vel
description: Ismerje meg, hogyan kezelheti az olvasási replikákat Azure Database for PostgreSQL egyetlen kiszolgálón az Azure CLI-vel.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 63a8acad3c393a4c4d9c6a3b6750f1f934dad43d
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907426"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli"></a>Olvasási replikák létrehozása és kezelése az Azure CLI-ből

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet olvasási replikákat Azure Database for PostgreSQL az Azure CLI-vel. Az olvasási replikákkal kapcsolatos további tudnivalókért tekintse meg [](concepts-read-replicas.md)az áttekintést.

> [!IMPORTANT]
> Létrehozhat egy olvasási replikát a főkiszolgálóval megegyező régióban, vagy bármely más, az Ön által választott Azure-régióban is. A régiók közötti replikáció jelenleg nyilvános előzetes verzióban érhető el.

## <a name="prerequisites"></a>Előfeltételek
- Egy [Azure Database for PostgreSQL kiszolgáló](quickstart-create-server-up-azure-cli.md) a főkiszolgálóként.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 


## <a name="prepare-the-master-server"></a>A fő kiszolgáló előkészítése
Ezeket a lépéseket kell használni a főkiszolgáló előkészítéséhez a általános célú vagy a memória optimalizált szintjein.

A `azure.replication_support` paramétert a főkiszolgálón lévő **replika** értékre kell beállítani. Ha a statikus paraméter módosul, a módosítás érvénybe léptetéséhez újra kell indítani a kiszolgálót.

1. A `azure.replication_support` replika értékre van állítva.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. A módosítás alkalmazásához indítsa újra a kiszolgálót.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

## <a name="create-a-read-replica"></a>Hozzon létre egy olvasható replika

Az az [postgres Server replika Create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) paranccsal a következő paramétereket kell megadni:

| Beállítás | Példaérték | Leírás  |
| --- | --- | --- |
| resource-group | myResourceGroup |  Az az erőforráscsoport, amelyben a replika-kiszolgáló létre lesz hozva.  |
| name | mydemoserver-replica | A létrehozott új replikakiszolgáló neve. |
| source-server | mydemoserver | A replikálni kívánt létező főkiszolgáló neve vagy erőforrás-azonosítója. |

Az alábbi CLI-példában a replika a főkiszolgálóval megegyező régióban jön létre.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Egy több régióból származó olvasási replika létrehozásához használja `--location` a paramétert. Az alábbi CLI-példa létrehozza a replikát az USA nyugati régiójában.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Ha többet szeretne megtudni arról, hogy mely régiókban hozhat létre replikát, látogasson el a [replika áttekintése című cikkben](concepts-read-replicas.md). 

Ha nem állította be a `azure.replication_support` paramétert egy általános célú vagy memóriára optimalizált főkiszolgálón lévő **replika** számára, és újraindítja a kiszolgálót, hibaüzenetet kap. A replika létrehozása előtt végezze el a két lépést.

A replika ugyanazokkal a számítási és tárolási beállításokkal jön létre, mint a főkiszolgáló. A replika létrehozása után több beállítás is módosítható a főkiszolgálótól függetlenül: számítási generáció, virtuális mag, tárterület és biztonsági mentési megőrzési időszak. Az árképzési szint külön is módosítható, kivéve az alapszintű csomagból vagy abból.

> [!IMPORTANT]
> A főkiszolgálói beállítás új értékre való frissítése előtt frissítse a replika beállításait egy egyenlő vagy nagyobb értékre. Ez a művelet segíti a replikát a főkiszolgálón végrehajtott bármilyen módosítással.

## <a name="list-replicas"></a>Replikák listázása
A főkiszolgáló replikáinak listáját az [az postgres Server replika List](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) parancs használatával tekintheti meg.

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

## <a name="stop-replication-to-a-replica-server"></a>Az adatbázisreplika-kiszolgáló replikáció leállítása
Az [az postgres Server replika leállítása](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) paranccsal leállíthatja a replikációt a főkiszolgáló és egy olvasási replika között.

Miután leállította a replikálást egy főkiszolgálóra és egy olvasási replikára, nem vonható vissza. Az olvasási replika önálló kiszolgáló lesz, amely támogatja az olvasást és az írást is. Az önálló kiszolgáló nem hozható létre újra replikába.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

## <a name="delete-a-master-or-replica-server"></a>Fő-vagy replika-kiszolgáló törlése
A fő-vagy a replika-kiszolgáló törléséhez használja az az [postgres Server delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) parancsot.

Főkiszolgáló törlésekor a rendszer leállítja a replikálást az összes olvasási replikára. Az olvasási replikák olyan önálló kiszolgálók lesznek, amelyek már támogatják az olvasást és az írást is.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>További lépések
* További információ [az olvasási replikákkal kapcsolatban Azure Database for PostgreSQL](concepts-read-replicas.md).
* Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat a Azure Portalban](howto-read-replicas-portal.md).