---
title: Olvasási replikák kezelése az Azure Database for PostgreSQL – egyetlen kiszolgáló az Azure parancssori felületen
description: Ismerje meg, hogyan kezelheti a olvasható replikák az Azure Database for PostgreSQL – egyetlen kiszolgáló az Azure parancssori felületen.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/28/2019
ms.openlocfilehash: 9a6a1a744a8441d2f082d4d14a3aba8aa1cfc09e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66306022"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli"></a>Hozzon létre, és olvasási replikák kezelése az Azure parancssori felületen

Ebből a cikkből megismerheti, hogyan hozhat létre és kezelheti a olvasható replikák az Azure Database for postgresql-hez az Azure parancssori felületen. Olvasási replikák kapcsolatos további információkért tekintse meg a [áttekintése](concepts-read-replicas.md).

> [!IMPORTANT]
> Olvasási replikát hozhat létre, mint a fölérendelt kiszolgáló ugyanabban a régióban, vagy bármely más Azure-régióban a választott. Régiók közötti replikáció jelenleg nyilvános előzetes verzióban érhető el.

## <a name="prerequisites"></a>Előfeltételek
- Egy [, Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-up-azure-cli.md) kell a fölérendelt kiszolgáló.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 


## <a name="prepare-the-master-server"></a>A fölérendelt kiszolgáló előkészítése
Az általános célú és memóriahasználatra optimalizált szinten főkiszolgálóvá előkészítése ezeket a lépéseket kell használni.

A `azure.replication_support` paramétert állítsa **REPLIKA** a fölérendelt kiszolgálón. A statikus paraméter módosul, ha a kiszolgáló újraindítása szükség a módosítás érvénybe léptetéséhez.

1. Állítsa be `azure.replication_support` replikára.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. Indítsa újra a kiszolgálót, a módosítás alkalmazása.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

## <a name="create-a-read-replica"></a>Hozzon létre egy olvasható replika

A [az postgres server replika létrehozása](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) parancs paraméterei a következők:

| Beállítás | Példaérték | Leírás  |
| --- | --- | --- |
| resource-group | myResourceGroup |  Az erőforráscsoport, ahol a replikakiszolgálón létrejön.  |
| name | mydemoserver-replica | A létrehozott új replikakiszolgáló neve. |
| source-server | mydemoserver | Név vagy az erőforrás azonosítója a meglévő fölérendelt kiszolgáló a replikáláshoz. |

A CLI az alábbi példában a replika jön létre a fő ugyanabban a régióban.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Hozhat létre egy eltérő régióban replika olvasni, használja a `--location` paraméter. A CLI az alábbi példában az USA nyugati RÉGIÓJA hoz létre a replikát.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

Ha nem állított be a `azure.replication_support` paramétert **REPLIKA** az általános célú vagy Memóriaoptimalizált kiszolgáló fő és a kiszolgáló újraindul, hibaüzenetet kap. Hajtsa végre ezeket a lépéseket két, a replika létrehozása előtt.

A kiszolgáló ugyanazt a konfigurációt a master létrehoztak egy replikát. Replika létrehozása után több beállítások egymástól függetlenül lehet módosítani a főkiszolgálóról: számítási generáció, virtuális maggal, tárolási és biztonsági másolat megőrzési ideje. A tarifacsomag egymástól függetlenül is módosítható, vagy az alapszintű csomag kivételével.

> [!IMPORTANT]
> Egy fölérendelt kiszolgáló konfigurációs frissül az új értékekre, mielőtt frissíteni a replikát konfigurációt egyenlő vagy nagyobb értékre. Ez a művelet biztosítja, hogy a replika továbbra is a fő végzett módosítások.

## <a name="list-replicas"></a>Replikák listája
Megtekintheti a lista egy fölérendelt kiszolgáló replikával használatával [listáján az postgres server](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) parancsot.

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

## <a name="stop-replication-to-a-replica-server"></a>Az adatbázisreplika-kiszolgáló replikáció leállítása
Egy fölérendelt kiszolgáló és a egy olvasási replika közötti replikációt használatával állítsa le [az postgres server replika stop](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) parancsot.

Miután leállította egy fölérendelt kiszolgáló és a egy olvasási replika-replikáció, nem lehet visszavonni. Az olvasási replika egy önálló kiszolgáló, amely támogatja az olvasásokat és az írásokat válik. Az önálló kiszolgáló nem hajtható végre egy replika be újra.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

## <a name="delete-a-master-or-replica-server"></a>A master és a replika kiszolgáló törlése
A master és a replika kiszolgáló törléséhez használja a [az postgres server delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) parancsot.

Ha töröl egy fölérendelt kiszolgáló, a replikáció az összes olvasható replika le van állítva. Az olvasási replikák önálló kiszolgálók által mostantól támogatják az olvasásokat és az írásokat válnak.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>További lépések
Tudjon meg többet [olvassa el a replikákat az Azure Database for postgresql-hez](concepts-read-replicas.md).