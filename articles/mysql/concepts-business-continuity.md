---
title: Üzletmenet folytonossága – Azure Database for MySQL
description: Ismerje meg az üzletmenet folytonosságát (időponthoz kötött visszaállítás, adatközpont-kimaradás, geo-visszaállítás) az Azure Database for MySQL szolgáltatás használatakor.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: af0069adc741cfc802c37c90c0c7ec3c3ba74bb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537227"
---
# <a name="understand-business-continuity-in-azure-database-for-mysql"></a>Az üzletmenet folytonosságának megismerése a MySQL-hez készült Azure Database-ben

Ez a cikk ismerteti az Azure Database for MySQL által az üzletmenet folytonosságát és a vészhelyreállítást. Ismerje meg a zavaró események ből való helyreállítás lehetőségeit, amelyek adatvesztést okozhatnak, vagy az adatbázis és az alkalmazás elérhetetlenné válhatnak. Megtudhatja, hogy mi a teendő, ha egy felhasználói vagy alkalmazáshiba hatással van az adatok integritására, egy Azure-régió ban van egy kimaradás, vagy az alkalmazás karbantartást igényel.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Az üzletmenet folytonosságának biztosításához használható funkciók

Az Azure Database for MySQL olyan üzletmenet-folytonossági funkciókat biztosít, amelyek magukban foglalják az automatikus biztonsági mentéseket és a felhasználók számára a geo-visszaállítás kezdeményezését. Mindegyik különböző jellemzőkkel rendelkezik a becsült helyreállítási idő (ERT) és a potenciális adatvesztés. Miután megértette ezeket a lehetőségeket, választhat közülük, és együtt használhatja őket a különböző forgatókönyvekhez. Az üzletmenet-folytonossági terv kidolgozásakor meg kell értenie a maximális elfogadható időt, mielőtt az alkalmazás teljesen helyreáll a zavaró esemény után - ez a helyreállítási idő célkitűzés (RTO). Azt is meg kell értenie, hogy az alkalmazás a zavaró esemény után a legutóbbi adatfrissítések maximális mennyiségét (időintervallumát) képes elviselni – ez a helyreállításipont-célkitűzés (RPO).

Az alábbi táblázat összehasonlítja az ERT-t és az RPO-t a rendelkezésre álló szolgáltatásokhoz:

| **Képesség** | **Basic** | **Általános cél** | **Memóriaoptimalizált** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Időponthoz kötött visszaállítás biztonsági másolatból | Bármely visszaállítási pont a megőrzési időszakon belül | Bármely visszaállítási pont a megőrzési időszakon belül | Bármely visszaállítási pont a megőrzési időszakon belül |
| Geo-visszaállítás georeplikált biztonsági mentések | Nem támogatott | ERT < 12 óra<br/>RPO < 1 óra | ERT < 12 óra<br/>RPO < 1 óra |

> [!IMPORTANT]
> A törölt kiszolgálók **nem** állíthatók vissza. Ha törli a kiszolgálót, a kiszolgálóhoz tartozó összes adatbázis is törlődik, és nem állítható helyre.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Kiszolgáló helyreállítása felhasználói vagy alkalmazáshibával

A szolgáltatás biztonsági másolatai segítségével helyreállíthatja a kiszolgálót a különböző zavaró eseményekből. A felhasználó véletlenül törölhet bizonyos adatokat, véletlenül eldobhat egy fontos táblát, vagy akár egy teljes adatbázist is eldobhat. Előfordulhat, hogy egy alkalmazás véletlenül felülírja a jó adatokat rossz adatokkal egy alkalmazáshiba miatt, és így tovább.

A kiszolgáló egy ismert megfelelő időpontra való másolatának létrehozásához időpontban is végrehajthat egy időpontot. Ennek az időpontnak a kiszolgálóhoz beállított biztonsági mentési megőrzési időszakon belül kell lennie. Miután az adatokat visszaállította az új kiszolgálóra, lecserélheti az eredeti kiszolgálót az újonnan visszaállított kiszolgálóra, vagy átmásolhatja a szükséges adatokat a visszaállított kiszolgálóról az eredeti kiszolgálóra.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Helyreállítás egy Azure regionális adatközpont-kimaradásából

Bár ritka, mégis előfordulhat, hogy valamelyik Azure-adatközpont leáll. Kimaradás esetén olyan üzleti zavarokat okoz, amelyek csak néhány percig tarthatnak, de órákig is tarthatnak.

Az egyik lehetőség az, hogy megvárja, amíg a kiszolgáló újra online állapotba kerül, amikor az adatközpont kimaradása véget ért. Ez olyan alkalmazásokesetében működik, amelyek megengedhetik maguknak, hogy a kiszolgáló bizonyos ideig offline állapotban legyen, például egy fejlesztői környezetben. Ha az adatközpontban kimaradás van, nem tudja, hogy a szolgáltatáskimaradás mennyi ideig tarthat, így ez a beállítás csak akkor működik, ha egy ideig nincs szüksége a kiszolgálóra.

A másik lehetőség az Azure Database for MySQL geo-visszaállítási szolgáltatás, amely visszaállítja a kiszolgáló georedundáns biztonsági mentések használatával. Ezek a biztonsági mentések akkor is elérhetők, ha a kiszolgáló által üzemeltetett terület offline állapotban van. Ezekből a biztonsági mentésekből bármely más régióba visszaállíthatja, és a kiszolgálót újra online állapotba hozhatja.

> [!IMPORTANT]
> A geo-visszaállítás csak akkor lehetséges, ha a kiszolgálót georedundáns biztonsági mentési tárolóval állította be. Ha egy meglévő kiszolgáló helyiredundáns biztonsági másolatairól georedundáns biztonsági másolatokra szeretne váltani, a meglévő kiszolgáló mysqldump használatával memóriaképet kell készítenie, és vissza kell állítania egy újonnan létrehozott, georedundáns biztonsági másolatokkal konfigurált kiszolgálóra.

## <a name="next-steps"></a>További lépések

- További információ az automatikus biztonsági mentések az [Azure Database for MySQL.](concepts-backup.md)
- Ismerje meg, hogyan állíthatja vissza [az Azure Portalon](howto-restore-server-portal.md) vagy [az Azure CLI](howto-restore-server-cli.md)használatával.
- Ismerje meg a [replikák olvasását az Azure Database for MySQL-ben.](concepts-read-replicas.md)
