---
title: Üzletmenet-folytonosság – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk az üzletmenet folytonosságát (az időponthoz való visszaállítást, az adatközpont-kimaradást, a Geo-visszaállítást és a replikákat) ismerteti Azure Database for PostgreSQL használatakor.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/07/2020
ms.openlocfilehash: 6bcb1ea6c16fd387dfb7f15f909d1908c20a44d7
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2020
ms.locfileid: "91710906"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>Az üzletmenet folytonosságának áttekintése Azure Database for PostgreSQL – egyetlen kiszolgálóval

Ez az Áttekintés ismerteti azokat a képességeket, amelyeket a Azure Database for PostgreSQL az üzletmenet folytonosságát és a vész-helyreállítást biztosítja. Megtudhatja, hogyan lehet helyreállítani az adatvesztést okozó, vagy az adatbázis és az alkalmazás elérhetetlenné válását okozó zavaró események helyreállításának lehetőségeit. Megtudhatja, mi a teendő, ha egy felhasználói vagy alkalmazáshiba hatással van az adatok integritására, egy Azure-régió leáll, vagy az alkalmazás karbantartást igényel.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Az üzletmenet folytonosságának biztosítására használható funkciók

Az üzletmenet-folytonossági terv kidolgozása során meg kell ismernie a maximális elfogadható időtartamot, mielőtt az alkalmazás teljesen helyreáll a zavaró esemény után – ez a helyreállítási idő célkitűzése (RTO). Meg kell ismernie a legutóbbi adatfrissítések (időintervallum) maximális mennyiségét is, ha az alkalmazás a zavaró esemény utáni helyreállítás során elveszíti az adatvesztést – ez a helyreállítási pont célkitűzése (RPO).

A Azure Database for PostgreSQL olyan üzletmenet-folytonossági funkciókat biztosít, amelyek a Geo-redundáns biztonsági mentéseket is lehetővé teszi a Geo-visszaállítás megkezdésére és az olvasási replikák egy másik régióban történő üzembe helyezésére. Mindegyiknek különböző jellemzői vannak a helyreállítási időhöz és a lehetséges adatvesztéshez. A [geo-visszaállítás](concepts-backup.md) funkcióval egy új kiszolgáló jön létre, amely egy másik régióból replikált biztonsági mentési adatok használatával történik. A helyreállításhoz és helyreállításhoz szükséges teljes idő az adatbázis méretétől és a helyreállítható naplók mennyiségétől függ. A kiszolgáló létrehozásának teljes ideje néhány perctől néhány órára változhat. [Olvasási replikák](concepts-read-replicas.md)esetén az elsődleges tranzakciós naplók aszinkron módon lesznek továbbítva a replikára. Az elsődleges és a replika közötti késés a helyek közötti késéstől és a továbbítandó adatok mennyiségétől függ. Ha egy elsődleges hely meghibásodása, például a rendelkezésre állási zóna hibája, a replika előléptetése rövidebb RTO és adatvesztést biztosít. 

A következő táblázat összehasonlítja a RTO és a RPO egy tipikus forgatókönyvben:

| **Képesség** | **Basic** | **Általános célú** | **Memóriaoptimalizált** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Időponthoz kötött visszaállítás biztonsági másolatból | A megőrzési időtartamon belüli visszaállítási pontok | A megőrzési időtartamon belüli visszaállítási pontok | A megőrzési időtartamon belüli visszaállítási pontok |
| Geo-visszaállítás földrajzilag replikált biztonsági másolatokból | Nem támogatott | RTO – változó <br/>RPO < 1 óra | RTO – változó <br/>RPO < 1 óra |
| Olvasási replikák | RTO – perc <br/>RPO < 5 perc | RTO – perc <br/>RPO < 5 perc| RTO – perc <br/>RPO < 5 perc|

> [!IMPORTANT]
> Az itt említett várt RTO és RPO csak referenciául szolgálnak. Ezekhez a metrikához nem biztosítunk SLA-t.

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
