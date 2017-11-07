---
title: "Biztonsági mentése és visszaállítása egy kiszolgálóhoz az Azure-adatbázis PostgreSQL |} Microsoft Docs"
description: "Útmutató: biztonsági mentése és visszaállítása egy kiszolgálóhoz az Azure-adatbázis a PostgreSQL az Azure parancssori felület használatával."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: 0cfce63b1523f939dc2d706dba771e56ce9ccd6c
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql-by-using-the-azure-cli"></a>Biztonsági mentése és visszaállítása egy kiszolgálóhoz az Azure-adatbázis PostgreSQL az Azure parancssori felület használatával

Egy kiszolgáló-adatbázis visszaállítása korábbi dátumra 35 nap 7 kiterjedő PostgreSQL Azure adatbázis használata.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató útmutató befejezéséhez lesz szüksége:
- Egy [PostgreSQL-kiszolgáló és adatbázis Azure-adatbázis](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> Telepítésekor és az Azure parancssori felület helyileg használja, ez az útmutató útmutató használatához Azure CLI 2.0-s vagy újabb verziója. Erősítse meg a verzió, az Azure CLI parancssorba írja be a következőt `az --version`. Telepítéséhez vagy frissítéséhez, lásd: [Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="backup-happens-automatically"></a>Automatikusan megtörténik a biztonsági mentés
Amikor PostgreSQL Azure-adatbázist használ, az adatbázis-szolgáltatás automatikusan teszi a szolgáltatás biztonsági másolatot, 5 percenként. 

Az alapszintű csomag a biztonsági mentések érhetők el 7 napban. Standard csomagra a biztonsági mentések érhetők el 35 napon. További információkért lásd: [Azure-adatbázis a tarifacsomagok PostgreSQL](concepts-service-tiers.md).

Az automatikus biztonsági mentési szolgáltatással a kiszolgáló és az adatbázisok visszaállítása korábbi dátumra, vagy mikor.

## <a name="restore-a-database-to-a-previous-point-in-time-by-using-the-azure-cli"></a>Adatbázis visszaállítása egy korábbi időpontra időben az Azure parancssori felület használatával
A kiszolgáló visszaállítása egy korábbi időpontra időben PostgreSQL Azure adatbázis használata. A visszaállított adatok másolását egy új kiszolgálóra, és a meglévő kiszolgáló marad, mert a. Például egy táblázat véletlenül megszakadása ma órakor, visszaállíthatja az idő előtt déltől. Ezt követően beolvasható a hiányzó táblázat és az adatokat a kiszolgáló visszaállított példányát. 

A kiszolgáló visszaállításához használja az Azure parancssori felület [az postgres kiszolgálójának visszaállítását](/cli/azure/postgres/server#restore) parancsot.

### <a name="run-the-restore-command"></a>A restore parancs futtatása

Állítsa vissza a kiszolgáló, az Azure CLI parancssorba írja be a következő parancsot:

```azurecli-interactive
az postgres server restore --resource-group myResourceGroup --name mypgserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mypgserver-20170401
```

A `az postgres server restore` parancs paraméterei a következők:
| Beállítás | Ajánlott érték | Leírás  |
| --- | --- | --- |
| Erőforráscsoport |  myResourceGroup |  Az erőforráscsoport, ahol a forráskiszolgáló található.  |
| név | mypgserver visszaállítása | A restore parancs által létrehozott új kiszolgáló neve. |
| visszaállítás--időpontban | 2017-04-13T13:59:00Z | Válasszon ki egy pontot időben történő visszaállításához. A dátum és idő a forráskiszolgáló biztonsági mentés megőrzési időn belül kell lennie. Dátum és idő ISO8601 formátumot használja. Például használhatja a saját helyi időzóna, például a `2017-04-13T05:59:00-08:00`. Használhatja a UTC Zulu formátumban, például `2017-04-13T13:59:00Z`. |
| forrás-kiszolgáló | mypgserver-20170401 | Név vagy azonosító a forráskiszolgáló visszaállítása. |

Időben egy korábbi állapotba szeretné visszaállítani egy kiszolgálót, létrejön egy új kiszolgálót. Az eredeti kiszolgáló és az idő megadott pontjáról adatbázisainak az új kiszolgálóra történő átmásolása.

A hely és a visszaállított kiszolgáló árképzési szint értékek továbbra is ugyanaz, mint az eredeti kiszolgálón. 

A `az postgres server restore` parancs szinkron. Miután visszaállította a kiszolgáló, segítségével azt újra meg kell ismételni a különböző ponton időben. 

A visszaállítási folyamat befejezése után keresse meg az új kiszolgálón, és győződjön meg arról, hogy az adatok visszaállítása, az elvártaknak megfelelően.

## <a name="next-steps"></a>Következő lépések
[Adatkapcsolattárak PostgreSQL Azure-adatbázis](concepts-connection-libraries.md)
