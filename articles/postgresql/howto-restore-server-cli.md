---
title: Biztonsági mentése és visszaállítása egy kiszolgálóhoz az Azure-adatbázis PostgreSQL |} Microsoft Docs
description: 'Útmutató: biztonsági mentése és visszaállítása egy kiszolgálóhoz az Azure-adatbázis a PostgreSQL az Azure parancssori felület használatával.'
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 04/01/2018
ms.openlocfilehash: 8ca129640db862f6031325279cc98c1e08dcef59
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-cli"></a>Készítsen biztonsági másolatot, és egy kiszolgálóhoz az Azure-adatbázis visszaállítása a PostgreSQL az Azure parancssori felület használatával

## <a name="backup-happens-automatically"></a>Automatikusan megtörténik a biztonsági mentés
PostgreSQL-kiszolgálók Azure-adatbázis biztonsági mentése rendszeresen visszaállítási szolgáltatások engedélyezése. Ezzel a szolgáltatással is visszaállíthatja a kiszolgáló és az adatbázisok egy korábbi-időpontban, egy új kiszolgálóra.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató útmutató befejezéséhez lesz szüksége:
- Egy [PostgreSQL-kiszolgáló és adatbázis Azure-adatbázis](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> Ez az útmutató útmutató az Azure parancssori felület 2.0 vagy újabb verzió használatát igényli. Erősítse meg a verzió, az Azure CLI parancssorba írja be a következőt `az --version`. Telepítéséhez vagy frissítéséhez, lásd: [Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="add-the-extension"></a>A bővítmény hozzáadása
A következő paranccsal adhatja hozzá a frissített Azure Database for PostgreSQL felügyeleti bővítményt:
```azurecli-interactive
az extension add --name rdbms
``` 

Ellenőrizze, hogy a bővítmény megfelelő verziója van-e telepítve. 
```azurecli-interactive
az extension list
```

A visszaküldött JSON-fájlnak a következőket kell tartalmaznia: 
```json
{
    "extensionType": "whl",
    "name": "rdbms",
    "version": "0.0.5"
}
```

Ha 0.0.5 verziója nem ad vissza, futtassa a következő a bővítmény frissítése: 
```azurecli-interactive
az extension update --name rdbms
```


## <a name="set-backup-configuration"></a>Biztonsági mentési konfigurációjának beállítása

Hogy a kiszolgáló, vagy helyileg redundáns vagy földrajzilag redundáns történő biztonsági mentésekre konfigurálása a kiszolgáló létrehozásakor választhat. 

> [!NOTE]
> Miután a kiszolgáló akkor jön létre, milyen típusú redundancia rendelkezik, helyileg redundáns földrajzilag redundáns vs nem állítható át.
>

A kiszolgálón keresztül létrehozása során a `az postgres server create` parancs, a `--geo-redundant-backup` paraméter úgy dönt, a biztonsági mentés redundancia beállítást. Ha `Enabled`, földrajzi redundancia biztonsági mentés készül. Vagy ha `Disabled` helyileg redundáns biztonsági mentés készül. 

A biztonsági mentés megőrzési idő van beállítva, a paraméter által `--backup-retention-days`. 

Ezeket az értékeket létrehozásakor beállításával kapcsolatos további információkért lásd: a [PostgreSQL-kiszolgáló CLI gyors üzembe helyezés az Azure Database](quickstart-create-server-database-azure-cli.md).

A kiszolgáló biztonsági másolatok megőrzésének időtartama módosíthatja az alábbiak szerint:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --backup-retention-days 10
```

Az előző példában a biztonsági másolatok megőrzésének időtartama mydemoserver 10 nap módosításait.

A biztonsági mentés megőrzési időszak irányító milyen távolságban az idő lekérhető pont időponthoz kötött visszaállítás, mivel elérhető biztonsági másolatokat alapul. A következő szakaszban leírt további pont időponthoz kötött visszaállítás.

## <a name="server-point-in-time-restore"></a>Kiszolgáló pont időponthoz kötött visszaállítás
A kiszolgálón visszaállíthatja egy korábbi időpontra időben. A visszaállított adatok másolását egy új kiszolgálóra, és a meglévő kiszolgáló marad, mert a. Például egy táblázat véletlenül megszakadása ma órakor, visszaállíthatja az idő előtt déltől. Ezt követően beolvasható a hiányzó táblázat és az adatokat a kiszolgáló visszaállított példányát. 

A kiszolgáló visszaállításához használja az Azure parancssori felület [az postgres kiszolgálójának visszaállítását](/cli/azure/postgres/server#az_postgres_server_restore) parancsot.

### <a name="run-the-restore-command"></a>A restore parancs futtatása

Állítsa vissza a kiszolgáló, az Azure CLI parancssorba írja be a következő parancsot:

```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

A `az postgres server restore` parancs paraméterei a következők:
| Beállítás | Ajánlott érték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az erőforráscsoport, ahol a forráskiszolgáló található.  |
| név | mydemoserver-restored | A visszaállítási paranccsal létrehozott új kiszolgáló neve. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Válasszon ki egy pontot időben történő visszaállításához. Ennek a dátumnak és időnek a forráskiszolgáló biztonsági mentésének megőrzési időszakán belül kell lennie. Dátum és idő ISO8601 formátumot használja. Például használhatja a saját helyi időzóna, például a `2018-03-13T05:59:00-08:00`. Használhatja a UTC Zulu formátumban, például `2018-03-13T13:59:00Z`. |
| source-server | mydemoserver | A forráskiszolgáló neve vagy azonosítója, amelyről a visszaállítást végzi. |

Időben egy korábbi állapotba szeretné visszaállítani egy kiszolgálót, létrejön egy új kiszolgálót. Az eredeti kiszolgáló és az idő megadott pontjáról adatbázisainak az új kiszolgálóra történő átmásolása.

A hely és a visszaállított kiszolgáló árképzési szint értékek továbbra is ugyanaz, mint az eredeti kiszolgálón. 

A visszaállítási folyamat befejezése után keresse meg az új kiszolgálón, és győződjön meg arról, hogy az adatok visszaállítása, az elvártaknak megfelelően.

## <a name="geo-restore"></a>Georedundáns helyreállítás
Ha a kiszolgáló a földrajzilag redundáns biztonsági mentéshez konfigurált, egy új kiszolgálót a biztonsági mentésből meglévő kiszolgáló hozhatók létre. Az új kiszolgáló is hozható létre bármely régióban, hogy rendelkezésre áll-e PostgreSQL az Azure-adatbázis.  

Földrajzi redundancia biztonsági segítségével kiszolgáló létrehozásához használja az Azure parancssori felület `az postgres server georestore` parancsot.

Georedundáns helyreállítás a kiszolgáló, az Azure CLI parancssorba írja be a következő parancsot:

```azurecli-interactive
az postgres server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen4_8 
```
Ez a parancs létrehoz egy új kiszolgálót nevű *mydemoserver-georestored* az USA keleti régiója fog tartozni *myresourcegroup*. Általános célú, 8 vCores generációs 4 kiszolgáló. A kiszolgáló akkor jön létre, a georedundáns biztonsági másolatból *mydemoserver*, ami egyben az erőforráscsoportban *myresourcegroup*

Ha szeretne létrehozni az új kiszolgáló egy másik erőforráscsoportban található a meglévő kiszolgálóról egy, majd a `--source-server` paraméter a kiszolgáló nevét az alábbi példában látható módon jogosult lenne:

```azurecli-interactive
az postgres server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver" --location eastus --sku-name GP_Gen4_8

```

A `az postgres server georestore` parancs requies a következő paraméterekkel:
| Beállítás | Ajánlott érték | Leírás  |
| --- | --- | --- |
|resource-group| myResourceGroup | Az erőforráscsoport nevét az új kiszolgálón fog tartozni.|
|név | mydemoserver-georestored | Az új kiszolgáló nevét. |
|source-server | mydemoserver | A meglévő kiszolgáló, amelynek földrajzi redundancia biztonsági mentések használhatók neve. |
|location | eastus | Az új kiszolgáló helyét. |
|Termékváltozat| GP_Gen4_8 | Ez a paraméter állandóként állítja be, az árképzési szint, számítási létrehozásának és az új kiszolgáló vCores száma. GP_Gen4_8 egy általános célú, a 8 vCores generációs 4 kiszolgáló van leképezve.|


>[!Important]
>Új kiszolgáló egy földrajzi visszaállítás létrehozásakor örökli a ugyanolyan méretű és tarifacsomag mint a forráskiszolgálónak. Ezek az értékek létrehozása közben nem módosítható. Az új kiszolgáló létrehozását követően akár mérete is méretezhető.

A visszaállítási folyamat befejezése után keresse meg az új kiszolgálón, és győződjön meg arról, hogy az adatok visszaállítása, az elvártaknak megfelelően.

## <a name="next-steps"></a>További lépések
- További információ a szolgáltatás [biztonsági mentések](concepts-backup.md).
- További információ [az üzletmenet folytonossága](concepts-business-continuity.md) beállítások.
