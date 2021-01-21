---
title: Főverzió frissítése Azure Database for MySQL – egyetlen kiszolgálón
description: Ez a cikk azt ismerteti, hogyan frissíthető a Azure Database for MySQL-Single Server főverziója
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 1/13/2021
ms.openlocfilehash: b62f4ebc61ac27478788d8b2bae5e4145f87ac8b
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630200"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server"></a>Főverzió frissítése Azure Database for MySQL egyetlen kiszolgálón

> [!IMPORTANT]
> Az Azure Database for MySQL egyetlen kiszolgálójának főverziójának frissítése nyilvános előzetes verzióban érhető el.

Ez a cikk azt ismerteti, hogyan frissítheti a MySQL főverzióját helyben, Azure Database for MySQL egyetlen kiszolgálón.

Ez a funkció lehetővé teszi, hogy az ügyfelek a MySQL 5,6-kiszolgálókat a MySQL 5,7-re helyben, az adatmozgatás nélkül, vagy az alkalmazás-kapcsolódási sztringek megváltozásának szükségességére kattintva végezzék.

> [!Note]
> * A főverzió frissítése csak a MySQL 5,6-ből a MySQL 5,7-re való frissítés főverziójának frissítése esetén érhető el<br>
> * A főverzió frissítése még nem támogatott a replika-kiszolgálón.
> * A kiszolgáló a frissítési művelet során nem lesz elérhető. Ezért javasoljuk, hogy a tervezett karbantartási időszak alatt végezzen frissítéseket.

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal"></a>Főverzió frissítése a MySQL 5,6-ből a MySQL 5,7-re Azure Portal használatával

Az alábbi lépéseket követve elvégezheti a MySQL 5,6-kiszolgáló Azure-adatbázisának nagyobb verziófrissítését Azure Portal

> [!IMPORTANT]
> Javasoljuk, hogy először a kiszolgáló visszaállított példányán végezze el a frissítést, nem pedig közvetlenül az éles környezetben. Lásd: [az időponthoz tartozó visszaállítás végrehajtása](howto-restore-server-portal.md#point-in-time-restore).

1. A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő Azure Database for MySQL 5,6-kiszolgálót.

2. Az **Áttekintés** lapon kattintson a **frissítés** gombra az eszköztáron.

3. A **frissítés** szakaszban kattintson az **OK** gombra az Azure Database for MySQL 5,6-kiszolgáló frissítéséhez a 5,7 Serverre.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL – áttekintés – frissítés":::

4. Egy értesítés megerősíti, hogy a frissítés sikeres volt.


## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-cli"></a>Nagyobb verziófrissítés a MySQL 5,6-ről a MySQL 5,7-re az Azure CLI használatával

Az alábbi lépéseket követve elvégezheti a MySQL 5,6-kiszolgáló Azure-adatbázisának verziófrissítését az Azure CLI használatával

> [!IMPORTANT]
> Javasoljuk, hogy először a kiszolgáló visszaállított példányán végezze el a frissítést, nem pedig közvetlenül az éles környezetben. Lásd: [az időponthoz tartozó visszaállítás végrehajtása](howto-restore-server-cli.md#server-point-in-time-restore).

1. Telepítse az [Azure CLI-t a Windowshoz](/cli/azure/install-azure-cli) , vagy használja az Azure CLI-t a [Azure Cloud Shell](../cloud-shell/overview.md) a frissítési parancsok futtatásához. 
 
   Ehhez a frissítéshez az Azure CLI 2.16.0 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van. Futtassa az az version parancsot a telepített verzió és a függő kódtárak megkereséséhez. A legújabb verzióra az az upgrade paranccsal frissíthet.

2. A bejelentkezés után futtassa az az [MySQL Server upgrade](https://docs.microsoft.com/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_upgrade&preserve-view=true) parancsot:
    
   ```azurecli
   az mysql server upgrade --name testsvr --resource-group testgroup --subscription MySubscription --target-server-version 5.7"
   ```
   
   A parancssorban megjelenik a "-Running" üzenet. Miután ez az üzenet már nem jelenik meg, a verziófrissítés befejeződött.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="when-will-this-upgrade-feature-be-ga-as-we-have-mysql-v56-in-our-production-environment-that-we-need-to-upgrade"></a>Mikor fog ez a frissítés a GA verzióban, mivel a MySQL v 5.6 üzemi környezetben frissíteni kell a szolgáltatást?

A szolgáltatáshoz tartozó GA a MySQL v 5.6-os nyugdíjazás előtt lett tervezve. Azonban a szolgáltatás készen áll a gyártásra, és az Azure teljes mértékben támogatja, így biztosnak kell lennie az Ön környezetében. Ajánlott eljárásként javasoljuk, hogy először futtassa és tesztelje a kiszolgáló visszaállított példányán, hogy a frissítés során megbecsülje a állásidőt, és az alkalmazás-kompatibilitási teszt végrehajtása előtt futtassa az alkalmazást az éles környezetben. További információ: az [időponthoz kapcsolódó visszaállítás végrehajtása](howto-restore-server-portal.md#point-in-time-restore) a kiszolgáló egy adott időpontban történő másolásának létrehozásához. 

### <a name="will-this-cause-downtime-of-the-server-and-if-so-how-long"></a>A kiszolgáló leállását okozhatja, és ha igen, mennyi ideig tart?

Igen, a kiszolgáló a frissítési folyamat során nem lesz elérhető, ezért javasoljuk, hogy ezt a műveletet a tervezett karbantartási időszakban hajtsa végre. A becsült állásidő az adatbázis méretétől, a kiépített tárolási mérettől, valamint az adatbázis tábláinak számától függ. A frissítési idő közvetlenül arányos a kiszolgálón lévő táblák számával. Az alapszintű SKU-kiszolgálók frissítése várhatóan hosszabb időt vesz igénybe, mivel a standard szintű tárolási platformon van. A kiszolgáló-környezet leállásának becsléséhez azt javasoljuk, hogy először hajtsa végre a frissítést a kiszolgáló visszaállított példányán.  

### <a name="it-is-noted-that-it-is-not-supported-on-replica-server-yet-what-does-that-mean-concrete"></a>Meg kell jegyezni, hogy a replika-kiszolgálón még nem támogatott. Mit jelent a konkrét?

A replika-kiszolgálók jelenleg nem támogatják a főverziók frissítését, ami azt jelenti, hogy a replikálásban részt vevő kiszolgálókhoz nem kell futtatnia (forrás-vagy replika-kiszolgáló). Ha tesztelni szeretné a replikálásban részt vevő kiszolgálók frissítését, mielőtt hozzáadja a replika támogatását a frissítési funkcióhoz, a következő lépéseket javasoljuk:

1. A tervezett karbantartás során [állítsa le a replikálást, és törölje a replika kiszolgálót](howto-read-replicas-portal.md) a név és az összes konfigurációs információ (a tűzfalbeállítások, a kiszolgálói paraméterek konfigurálása, ha az eltér a forráskiszolgálón) rögzítése után.
2. A forráskiszolgáló frissítésének elvégzése.
3. Hozzon létre egy új olvasási replika-kiszolgálót az 1. lépésben rögzített névvel és konfigurációs beállításokkal. Az új replika-kiszolgáló automatikusan a v 5.7-es verzióra kerül, miután a forráskiszolgáló frissítve lett a v 5.7-es verziójára.

### <a name="what-will-happen-if-we-do-not-choose-to-upgrade-our-mysql-v56-server-before-february-5-2021"></a>Mi történik, ha nem választjuk a MySQL v 5.6-kiszolgáló frissítését a 2021. február 5. előtt?

Továbbra is futtathatja a MySQL v 5.6-kiszolgálót a korábban leírtak szerint. Az Azure **soha nem fogja** végrehajtani a kényszerített frissítést a kiszolgálón. A [Azure Database for MySQL verziószámozási szabályzatában](concepts-version-policy.md) ismertetett korlátozások azonban érvényesek lesznek.

## <a name="next-steps"></a>Következő lépések

További információ a [Azure Database for MySQL verziószámozási szabályzatáról](concepts-version-policy.md).
