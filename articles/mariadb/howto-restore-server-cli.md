---
title: Biztonsági mentés és visszaállítás – Azure CLI – Azure Database for MariaDB
description: Megtudhatja, hogyan készíthet biztonsági mentést és visszaállítást Azure Database for MariaDB-kiszolgálóról az Azure CLI használatával.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 4564aff5e8fe2119a494af33e71ff927718646db
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765851"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mariadb-using-the-azure-cli"></a>Azure Database for MariaDB-kiszolgáló biztonsági mentése és visszaállítása az Azure CLI használatával

A visszaállítási funkciók engedélyezéséhez rendszeresen biztonsági mentést kell készíteni Azure Database for MariaDB-kiszolgálókról. A szolgáltatás használatával visszaállíthatja a kiszolgálót és az összes adatbázisát egy korábbi időpontra, egy új kiszolgálón.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

- Egy [Azure Database for MariaDB-kiszolgáló és-adatbázis](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ehhez az útmutatóhoz az Azure CLI 2,0-es vagy újabb verzióját kell használnia. A verzió megerősítéséhez az Azure CLI parancssorában írja be a következőt: `az --version`. A telepítéshez vagy a frissítéshez lásd: az [Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="set-backup-configuration"></a>Biztonsági mentési konfiguráció beállítása

Választhatja a kiszolgáló helyi redundáns biztonsági mentések vagy földrajzilag redundáns biztonsági másolatok konfigurálását a kiszolgáló létrehozásakor.

> [!NOTE]
> A kiszolgáló létrehozása után a redundancia, földrajzilag redundáns vagy helyileg redundáns, nem állítható be.
>

Amikor létrehoz egy kiszolgálót a `az mariadb server create` parancs használatával, a `--geo-redundant-backup` paraméter határozza meg a biztonsági mentési redundancia beállítást. Ha `Enabled`, a rendszer redundáns biztonsági mentéseket végez. Vagy ha `Disabled` helyileg redundáns biztonsági mentés készül.

A biztonsági mentés megőrzési időtartamát a `--backup-retention`paraméter állítja be.

További információ ezekről az értékekről a létrehozás során: [Azure Database for MariaDB Server CLI](quickstart-create-mariadb-server-database-using-azure-cli.md)rövid útmutató.

A kiszolgáló biztonsági mentési megőrzési időszaka a következőképpen módosítható:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

Az előző példa a mydemoserver biztonsági mentés megőrzési időtartamát 10 napra módosítja.

A biztonsági másolatok megőrzési időszaka azt szabályozza, hogy az adott időpontra visszamenőleges visszaállítás hogyan kérhető le, mert az elérhető biztonsági másolatokon alapul. Az időponthoz való visszaállítás a következő szakaszban olvasható.

## <a name="server-point-in-time-restore"></a>Kiszolgáló időpontjának visszaállítása

A kiszolgálót visszaállíthatja egy korábbi időpontra. A visszaállított adatfájlokat egy új kiszolgálóra másolja a rendszer, és a meglévő kiszolgáló a következő marad:. Ha például egy táblát a mai napig véletlenül dobják el, a visszaállíthatja azt az időpontot megelőzően. Ezután lekérheti a hiányzó táblát és az adatait a kiszolgáló visszaállított példányáról.

A-kiszolgáló visszaállításához használja az Azure CLI az [MariaDB Server Restore](/cli/azure/mariadb/server#az-mariadb-server-restore) parancsot.

### <a name="run-the-restore-command"></a>A Restore parancs futtatása

A kiszolgáló visszaállításához az Azure CLI parancssorában adja meg a következő parancsot:

```azurecli-interactive
az mariadb server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

A `az mariadb server restore` parancshoz a következő paraméterek szükségesek:

| Beállítás | Ajánlott érték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az erőforráscsoport, amelyben a forráskiszolgáló található.  |
| név | mydemoserver-restored | A visszaállítási paranccsal létrehozott új kiszolgáló neve. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Válasszon ki egy időpontot a visszaállításhoz. Ennek a dátumnak és időnek a forráskiszolgáló biztonsági mentésének megőrzési időszakán belül kell lennie. Használja a ISO8601 dátum és idő formátumát. Használhatja például a saját helyi időzónáját, például `2018-03-13T05:59:00-08:00`. Az UTC Zulu formátumot is használhatja, például `2018-03-13T13:59:00Z`. |
| source-server | mydemoserver | A forráskiszolgáló neve vagy azonosítója, amelyről a visszaállítást végzi. |

WWhen egy kiszolgálót egy korábbi időpontra, létrejön egy új kiszolgáló. A rendszer átmásolja az eredeti kiszolgálót és a megadott időponthoz tartozó adatbázisait az új kiszolgálóra.

A visszaállított kiszolgáló helye és árképzési szintjei változatlanok maradnak az eredeti kiszolgálóval. 

A visszaállítási folyamat befejeződése után keresse meg az új kiszolgálót, és győződjön meg róla, hogy az Adathelyreállítás a várt módon történik. Az új kiszolgáló ugyanazzal a kiszolgáló-rendszergazdai bejelentkezési névvel és jelszóval rendelkezik, amely a visszaállítás megkezdésének időpontjában érvényes a meglévő kiszolgálóhoz. A jelszót az új kiszolgáló **áttekintő** oldaláról lehet megváltoztatni.

A visszaállítás során létrehozott új kiszolgáló nem rendelkezik az eredeti kiszolgálón található tűzfalszabályok vagy VNet szolgáltatás-végpontokkal. Ezeket a szabályokat külön kell beállítani az új kiszolgálóhoz.

## <a name="geo-restore"></a>Geo-visszaállítás

Ha a kiszolgálót földrajzilag redundáns biztonsági mentésre konfigurálta, akkor a rendszer létrehoz egy új kiszolgálót a meglévő kiszolgáló biztonsági másolatából. Ezt az új kiszolgálót bármely olyan régióban létre lehet hozni, amely Azure Database for MariaDB elérhető.  

Ha egy olyan kiszolgálót szeretne létrehozni, amely egy földrajzi redundáns biztonsági mentést használ, használja az Azure CLI `az mariadb server georestore` parancsot.

> [!NOTE]
> A kiszolgálók első létrehozásakor előfordulhat, hogy a Geo-visszaállításhoz nem lesz azonnal elérhető. A szükséges metaadatok feltöltése néhány órát is igénybe vehet.
>

A kiszolgáló geo-visszaállításához az Azure CLI parancssorában adja meg a következő parancsot:

```azurecli-interactive
az mariadb server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen5_8
```

Ez a parancs létrehoz egy új, *mydemoserver-georestored* nevű kiszolgálót az USA keleti régiójában, amely a *myresourcegroup*-hoz fog tartozni. Ez egy általános célú, Gen 5 kiszolgáló 8 virtuális mag. A kiszolgáló a *mydemoserver*a Geo-redundáns biztonsági másolatból jön létre, amely szintén az erőforráscsoport *myresourcegroup*

Ha az új kiszolgálót egy másik erőforráscsoporthoz szeretné létrehozni a meglévő kiszolgálóról, akkor a `--source-server` paraméterben az alábbi példában látható módon fogja minősíteni a kiszolgálónevet:

```azurecli-interactive
az mariadb server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMariaDB/servers/mydemoserver" --location eastus --sku-name GP_Gen5_8

```

A `az mariadb server georestore` parancshoz a következő paraméterek szükségesek:

| Beállítás | Ajánlott érték | Leírás  |
| --- | --- | --- |
|resource-group| myResourceGroup | Annak az erőforráscsoportnak a neve, amelyhez az új kiszolgáló tartozni fog.|
|név | mydemoserver – georestored | Az új kiszolgáló neve. |
|source-server | mydemoserver | Annak a meglévő kiszolgálónak a neve, amelynek a földrajzi redundáns biztonsági másolatait használja a rendszer. |
|location | eastus | Az új kiszolgáló helye. |
|sku-name| GP_Gen5_8 | Ez a paraméter beállítja az árképzési szintet, a számítási generációt és az új kiszolgáló virtuális mag számát. GP_Gen5_8 térképeket egy általános célú, Gen 5 Server 8 virtuális mag.|

Amikor új kiszolgálót hoz létre a Geo-visszaállítással, az örökli a forráskiszolgáló azonos tárolási méretét és díjszabási szintjét. Ezek az értékek nem módosíthatók a létrehozás során. Az új kiszolgáló létrehozása után a tárolási mérete méretezhető.

A visszaállítási folyamat befejeződése után keresse meg az új kiszolgálót, és győződjön meg róla, hogy az Adathelyreállítás a várt módon történik. Az új kiszolgáló ugyanazzal a kiszolgáló-rendszergazdai bejelentkezési névvel és jelszóval rendelkezik, amely a visszaállítás megkezdésének időpontjában érvényes a meglévő kiszolgálóhoz. A jelszót az új kiszolgáló **áttekintő** oldaláról lehet megváltoztatni.

A visszaállítás során létrehozott új kiszolgáló nem rendelkezik az eredeti kiszolgálón található tűzfalszabályok vagy VNet szolgáltatás-végpontokkal. Ezeket a szabályokat külön kell beállítani az új kiszolgálóhoz.

## <a name="next-steps"></a>Következő lépések

- További információ a szolgáltatás [biztonsági mentéséről](concepts-backup.md)
- A [replikák](concepts-read-replicas.md) megismerése
- További információ az [üzletmenet-folytonossági](concepts-business-continuity.md) lehetőségekről
