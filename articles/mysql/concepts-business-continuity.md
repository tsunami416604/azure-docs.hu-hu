---
title: Az Azure Database for MySQL üzletmenet-folytonossági funkcióinak áttekintése
description: Az Azure Database for MySQL üzletmenet-folytonossági funkcióinak áttekintése.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: a09c1934ecb34518b191a8e730a72efecc85aa2f
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55692885"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mysql"></a>Az Azure Database for MySQL üzletmenet-folytonossági funkcióinak áttekintése

Ez az áttekintés, Azure Database for MySQL biztosítja az üzletmenet-folytonosság és vészhelyreállítás funkcióit írja le. Ismerje meg azokat a káros eseményeket, amelyek adatvesztéshez vezethetnek vagy az adatbázis és az alkalmazás elérhetetlenné okozhat a beállításokat. Ismerje meg, mi a teendő, ha egy felhasználó vagy alkalmazás hiba hatással van az adatok integritásának megőrzése, egy Azure-régióban van kimaradás vagy az alkalmazás karbantartásra szorul.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Üzletmenet-folytonosságot biztosító használó szolgáltatások

Azure Database for MySQL üzletmenet-folytonossági funkciókat, beleértve az automatikus biztonsági mentést és a felhasználók a geo-visszaállítás kezdeményezése itt. Minden más paraméterekkel rendelkezik a becsült helyreállítási idő (ERT) és az esetleges adatvesztés. Ha már megismerte, hogy ezek a beállítások, válogathat közülük, és a különböző helyzetekhez együtt használja azokat. Az üzletmenet folytonosságát biztosító terve kidolgozásakor kell megérteni a maximális elfogadható idő, mielőtt az alkalmazás a zavaró eseményeket követő teljes helyreállításának – ez a helyreállítási időre vonatkozó célkitűzés (RTO). Emellett ismernie kell a legújabb adatok maximális mérete (időintervallum) frissítéseket az alkalmazás működését a zavaró eseményeket követő helyreállítása során elvesztése – Ez a helyreállítási időkorlátot (RPO).

Az alábbi táblázat összehasonlítja a ERT és RPO elérhető funkciók:

| **Képesség** | **Basic** | **Általános célú** | **Memóriaoptimalizált** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Időponthoz kötött visszaállítás biztonsági másolatból | A megőrzési időtartamon belül bármely visszaállítási pont | A megőrzési időtartamon belül bármely visszaállítási pont | A megőrzési időtartamon belül bármely visszaállítási pont |
| Georedundáns visszaállítás georeplikált biztonsági másolatokból | Nem támogatott | ERT < 12 óra<br/>RPO < 1 óra | ERT < 12 óra<br/>RPO < 1 óra |

> [!IMPORTANT]
> Törölt kiszolgálók **nem** állítható vissza. Ha törli a kiszolgálót, akkor a kiszolgálóhoz tartozó összes adatbázis is törlődik, és nem állítható helyre.

## <a name="recover-a-server-after-a-user-or-application-error"></a>A kiszolgáló helyreállítása után a felhasználó vagy alkalmazás hiba

A szolgáltatás biztonsági mentések használatával egy kiszolgáló helyreállítása a különféle zavaró eseményeket. A felhasználó előfordulhat, hogy véletlenül törölhet néhány adatot, véletlenül dobja el egy fontos táblát, vagy még dobja el a teljes adatbázisra vonatkozóan. Egy alkalmazás előfordulhat, hogy véletlenül felülírja a helyes adatokat rossz adatokkal egy alkalmazás valamilyen alkalmazáshiba miatt, és így tovább.

A pont a-időponthoz kötött visszaállítás hozhat létre a kiszolgáló számára ismert jó pont másolatát időben hajthat végre. Az időponthoz kötött konfigurálta, a kiszolgáló a biztonsági másolatok megőrzési időszakán belül kell lennie. Az adatok az új kiszolgáló visszaállítása után az eredeti kiszolgáló cserélje le az újonnan visszaállított kiszolgáló, vagy átmásolhatja a szükséges adatokat a visszaállított kiszolgáló az eredeti kiszolgálóra.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Az Azure regionális adatközpontjának leállása esetén helyreállítható

Bár ritka, mégis előfordulhat, hogy valamelyik Azure-adatközpont leáll. Szolgáltatáskimaradás esetén előfordulhat, hogy csak az elmúlt pár perc alatt, de sikerült az elmúlt óra üzletmenet okoz.

Az egyik lehetőség, hogy Várjon, amíg a kiszolgáló ismét online állapotú lesz, ha a tartós adatközponti üzemkimaradások keresztül. Az alkalmazásokat, amelyek esetében megengedhető, a kiszolgáló offline állapotba kell bizonyos ideig, például egy fejlesztési környezetben működik. Amikor adatközpontban szolgáltatáskimaradás következik, nem tudja, mennyi ideig a szolgáltatáskimaradás elhárítása után előfordulhat, hogy a legutóbbi, így ez a beállítás csak akkor működik, ha már nincs szüksége a kiszolgáló egy ideig.

A másik lehetőség, hogy az Azure Database for MySQL a geo-visszaállítás szolgáltatást, amely visszaállítja az a kiszolgáló georedundáns biztonsági másolatokból. Ezeket a biztonsági másolatokat elérhetők, még akkor is, ha a régió, a kiszolgáló üzemel, a kapcsolat nélküli üzemmódban. Ezek a biztonsági mentések visszaállítása bármelyik régióban, és a kiszolgáló ismét online állapotba.

> [!IMPORTANT]
> A GEO-visszaállítás csak akkor lehetséges, a kiszolgáló georedundáns biztonsági mentési tároló üzembe helyezése. Váltás a helyileg redundáns a meglévő kiszolgáló georedundáns biztonsági mentését szeretné, ha kell venni egy, a meglévő kiszolgáló mysqldump használatával memóriakép és annak visszaállítására egy újonnan létrehozott kiszolgáló georedundáns biztonsági mentés konfigurálva.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet a [automatikus biztonsági másolatokat az Azure Database for MySQL-hez](concepts-backup.md).
- Ismerje meg, hogyan segítségével történő visszaállításhoz [az Azure Portalon](howto-restore-server-portal.md) vagy [az Azure CLI](howto-restore-server-cli.md).
- Ismerje meg [olvassa el a replikákat az Azure Database for MySQL-hez](concepts-read-replicas.md).