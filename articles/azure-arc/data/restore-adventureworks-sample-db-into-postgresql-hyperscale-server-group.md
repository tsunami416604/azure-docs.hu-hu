---
title: A AdventureWorks-mintaadatbázis visszaállítása az Azure arc-kompatibilis PostgreSQL-nagy kapacitású
description: A AdventureWorks-mintaadatbázis visszaállítása az Azure arc-kompatibilis PostgreSQL-nagy kapacitású
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 2b51c5ca2295671a30fa6c0aee8d313c4c333900
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935956"
---
# <a name="restore-the-adventureworks-sample-database-to-azure-arc-enabled-postgresql-hyperscale"></a>A AdventureWorks-mintaadatbázis visszaállítása az Azure arc-kompatibilis PostgreSQL-nagy kapacitású

A [AdventureWorks](/sql/samples/adventureworks-install-configure) az oktatóanyagokban használt OLTP-adatbázist és példákat tartalmazó mintaadatbázis. A Microsoft a [SQL Server Samples GitHub-tárház](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases)részeként biztosította és tartja karban.

Egy nyílt forráskódú projekt átalakította a AdventureWorks-adatbázist, hogy kompatibilis legyen az Azure arc-kompatibilis PostgreSQL-nagy kapacitású.
- [Eredeti projekt](https://github.com/lorint/AdventureWorks-for-Postgres)
- [Kövesse a projektben, amely előre átalakítja a CSV-fájlokat a PostgreSQL-sel való kompatibilitás érdekében](https://github.com/NorfolkDataSci/adventure-works-postgres)

Ez a dokumentum egy egyszerű folyamatot ismertet, amely lekéri a AdventureWorks-mintaadatbázis visszaállítását a PostgreSQL nagy kapacitású-kiszolgálói csoportba.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>A AdventureWorks biztonságimásolat-fájl letöltése

Töltse le a AdventureWorks. SQL fájlt a PostgreSQL nagy kapacitású Server Group-tárolóba. Ebben a példában a `kubectl exec` paranccsal távolról végrehajtjuk a parancsot a PostgreSQL nagy kapacitású Server Group tárolóban, hogy le lehessen tölteni a fájlt a tárolóba. Ezt a fájlt bármely elérhető helyről letöltheti `curl` . Ugyanezt a módszert akkor használja, ha a PostgreSQL nagy kapacitású Server Group tárolóban más adatbázisról is szeretne biztonsági másolatot készíteni a fájlokról. Ha a PostgreSQL nagy kapacitású Server Group tárolóban van, egyszerűen létrehozhatja az adatbázist, a sémát, és feltöltheti az adatokat.

A fájlok letöltéséhez futtassa a parancshoz hasonló parancsot, és a Futtatás előtt cserélje le a pod neve és a névtér neve értékét:

> [!NOTE]
>  A tárolónak az 443-es internetkapcsolattal kell rendelkeznie, hogy letöltse a fájlt a GitHubról.

> [!NOTE]
>  Használja a postgres nagy kapacitású-kiszolgálócsoport koordinátor csomópontjának Pod-nevét. Neve: <server group name> -0.  Ha nem biztos benne, hogy a pod neve fut, futtassa a parancsot `kubectl get pod`

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres  -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/master/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"

#Example:
#kubectl exec postgres02-0 -n arc -c postgres -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/master/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"
```

## <a name="step-2-restore-the-adventureworks-database"></a>2. lépés: a AdventureWorks-adatbázis visszaállítása

Hasonlóképpen futtathatja a kubectl exec parancsot is, hogy az adatbázis létrehozásához és betöltéséhez az psql CLI eszközt használja, amely a PostgreSQL nagy kapacitású-kiszolgálócsoport tárolójában található.

Futtassa az ehhez hasonló parancsot az üres adatbázis létrehozásához, először a pod neve és a névtér nevének értékét a futtatása előtt.

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'
```

Ezután futtasson egy olyan parancsot, amely így van, hogy a Futtatás előtt állítsa vissza az adatbázist a pod neve és a névtér neve értékének behelyettesítésével.

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql
```


> **Megjegyzés: nem fogja látni az Azure arc-kompatibilis PostgreSQL-nagy kapacitású futtatásának teljesítménybeli előnyeit, amíg ki nem bővíti a horizontális felskálázást, és a PostgreSQL nagy kapacitású-kiszolgálócsoport munkavégző csomópontjain átméretezi/elosztja az adatait/táblákat. Lásd a [javasolt következő lépéseket](#suggested-next-steps).**

## <a name="suggested-next-steps"></a>Javasolt következő lépések
- Olvassa el a Azure Database for PostgreSQL nagy kapacitású kapcsolatos fogalmakat és útmutatókat az adatok több PostgreSQL nagy kapacitású-csomóponton keresztüli terjesztéséhez, valamint a Azure Database for PostgreSQL nagy kapacitású teljesítményének kihasználása érdekében. :
    * [Csomópontok és táblák](../../postgresql/concepts-hyperscale-nodes.md)
    * [Alkalmazás típusának meghatározása](../../postgresql/concepts-hyperscale-app-type.md)
    * [Elosztási oszlop kiválasztása](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Tábla közös elhelyezése](../../postgresql/concepts-hyperscale-colocation.md)
    * [Táblák elosztása és módosítása](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Több-bérlős adatbázis tervezése](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Valós idejű elemzési irányítópult kialakítása](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

   > \* A fenti dokumentumokban hagyja ki a **bejelentkezett szakaszt a Azure Portalba**, & **hozzon létre egy Azure Database for PostgreSQL-nagy kapacitású (Citus)**. Implementálja az Azure arc üzembe helyezésének hátralévő lépéseit. Ezek a részek Azure Database for PostgreSQL az Azure-felhőben nagy kapacitású (Citus) jellemzőek, de a dokumentumok egyéb részei közvetlenül alkalmazhatók az Azure arc-kompatibilis PostgreSQL-nagy kapacitású.

- [A Azure Database for PostgreSQL nagy kapacitású-kiszolgálócsoport felskálázása](scale-out-postgresql-hyperscale-server-group.md)
