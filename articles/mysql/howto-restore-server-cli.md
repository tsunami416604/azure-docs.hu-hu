---
title: Biztonsági mentése és visszaállítása egy kiszolgálót az Azure Database for MySQL-hez
description: 'Útmutató: biztonsági mentése és visszaállítása egy kiszolgálót az Azure Database for MySQL-hez az Azure parancssori felület használatával.'
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/01/2018
ms.openlocfilehash: 801f6dddfb3aaea850d76c80d43de93181c3d41c
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54913478"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-cli"></a>Hogyan biztonsági mentése és visszaállítása egy kiszolgálót az Azure Database for MySQL-hez az Azure CLI használatával

## <a name="backup-happens-automatically"></a>Biztonsági mentés automatikusan történik
Azure Database for MySQL-kiszolgálók biztonsági mentése rendszeres visszaállítási szolgáltatások engedélyezése. Ezzel a funkcióval, előfordulhat, hogy állítsa vissza a kiszolgáló és az összes adatbázis egy korábbi-időponthoz, egy új kiszolgálón.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató végrehajtásához lesz szüksége:
- Egy [, Azure Database for MySQL-kiszolgáló és adatbázis](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> Ez az útmutató az Azure CLI 2.0-s vagy újabb verzió használatát igényli. Adja meg, hogy ellenőrizze a verziót, az Azure CLI-vel parancssorban `az --version`. Telepítéshez vagy frissítéshez olvassa [Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="set-backup-configuration"></a>Biztonsági mentési konfiguráció beállítása

A választást, hogy a kiszolgáló a helyileg redundáns biztonsági mentések vagy a georedundáns biztonsági mentések konfigurálása a kiszolgáló létrehozásakor választja ki. 

> [!NOTE]
> Miután a kiszolgáló akkor jön létre, milyen típusú redundancia rendelkezik, a helyileg redundáns, georedundáns és nem kapcsolható.
>

-N keresztül a kiszolgáló létrehozásakor a `az mysql server create` parancs, a `--geo-redundant-backup` paramétert úgy dönt, hogy a biztonsági mentés adatredundáns tárolási mód. Ha `Enabled`, georedundáns redundáns biztonsági másolatokat készít. Vagy ha `Disabled` helyileg redundáns biztonsági másolatokat készít. 

A biztonsági másolatok megőrzési időszak van beállítva, a paraméter által `--backup-retention`. 

Ezek az értékek beállítása a létrehozás során kapcsolatos további információkért lásd: a [, Azure Database for MySQL-kiszolgáló CLI rövid](quickstart-create-mysql-server-database-using-azure-cli.md).

A biztonsági másolat megőrzési idejének kiszolgáló a következőképpen módosíthatók:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

Az előző példában a biztonsági másolatok megőrzési időtartama mydemoserver 10 nap változik.

A biztonsági másolat megőrzési idejének milyen időben időponthoz visszaállítást is lekérdezhetők,-alapú biztonsági mentések érhető el, mivel szabályozza. Visszaállítási pont kötött erről a következő szakaszban.

## <a name="server-point-in-time-restore"></a>Kiszolgáló-időponthoz visszaállítása
A kiszolgáló szerinti visszaállíthatja egy korábbi időpontra. A visszaállított adatokat másolja egy új kiszolgálóra, és a meglévő kiszolgáló maradt-jébe. Például egy tábla már ma délben véletlenül megszakadása, visszaállíthatja az idő, noon előtt. Ezután letöltheti a hiányzó táblázat és az adatokat a kiszolgáló a visszaállított másolatból. 

A kiszolgáló visszaállításához az Azure CLI használatával [az mysql server restore](/cli/azure/mysql/server#az-mysql-server-restore) parancsot.

### <a name="run-the-restore-command"></a>A restore parancs futtatása

Állítsa vissza a kiszolgálót, az Azure CLI-vel parancssorba írja be a következő parancsot:

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

A `az mysql server restore` parancs paraméterei a következők:

| Beállítás | Ajánlott érték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az erőforráscsoport, amelyben a forráskiszolgáló található.  |
| név | mydemoserver-restored | A visszaállítási paranccsal létrehozott új kiszolgáló neve. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Válasszon ki egy pontot időben való visszaállításához. Ennek a dátumnak és időnek a forráskiszolgáló biztonsági mentésének megőrzési időszakán belül kell lennie. ISO8601 dátum és idő formátumot használja. Például használhatja a saját időzónájára, mint például `2018-03-13T05:59:00-08:00`. Például az UTC Zulu formátumot is használható `2018-03-13T13:59:00Z`. |
| source-server | mydemoserver | A forráskiszolgáló neve vagy azonosítója, amelyről a visszaállítást végzi. |

Időben egy korábbi időpontra való visszaállítása egy kiszolgálót, létrejön egy új kiszolgálóra. Az eredeti kiszolgálón és a hozzá tartozó adatbázisok a megadott időpontra a rendszer másolja az új kiszolgálóra.

A hely és a tarifacsomag-értékei a visszaállított kiszolgáló továbbra is ugyanaz, mint az eredeti kiszolgálón. 

A visszaállítási folyamat befejezése után keresse meg az új kiszolgálón, és győződjön meg arról, hogy az adatok helyreáll a várt módon.

## <a name="geo-restore"></a>GEO-visszaállítás
Ha konfigurálta a kiszolgáló georedundáns biztonsági mentésekhez, egy új kiszolgálót a biztonsági mentésből a meglévő kiszolgáló hozható létre. Az új kiszolgáló bármelyik régióban érhető el, hogy az Azure Database for MySQL hozható létre.  

Redundáns georedundáns biztonsági másolat segítségével hoz létre, használja az Azure CLI `az mysql server georestore` parancsot.

> [!NOTE]
> A kiszolgáló létrehozásakor azt nem lehet geo-visszaállítás azonnal elérhetővé válik. A szükséges metaadatok kell feltöltenie néhány órát vehet igénybe.
>

A geo-visszaállítás a kiszolgáló, az Azure CLI-vel parancssorba írja be a következő parancsot:

```azurecli-interactive
az mysql server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen4_8 
```
Ez a parancs létrehoz egy új kiszolgálót nevű *mydemoserver – georestored* fog tartozni, USA keleti régiójában *myresourcegroup*. Egy általános célú, 4. generációs kiszolgáló 8 virtuális maggal rendelkező. A kiszolgáló akkor jön létre, a georedundáns biztonsági mentéséből *mydemoserver*, ami egyben az erőforráscsoportban lévő *myresourcegroup*

Ha azt szeretné, majd hozzon létre egy másik erőforráscsoportban található, a meglévő kiszolgálóról egy, az új kiszolgálóra a a `--source-server` paraméter jogosultak lennének az a kiszolgáló nevét az alábbi példában látható módon:

```azurecli-interactive
az mysql server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMySQL/servers/mydemoserver" --location eastus --sku-name GP_Gen4_8

```

A `az mysql server georestore` parancs paraméterei a következők:

| Beállítás | Ajánlott érték | Leírás  |
| --- | --- | --- |
|resource-group| myResourceGroup | Az erőforráscsoport nevét az új kiszolgáló fog tartozni.|
|név | mydemoserver-georestored | Az új kiszolgáló neve. |
|source-server | mydemoserver | A meglévő kiszolgáló, amelynek földrajzi redundáns biztonsági mentések használhatók neve. |
|location | eastus | Az új kiszolgáló helyét. |
|sku-name| GP_Gen4_8 | Ez a paraméter beállítása az árképzési szint, számítási generáció és az új kiszolgáló virtuális magok számát. Egy általános célú, 4. generációs kiszolgáló 8 virtuális maggal rendelkező GP_Gen4_8 rendeli hozzá.|


>[!Important]
>Az új kiszolgáló létrehozása a geo-visszaállítás által, amikor örökli az azonos tárolási mérettel és tarifacsomagjának mint a forráskiszolgálónak. Ezeket az értékeket nem lehet módosítani a létrehozása során. Az új kiszolgáló létrehozása után a tároló mérete is vertikálisan fel.

A visszaállítási folyamat befejezése után keresse meg az új kiszolgálón, és győződjön meg arról, hogy az adatok helyreáll a várt módon.

## <a name="next-steps"></a>További lépések
- További információ a szolgáltatásról [biztonsági mentések](concepts-backup.md).
- Tudjon meg többet [üzletmenet-folytonossági](concepts-business-continuity.md) beállítások.
