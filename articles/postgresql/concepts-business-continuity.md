---
title: Üzletmenet-folytonosság – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk az üzletmenet folytonosságát (az időponthoz való visszaállítást, az adatközpont-kimaradást, a Geo-visszaállítást és a replikákat) ismerteti Azure Database for PostgreSQL használatakor.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/07/2020
ms.openlocfilehash: 75cd86bd1587a9294caef00efdf973fe8a26c150
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89612012"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>Az üzletmenet folytonosságának áttekintése Azure Database for PostgreSQL – egyetlen kiszolgálóval

Ez az Áttekintés ismerteti azokat a képességeket, amelyeket a Azure Database for PostgreSQL az üzletmenet folytonosságát és a vész-helyreállítást biztosítja. Megtudhatja, hogyan lehet helyreállítani az adatvesztést okozó, vagy az adatbázis és az alkalmazás elérhetetlenné válását okozó zavaró események helyreállításának lehetőségeit. Megtudhatja, mi a teendő, ha egy felhasználói vagy alkalmazáshiba hatással van az adatok integritására, egy Azure-régió leáll, vagy az alkalmazás karbantartást igényel.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Az üzletmenet folytonosságának biztosítására használható funkciók

A Azure Database for PostgreSQL olyan üzletmenet-folytonossági funkciókat biztosít, amelyek automatikus biztonsági mentést tartalmaznak, és lehetővé teszi a felhasználók számára a Geo-visszaállítás kezdeményezését. Mindegyik különböző tulajdonságokkal rendelkezik a becsült helyreállítási idő (ERT) és a lehetséges adatvesztés tekintetében. A becsült helyreállítási idő (ERT) a becsült időtartam ahhoz, hogy az adatbázis teljesen működőképes legyen egy visszaállítási/feladatátvételi kérelem után. Ha megértette ezeket a lehetőségeket, választhat közülük, és együtt használhatja őket különböző forgatókönyvek esetén. Az üzletmenet-folytonossági terv kidolgozása során meg kell ismernie a maximális elfogadható időtartamot, mielőtt az alkalmazás teljesen helyreáll a zavaró esemény után – ez a helyreállítási idő célkitűzése (RTO). Meg kell ismernie a legutóbbi adatfrissítések (időintervallum) maximális mennyiségét is, ha az alkalmazás a zavaró esemény utáni helyreállítás során elveszíti az adatvesztést – ez a helyreállítási pont célkitűzése (RPO).

A következő táblázat összehasonlítja a rendelkezésre álló funkciók ERT-és RPO:

| **Képesség** | **Basic** | **általános célú** | **Memóriaoptimalizált** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Időponthoz kötött visszaállítás biztonsági másolatból | A megőrzési időtartamon belüli visszaállítási pontok | A megőrzési időtartamon belüli visszaállítási pontok | A megőrzési időtartamon belüli visszaállítási pontok |
| Geo-visszaállítás földrajzilag replikált biztonsági másolatokból | Nem támogatott | ERT < 12 h<br/>RPO < 1 óra | ERT < 12 h<br/>RPO < 1 óra |

Érdemes lehet az [olvasási replikákat](concepts-read-replicas.md)is használni.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Kiszolgáló helyreállítása felhasználói vagy alkalmazáshiba miatt

A szolgáltatás biztonsági másolatai segítségével helyreállíthat egy kiszolgálót a különböző zavaró eseményekről. Előfordulhat, hogy A felhasználók véletlenül törölhetnek bizonyos adatfájlokat, és véletlenül eldobják egy fontos táblát, vagy akár el is dobják a teljes adatbázist. Előfordulhat, hogy egy alkalmazás helytelenül írja felül a megfelelő adatmennyiséget az alkalmazás hibája miatt, és így tovább.

Elvégezheti egy **időponthoz való visszaállítást** , hogy a kiszolgálóról egy ismert jó időpontra készítsen másolatot. Az időpontnak a kiszolgálón beállított biztonsági mentési megőrzési időtartamon belül kell lennie. Miután az adatok vissza lettek állítva az új kiszolgálóra, lecserélheti az eredeti kiszolgálót az újonnan visszaállított kiszolgálóra, vagy átmásolhatja a szükséges adatait a visszaállított kiszolgálóról az eredeti kiszolgálóra.

> [!IMPORTANT]
> A törölt kiszolgálók **nem** állíthatók vissza. Ha törli a kiszolgálót, a kiszolgálóhoz tartozó összes adatbázis is törlődik, és nem állítható helyre. Az [Azure erőforrás-zárolás](../azure-resource-manager/management/lock-resources.md) segítségével megakadályozhatja a kiszolgáló véletlen törlését.

## <a name="recover-from-an-azure-data-center-outage"></a>Helyreállítás Azure-adatközpontból

Bár ritka, mégis előfordulhat, hogy valamelyik Azure-adatközpont leáll. Leállás esetén az üzleti megszakadást okoz, amely csak néhány percet vesz igénybe, de az elmúlt órákban is tarthat.

Az egyik lehetőség, hogy megvárja, amíg a kiszolgáló ismét online állapotba kerül, amikor az adatközpont kimaradása meghalad. Ez olyan alkalmazásokhoz használható, amelyek megengedhetik maguknak, hogy a kiszolgáló bizonyos ideig offline állapotba kerüljön, például egy fejlesztési környezetben. Ha egy adatközpont leáll, nem tudja, mennyi ideig tarthat a leállás, így ez a lehetőség csak akkor működik, ha egy ideig nincs szüksége a kiszolgálóra.

## <a name="geo-restore"></a>Georedundáns visszaállítás

A Geo-visszaállítási szolgáltatás visszaállítja a kiszolgálót a Geo-redundáns biztonsági másolatok használatával. A biztonsági mentéseket a kiszolgáló [párosított régiójában](../best-practices-availability-paired-regions.md)tárolja a rendszer. Ezeket a biztonsági másolatokból bármely más régióba visszaállíthatja. A Geo-visszaállítás egy új kiszolgálót hoz létre a biztonsági másolatokból származó adatokkal. További információ a Geo-visszaállításról a [biztonsági mentési és visszaállítási fogalmakról szóló cikkből](concepts-backup.md).

> [!IMPORTANT]
> A Geo-visszaállítás csak akkor lehetséges, ha a kiszolgálót geo-redundáns biztonsági mentési tárolóval kiépített. Ha szeretné, hogy a helyileg redundáns biztonsági mentést a meglévő kiszolgálókon, akkor a meglévő kiszolgáló pg_dump használatával egy memóriaképet kell használnia, és vissza kell állítania egy, a Geo-redundáns biztonsági mentéssel konfigurált, újonnan létrehozott kiszolgálóra.

## <a name="cross-region-read-replicas"></a>Régiók közötti olvasási replikák
Az üzleti folytonosság és a vész-helyreállítás megtervezése érdekében a tartományok közötti olvasási replikákat is használhatja. Az olvasási replikák aszinkron módon frissülnek a PostgreSQL fizikai replikációs technológiájának használatával. További információk az olvasási replikák, az elérhető régiók és a feladatátvétel az [olvasási replikák fogalmai című cikkben](concepts-read-replicas.md)olvashatók. 

## <a name="faq"></a>GYIK
### <a name="where-does-azure-database-for-postgresql-store-customer-data"></a>Hol tárolja Azure Database for PostgreSQL az ügyféladatokat?
Alapértelmezés szerint a Azure Database for PostgreSQL nem helyezi át vagy nem tárolja az ügyféladatokat a-ben üzembe helyezett régióból. Az ügyfelek azonban igény szerint engedélyezhetik a [geo-redundáns biztonsági mentéseket](concepts-backup.md#backup-redundancy-options) , vagy létrehozhatnak [régiók közötti olvasási replikát](concepts-read-replicas.md#cross-region-replication) az adattároláshoz egy másik régióban.


## <a name="next-steps"></a>Következő lépések
- További információ a [Azure Database for PostgreSQL automatikus biztonsági mentéséről](concepts-backup.md). 
- Ismerje meg, hogyan lehet visszaállítani [a Azure Portal](howto-restore-server-portal.md) vagy [Az Azure CLI](howto-restore-server-cli.md)használatával.
- További információ a [Azure Database for PostgreSQL található olvasási replikáról](concepts-read-replicas.md).
