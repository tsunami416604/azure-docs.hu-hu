---
title: Nagyobb verziófrissítés – Azure Portal-Azure Database for MySQL – egyetlen kiszolgáló
description: Ez a cikk azt ismerteti, hogyan frissíthető a Azure Database for MySQL-Single Server főverziója Azure Portal használatával
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 080d7c09b180d5943216793119718eb6a2add79e
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95753051"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>A főverzió frissítése Azure Database for MySQL egyetlen kiszolgálón a Azure Portal használatával

> [!IMPORTANT]
> Az Azure Database for MySQL egyetlen kiszolgálójának főverziójának frissítése nyilvános előzetes verzióban érhető el.

Ez a cikk azt ismerteti, hogyan frissítheti a MySQL főverzióját helyben, Azure Database for MySQL egyetlen kiszolgálón.

Ez a funkció lehetővé teszi, hogy az ügyfelek a MySQL 5,6-kiszolgálókat a MySQL 5,7-re helyben, az adatmozgatás nélkül, vagy az alkalmazás-kapcsolódási sztringek megváltozásának szükségességére kattintva végezzék.

> [!Note]
> * A főverzió frissítése csak a MySQL 5,6-ből a MySQL 5,7-re való frissítés főverziójának frissítése esetén érhető el<br>
> * A főverzió frissítése még nem támogatott a replika-kiszolgálón.
> * A kiszolgáló a frissítési művelet során nem lesz elérhető. Ezért javasoljuk, hogy a tervezett karbantartási időszak alatt végezzen frissítéseket.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Egy [Azure Database for MySQL egyetlen kiszolgáló](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57"></a>Főverzió frissítésének végrehajtása a MySQL 5,6-ből a MySQL 5,7-be

Az alábbi lépéseket követve elvégezheti a MySQL 5,6-kiszolgáló Azure-adatbázisának verziófrissítését

> [!IMPORTANT]
> Javasoljuk, hogy először a kiszolgáló visszaállított példányán végezze el a frissítést, nem pedig közvetlenül az éles környezetben. Lásd: [az időponthoz tartozó visszaállítás végrehajtása](howto-restore-server-portal.md#point-in-time-restore). 

1. A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő Azure Database for MySQL 5,6-kiszolgálót.

2. Az **Áttekintés** lapon kattintson a **frissítés** gombra az eszköztáron.

3. A **frissítés** szakaszban kattintson az **OK** gombra az Azure Database for MySQL 5,6-kiszolgáló frissítéséhez a 5,7 Serverre.

    :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL – áttekintés – frissítés":::

4. Egy értesítés megerősíti, hogy a frissítés sikeres volt.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**1. Mikor lesz ez a verziófrissítés, mert a MySQL v 5.6-os verzióban az éles környezetben frissíteni kell a szolgáltatást?**

A szolgáltatáshoz tartozó GA a MySQL v 5.6-os nyugdíjazás előtt lett tervezve. Azonban a szolgáltatás készen áll a gyártásra, és az Azure teljes mértékben támogatja, így biztosnak kell lennie az Ön környezetében. Ajánlott eljárásként javasoljuk, hogy először futtassa és tesztelje a kiszolgáló visszaállított példányán, hogy a frissítés során megbecsülje a állásidőt, és az alkalmazás-kompatibilitási teszt végrehajtása előtt futtassa az alkalmazást az éles környezetben. További információ: az [időponthoz kapcsolódó visszaállítás végrehajtása](howto-restore-server-portal.md#point-in-time-restore) a kiszolgáló egy adott időpontban történő másolásának létrehozásához. 

**2. ez okozza a kiszolgáló leállását, és ha igen, mennyi ideig?**

Igen, a kiszolgáló a frissítési folyamat során nem lesz elérhető, ezért javasoljuk, hogy ezt a műveletet a tervezett karbantartási időszakban hajtsa végre. A becsült állásidő az adatbázis méretétől, a kiépített tárolási mérettől, valamint az adatbázis tábláinak számától függ. A frissítési idő közvetlenül arányos a kiszolgálón lévő táblák számával. Az alapszintű SKU-kiszolgálók frissítése várhatóan hosszabb időt vesz igénybe, mivel a standard szintű tárolási platformon van. A kiszolgáló-környezet leállásának becsléséhez azt javasoljuk, hogy először hajtsa végre a frissítést a kiszolgáló visszaállított példányán.  

**3. meg kell jegyezni, hogy a replika-kiszolgálón még nem támogatott. Mit jelent a konkrét?**

A replika-kiszolgálók jelenleg nem támogatják a főverziók frissítését, ami azt jelenti, hogy a replikálásban részt vevő kiszolgálókhoz nem kell futtatnia (forrás-vagy replika-kiszolgáló). Ha tesztelni szeretné a replikálásban részt vevő kiszolgálók frissítését, mielőtt hozzáadja a replika támogatását a frissítési funkcióhoz, a következő lépéseket javasoljuk:

1. A tervezett karbantartás során [állítsa le a replikálást, és törölje a replika kiszolgálót](howto-read-replicas-portal.md) a név és az összes konfigurációs információ (a tűzfalbeállítások, a kiszolgálói paraméterek konfigurálása, ha az eltér a forráskiszolgálón) rögzítése után.
2. A forráskiszolgáló frissítésének elvégzése.
3. Hozzon létre egy új olvasási replika-kiszolgálót az 1. lépésben rögzített névvel és konfigurációs beállításokkal. Az új replika-kiszolgáló automatikusan a v 5.7-es verzióra kerül, miután a forráskiszolgáló frissítve lett a v 5.7-es verziójára.

**4. mi történik, ha nem választjuk a MySQL v 5.6-kiszolgáló frissítését a 2021. február 5. előtt?**

Továbbra is futtathatja a MySQL v 5.6-kiszolgálót a korábban leírtak szerint. Az Azure **soha nem fogja** végrehajtani a kényszerített frissítést a kiszolgálón. A [Azure Database for MySQL verziószámozási szabályzatában](concepts-version-policy.md) ismertetett korlátozások azonban érvényesek lesznek.

## <a name="next-steps"></a>Következő lépések

További információ a [Azure Database for MySQL verziószámozási szabályzatáról](concepts-version-policy.md).
