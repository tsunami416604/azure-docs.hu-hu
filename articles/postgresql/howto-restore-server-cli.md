---
title: Biztonsági mentés és visszaállítás - Azure CLI - Azure Database for PostgreSQL - Single Server
description: Ismerje meg, hogyan állíthat be biztonsági mentési konfigurációkat, és állíthatja vissza a kiszolgálót az Azure Database for PostgreSQL – Single Server az Azure CLI használatával.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: f0ea24133d7b6acdc4b099ee21a8711a2d99095d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74775704"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Kiszolgáló biztonsági mentése és visszaállítása az Azure Database for PostgreSQL szolgáltatásban – Egyetlen kiszolgáló az Azure CLI használatával

A PostgreSQL-kiszolgálókhoz készült Azure Database rendszeres időközönként biztonsági mentést tartalmaz a visszaállítási funkciók engedélyezéséhez. Ezzel a szolgáltatással visszaállíthatja a kiszolgálót és annak adatbázisait egy korábbi időpontra, egy új kiszolgálóra.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató befejezéséhez a következőkre van szükség:
- [Egy Azure-adatbázis postgreSQL-kiszolgálóhoz és adatbázishoz](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> Ez az útmutató megköveteli, hogy az Azure CLI 2.0-s vagy újabb verzióját használja. A verzió megerősítéséhez írja be az Azure `az --version`CLI parancssorába a . A telepítéshez vagy frissítéshez olvassa [el az Azure CLI telepítése]( /cli/azure/install-azure-cli)című témakört.

## <a name="set-backup-configuration"></a>Biztonsági másolat konfigurációjának beállítása

A kiszolgáló helyiredundáns biztonsági mentések vagy földrajzilag redundáns biztonsági mentések konfigurálása között választhat a kiszolgáló létrehozásakor. 

> [!NOTE]
> A kiszolgáló létrehozása után a redundancia, földrajzilag redundáns vs helyileg redundáns, nem lehet váltani.
>

A kiszolgáló `az postgres server create` parancson keresztüli `--geo-redundant-backup` létrehozásakor a paraméter a biztonsági mentésredundanci abeállítását határozza meg. Ha `Enabled`georedundáns biztonsági mentések készülnek. Vagy `Disabled` ha helyileg redundáns biztonsági mentések készülnek. 

A biztonsági mentés megőrzési `--backup-retention-days`időszakát a paraméter állítja be. 

A létrehozás során ezeknek az értékeknek a beállításáról az [Azure Database for PostgreSQL server CLI quickstart](quickstart-create-server-database-azure-cli.md)című témakörben talál további információt.

A kiszolgáló biztonsági mentési megőrzési ideje a következőképpen módosítható:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

Az előző példa a mydemoserver biztonsági mentési megőrzési időszakát 10 napra módosítja.

A biztonsági mentés megőrzési időszak határozza meg, hogy milyen messze vissza az időben egy időpontban vissza egy időpontban visszaállítási lehet letölteni, mivel ez a rendelkezésre álló biztonsági mentések alapján. Az időponthoz való időpontvisszaállítást a következő szakasz ismerteti.

## <a name="server-point-in-time-restore"></a>Kiszolgálói időponthoz való visszaállítás
Visszaállíthatja a kiszolgálót egy korábbi időpontra. A visszaállított adatokat a rendszer egy új kiszolgálóra másolja, és a meglévő kiszolgáló marad a jelenlegi állapotában. Ha például egy táblát véletlenül ma délben dobnak le, visszaállíthatja a dél előtti időt. Ezután lekérheti a hiányzó táblát és adatokat a kiszolgáló visszaállított példányából. 

A kiszolgáló visszaállításához használja az Azure CLI [az postgres server restore](/cli/azure/postgres/server) parancsot.

### <a name="run-the-restore-command"></a>A visszaállítási parancs futtatása

A kiszolgáló visszaállításához az Azure CLI parancssorába írja be a következő parancsot:

```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

A `az postgres server restore` parancshoz a következő paraméterek szükségesek:

| Beállítás | Ajánlott érték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az az erőforráscsoport, ahol a forráskiszolgáló létezik.  |
| név | mydemoserver-restored | A visszaállítási paranccsal létrehozott új kiszolgáló neve. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Jelölje ki azt az időpontot, amerre vissza szeretne állítani. Ennek a dátumnak és időnek a forráskiszolgáló biztonsági mentésének megőrzési időszakán belül kell lennie. Használja az ISO8601 dátum- és időformátumot. Használhatja például a saját helyi időzónáját, például `2018-03-13T05:59:00-08:00`a . Használhatja az UTC Zulu formátumot `2018-03-13T13:59:00Z`is, például . |
| source-server | mydemoserver | A forráskiszolgáló neve vagy azonosítója, amelyről a visszaállítást végzi. |

Amikor egy kiszolgálót egy korábbi időpontra állít vissza, új kiszolgáló jön létre. Az eredeti kiszolgáló és adatbázisai a megadott időpontból az új kiszolgálóra kerülnek.

A visszaállított kiszolgáló hely- és tarifacsomag-értékei megegyeznek az eredeti kiszolgálóval. 

A visszaállítási folyamat befejezése után keresse meg az új kiszolgálót, és ellenőrizze, hogy az adatok a várt módon vannak-e visszaállítva. Az új kiszolgáló nak ugyanaz a kiszolgálórendszergazdai bejelentkezési neve és jelszava van, mint amely a visszaállítás indításakor érvényes volt a meglévő kiszolgálón. A jelszó az új kiszolgáló **áttekintő** lapjáról módosítható.

A visszaállítás során létrehozott új kiszolgáló nem rendelkezik az eredeti kiszolgálón létező tűzfalszabályokkal vagy virtuális hálózatszolgáltatás-végpontokkal. Ezeket a szabályokat külön kell beállítani ehhez az új kiszolgálóhoz.

## <a name="geo-restore"></a>Geo-visszaállítás
Ha a kiszolgálót földrajzilag redundáns biztonsági másolatokra állította be, a meglévő kiszolgáló biztonsági másolatából új kiszolgáló hozható létre. Ez az új kiszolgáló bármely olyan régióban létrehozható, amelyen az Azure Database for PostgreSQL elérhető.  

Ha georedundáns biztonsági másolathasználatával szeretne kiszolgálót létrehozni, használja az Azure CLI `az postgres server georestore` parancsot.

> [!NOTE]
> A kiszolgáló első létrehozásakor előfordulhat, hogy nem érhető el azonnal a geo-visszaállításhoz. A szükséges metaadatok feltöltése néhány órát is igénybe vehet.
>

A kiszolgáló földrajzi visszaállításához az Azure CLI parancssorába írja be a következő parancsot:

```azurecli-interactive
az postgres server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen4_8 
```
Ez a parancs létrehoz egy új szervert nevű *mydemoserver-georestored* az USA keleti részén, hogy fog tartozni *myresourcegroup*. Ez egy általános célú, Gen 4 szerver 8 virtuális maggal. A kiszolgáló a *mydemoserver*georedundáns biztonsági másolatából jön létre, amely szintén a *myresourcegroup* erőforráscsoport erőforráscsoportjában található

Ha az új kiszolgálót a meglévő kiszolgálótól eltérő erőforráscsoportban `--source-server` szeretné létrehozni, akkor a paraméterben a kiszolgáló nevét a következő példában szereplőnek minősíti:

```azurecli-interactive
az postgres server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver" --location eastus --sku-name GP_Gen4_8

```

A `az postgres server georestore` parancshoz a következő paraméterek szükségesek:

| Beállítás | Ajánlott érték | Leírás  |
| --- | --- | --- |
|resource-group| myResourceGroup | Annak az erőforráscsoportnak a neve, amelyhez az új kiszolgáló tartozni.|
|név | mydemoserver-georestored | Az új kiszolgáló neve. |
|source-server | mydemoserver | Annak a meglévő kiszolgálónak a neve, amelynek georedundáns biztonsági másolatait használják. |
|location | eastus | Az új kiszolgáló helye. |
|sku-name| GP_Gen4_8 | Ez a paraméter beállítja az új kiszolgáló tarifacsomagját, a számítási létrehozást és a virtuális magok számát. GP_Gen4_8 leképezi egy általános célú, Gen 4 kiszolgáló8 virtuális maggal.|

Amikor egy geo-visszaállítással új kiszolgálót hoz létre, ugyanazt a tárolási méretet és tarifacsomagot örökli, mint a forráskiszolgáló. Ezek az értékek nem módosíthatók a létrehozás során. Az új kiszolgáló létrehozása után a tároló mérete felskálázható.

A visszaállítási folyamat befejezése után keresse meg az új kiszolgálót, és ellenőrizze, hogy az adatok a várt módon vannak-e visszaállítva. Az új kiszolgáló nak ugyanaz a kiszolgálórendszergazdai bejelentkezési neve és jelszava van, mint amely a visszaállítás indításakor érvényes volt a meglévő kiszolgálón. A jelszó az új kiszolgáló **áttekintő** lapjáról módosítható.

A visszaállítás során létrehozott új kiszolgáló nem rendelkezik az eredeti kiszolgálón létező tűzfalszabályokkal vagy virtuális hálózatszolgáltatás-végpontokkal. Ezeket a szabályokat külön kell beállítani ehhez az új kiszolgálóhoz.

## <a name="next-steps"></a>További lépések
- További információ a szolgáltatás [biztonsági másolatairól](concepts-backup.md)
- További információ a [replikákról](concepts-read-replicas.md)
- További információ az [üzletmenet-folytonossági](concepts-business-continuity.md) lehetőségekről
