---
title: Üzletmenet-folytonosság – Azure Database for MySQL
description: Ismerje meg az üzletmenet folytonosságát (az időponthoz tartozó visszaállítást, az adatközpont-kimaradást, a Geo-visszaállítást) Azure Database for MySQL szolgáltatás használatakor.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: af0069adc741cfc802c37c90c0c7ec3c3ba74bb2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "79537227"
---
# <a name="understand-business-continuity-in-azure-database-for-mysql"></a>Az üzletmenet folytonosságának megismerése Azure Database for MySQL

Ez a cikk azokat a képességeket ismerteti, amelyeket a Azure Database for MySQL az üzletmenet folytonosságát és a vész-helyreállítást biztosítja. Megtudhatja, hogyan lehet helyreállítani az adatvesztést okozó, vagy az adatbázis és az alkalmazás elérhetetlenné válását okozó zavaró események helyreállításának lehetőségeit. Megtudhatja, mi a teendő, ha egy felhasználói vagy alkalmazáshiba hatással van az adatok integritására, egy Azure-régió leáll, vagy az alkalmazás karbantartást igényel.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Az üzletmenet folytonosságának biztosítására használható funkciók

A Azure Database for MySQL olyan üzletmenet-folytonossági funkciókat biztosít, amelyek automatikus biztonsági mentést tartalmaznak, és lehetővé teszi a felhasználók számára a Geo-visszaállítás kezdeményezését. Mindegyik különböző tulajdonságokkal rendelkezik a becsült helyreállítási idő (ERT) és a lehetséges adatvesztés tekintetében. Ha megértette ezeket a lehetőségeket, választhat közülük, és együtt használhatja őket különböző forgatókönyvek esetén. Az üzletmenet-folytonossági terv kidolgozása során meg kell ismernie a maximális elfogadható időtartamot, mielőtt az alkalmazás teljesen helyreáll a zavaró esemény után – ez a helyreállítási idő célkitűzése (RTO). Meg kell ismernie a legutóbbi adatfrissítések (időintervallum) maximális mennyiségét is, ha az alkalmazás a zavaró esemény utáni helyreállítás során elveszíti az adatvesztést – ez a helyreállítási pont célkitűzése (RPO).

A következő táblázat összehasonlítja a rendelkezésre álló funkciók ERT-és RPO:

| **Képesség** | **Alapszintű** | **általános célú** | **Memóriaoptimalizált** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Időponthoz kötött visszaállítás biztonsági másolatból | A megőrzési időtartamon belüli visszaállítási pontok | A megőrzési időtartamon belüli visszaállítási pontok | A megőrzési időtartamon belüli visszaállítási pontok |
| Geo-visszaállítás földrajzilag replikált biztonsági másolatokból | Nem támogatott | ERT < 12 h<br/>RPO < 1 óra | ERT < 12 h<br/>RPO < 1 óra |

> [!IMPORTANT]
> A törölt kiszolgálók **nem** állíthatók vissza. Ha törli a kiszolgálót, a kiszolgálóhoz tartozó összes adatbázis is törlődik, és nem állítható helyre.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Kiszolgáló helyreállítása felhasználói vagy alkalmazáshiba miatt

A szolgáltatás biztonsági másolatai segítségével helyreállíthat egy kiszolgálót a különböző zavaró eseményekről. Előfordulhat, hogy A felhasználók véletlenül törölhetnek bizonyos adatfájlokat, és véletlenül eldobják egy fontos táblát, vagy akár el is dobják a teljes adatbázist. Előfordulhat, hogy egy alkalmazás helytelenül írja felül a megfelelő adatmennyiséget az alkalmazás hibája miatt, és így tovább.

Elvégezheti egy időponthoz való visszaállítást, hogy a kiszolgálóról egy ismert jó időpontra készítsen másolatot. Az időpontnak a kiszolgálón beállított biztonsági mentési megőrzési időtartamon belül kell lennie. Miután az adatok vissza lettek állítva az új kiszolgálóra, lecserélheti az eredeti kiszolgálót az újonnan visszaállított kiszolgálóra, vagy átmásolhatja a szükséges adatait a visszaállított kiszolgálóról az eredeti kiszolgálóra.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Helyreállítás Azure Regional adatközpont-kimaradásból

Bár ritka, mégis előfordulhat, hogy valamelyik Azure-adatközpont leáll. Leállás esetén az üzleti megszakadást okoz, amely csak néhány percet vesz igénybe, de az elmúlt órákban is tarthat.

Az egyik lehetőség, hogy megvárja, amíg a kiszolgáló ismét online állapotba kerül, amikor az adatközpont kimaradása meghalad. Ez olyan alkalmazásokhoz használható, amelyek megengedhetik maguknak, hogy a kiszolgáló bizonyos ideig offline állapotba kerüljön, például egy fejlesztési környezetben. Ha az adatközpont leáll, nem tudja, mennyi ideig tarthat a leállás, így ez a lehetőség csak akkor működik, ha egy ideig nincs szüksége a kiszolgálóra.

A másik lehetőség a Azure Database for MySQL geo-visszaállítási funkciójának használata, amely a-kiszolgálót a Geo-redundáns biztonsági másolatok használatával állítja vissza. Ezek a biztonsági másolatok akkor is elérhetők, ha a kiszolgáló által üzemeltetett régió offline állapotban van. Ezeket a biztonsági másolatokból bármely más régióba visszaállíthatja, és a kiszolgáló ismét online állapotba helyezhető.

> [!IMPORTANT]
> A Geo-visszaállítás csak akkor lehetséges, ha a kiszolgálót geo-redundáns biztonsági mentési tárolóval kiépített. Ha szeretné, hogy a helyileg redundáns biztonsági mentést a meglévő kiszolgálókon, akkor a meglévő kiszolgáló mysqldump használatával készítsen memóriaképet, és állítsa vissza egy, a Geo-redundáns biztonsági mentéssel konfigurált, újonnan létrehozott kiszolgálóra.

## <a name="next-steps"></a>További lépések

- További információ a [Azure Database for MySQL automatikus biztonsági mentéséről](concepts-backup.md).
- Ismerje meg, hogyan lehet visszaállítani [a Azure Portal](howto-restore-server-portal.md) vagy [Az Azure CLI](howto-restore-server-cli.md)használatával.
- További információ a [Azure Database for MySQL található olvasási replikáról](concepts-read-replicas.md).
