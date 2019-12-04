---
title: Üzletmenet-folytonosság – Azure Database for MariaDB
description: Ismerje meg az üzletmenet folytonosságát (az időponthoz tartozó visszaállítást, az adatközpont-kimaradást, a Geo-visszaállítást) Azure Database for MariaDB szolgáltatás használatakor.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 76e749b26be5a5204b247d294f26da169f84094c
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74773017"
---
# <a name="understand-business-continuity-in-azure-database-for-mariadb"></a>Az üzletmenet folytonosságának megismerése Azure Database for MariaDB

Ez a cikk azokat a képességeket ismerteti, amelyeket a Azure Database for MariaDB az üzletmenet folytonosságát és a vész-helyreállítást biztosítja. Megtudhatja, hogyan lehet helyreállítani az adatvesztést okozó, vagy az adatbázis és az alkalmazás elérhetetlenné válását okozó zavaró események helyreállításának lehetőségeit. Megtudhatja, mi a teendő, ha egy felhasználói vagy alkalmazáshiba hatással van az adatok integritására, egy Azure-régió leáll, vagy az alkalmazás karbantartást igényel.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Az üzletmenet folytonosságának biztosítására használható funkciók

A Azure Database for MariaDB olyan üzletmenet-folytonossági funkciókat biztosít, amelyek automatikus biztonsági mentést tartalmaznak, és lehetővé teszi a felhasználók számára a Geo-visszaállítás kezdeményezését. Mindegyik különböző tulajdonságokkal rendelkezik a becsült helyreállítási idő (ERT) és a lehetséges adatvesztés tekintetében. Ha megértette ezeket a lehetőségeket, választhat közülük, és együtt használhatja őket különböző forgatókönyvek esetén. Az üzletmenet-folytonossági terv kidolgozása során meg kell ismernie a maximális elfogadható időtartamot, mielőtt az alkalmazás teljesen helyreáll a zavaró esemény után – ez a helyreállítási idő célkitűzése (RTO). Meg kell ismernie a legutóbbi adatfrissítések (időintervallum) maximális mennyiségét is, ha az alkalmazás a zavaró esemény utáni helyreállítás során elveszíti az adatvesztést – ez a helyreállítási pont célkitűzése (RPO).

A következő táblázat összehasonlítja a rendelkezésre álló funkciók ERT-és RPO:

| **Képesség** | **Basic** | **általános célú** | **Memóriaoptimalizált** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Időponthoz kötött visszaállítás biztonsági másolatból | A megőrzési időtartamon belüli visszaállítási pontok | A megőrzési időtartamon belüli visszaállítási pontok | A megőrzési időtartamon belüli visszaállítási pontok |
| Geo-visszaállítás földrajzilag replikált biztonsági másolatokból | Nem támogatott | ERT < 12 h<br/>RPO < 1 óra | ERT < 12 h<br/>RPO < 1 óra |

> [!IMPORTANT]
> Ha törli a kiszolgálót, a kiszolgálón található összes adatbázis is törlődik, és nem állítható helyre. Törölt kiszolgálót nem lehet visszaállítani.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Kiszolgáló helyreállítása felhasználói vagy alkalmazáshiba miatt

A szolgáltatás biztonsági másolatai segítségével helyreállíthat egy kiszolgálót a különböző zavaró eseményekről. Előfordulhat, hogy A felhasználók véletlenül törölhetnek bizonyos adatfájlokat, és véletlenül eldobják egy fontos táblát, vagy akár el is dobják a teljes adatbázist. Előfordulhat, hogy egy alkalmazás helytelenül írja felül a megfelelő adatmennyiséget az alkalmazás hibája miatt, és így tovább.

Elvégezheti egy időponthoz való visszaállítást, hogy a kiszolgálóról egy ismert jó időpontra készítsen másolatot. Az időpontnak a kiszolgálón beállított biztonsági mentési megőrzési időtartamon belül kell lennie. Miután az adatok vissza lettek állítva az új kiszolgálóra, lecserélheti az eredeti kiszolgálót az újonnan visszaállított kiszolgálóra, vagy átmásolhatja a szükséges adatait a visszaállított kiszolgálóról az eredeti kiszolgálóra.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Helyreállítás Azure Regional adatközpont-kimaradásból

Bár ritka, mégis előfordulhat, hogy valamelyik Azure-adatközpont leáll. Leállás esetén az üzleti megszakadást okoz, amely csak néhány percet vesz igénybe, de az elmúlt órákban is tarthat.

Az egyik lehetőség, hogy megvárja, amíg a kiszolgáló ismét online állapotba kerül, amikor az adatközpont kimaradása meghalad. Ez olyan alkalmazásokhoz használható, amelyek megengedhetik maguknak, hogy a kiszolgáló bizonyos ideig offline állapotba kerüljön, például egy fejlesztési környezetben. Ha az adatközpont leáll, nem tudja, mennyi ideig tarthat a leállás, így ez a lehetőség csak akkor működik, ha egy ideig nincs szüksége a kiszolgálóra.

A másik lehetőség a Azure Database for MariaDB geo-visszaállítási funkciójának használata, amely a-kiszolgálót a Geo-redundáns biztonsági másolatok használatával állítja vissza. Ezek a biztonsági másolatok akkor is elérhetők, ha a kiszolgáló által üzemeltetett régió offline állapotban van. Ezeket a biztonsági másolatokból bármely más régióba visszaállíthatja, és a kiszolgáló ismét online állapotba helyezhető.

> [!IMPORTANT]
> A Geo-visszaállítás csak akkor lehetséges, ha a kiszolgálót geo-redundáns biztonsági mentési tárolóval kiépített.

## <a name="next-steps"></a>Következő lépések

- Az automatikus biztonsági mentéssel kapcsolatos további tudnivalókért tekintse meg a [biztonsági mentések a Azure Database for MariaDBban](concepts-backup.md)című témakört.
- Ha a Azure Portal használatával szeretne visszaállítani egy adott időpontra, tekintse meg [az adatbázis visszaállítása egy időpontra a Azure Portal használatával](howto-restore-server-portal.md)című témakört.

<!--
- To restore to a point in time using Azure CLI, see [restore database to a point in time using CLI](howto-restore-server-cli.md). 
-->
