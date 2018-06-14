---
title: Az Azure Database PostgreSQL az üzletmenet folytonossága áttekintése
description: Az Azure Database PostgreSQL az üzletmenet folytonossága áttekintése.
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 1b981b650d75556f4521aaf0f089443bb88d064a
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
ms.locfileid: "29693409"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql"></a>Az Azure Database PostgreSQL az üzletmenet folytonossága áttekintése

Ez az áttekintés, amely PostgreSQL az Azure-adatbázis biztosítja az üzletmenet folytonossága és vészhelyreállítás képességeit ismerteti. Ismerje meg a beállítások esetén a helyreállítás őket az eseményeket, amelyek nem adatvesztéshez vezethet, vagy az adatbázis és a már nem érhető el az alkalmazást. Ismerje meg, mi a teendő, ha egy felhasználó vagy alkalmazás hiba hatással van az adatok integritását, egy Azure-régióban van kimaradás vagy az alkalmazás által igényelt karbantartási.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Adja meg az üzletmenet folytonossága használó szolgáltatások

Azure PostgreSQL-adatbázishoz, amely tartalmazza az automatikus biztonsági mentés és a felhasználók mostantól georedundáns helyreállítás kezdeményezése üzleti folytonosságot biztosító szolgáltatásokat biztosít. A becsült helyreállítási idő (Beszúrása) és a lehetséges adatvesztést más tulajdonságokkal mindegyike rendelkezik. Ezek a beállítások elsajátítása után ezek közül választhat, és együtt használva különböző helyzetek kezelésére. Ahogyan az üzletmenet folytonosságát biztosító terve elkészítéséhez kell tudni, hogy a maximális elfogadható idő, mielőtt az alkalmazás teljesen helyreállítja a zavaró esemény után – ez a helyreállítási idő célkitűzés (RTO). A legfrissebb adatok maximális mennyisége ismernie kell frissítések (alatt az időtartam alatt) az alkalmazás működését zavaró esemény után helyreállításakor elvesztését – Ez a helyreállítási pont célkitűzés (RPO).

Az alábbi táblázat összehasonlítja a Beszúrása és a helyreállítási Időkorlát elérhető szolgáltatások:

| **Képesség** | **Basic** | **Általános célú** | **Memóriaoptimalizált** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Időponthoz kötött visszaállítás biztonsági másolatból | A megőrzési időn belül bármely visszaállítási pont | A megőrzési időn belül bármely visszaállítási pont | A megőrzési időn belül bármely visszaállítási pont |
| Georedundáns helyreállítás georeplikált biztonsági másolatból | Nem támogatott | ERT < 12 h<br/>Helyreállítási Időkorlát < 1 óra | ERT < 12 h<br/>Helyreállítási Időkorlát < 1 óra |

> [!IMPORTANT]
> Ha törli a kiszolgálót, akkor a kiszolgálóhoz tartozó összes adatbázis is törlődnek, és nem állítható helyre. A Törölt kiszolgáló nem tudja visszaállítani.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Kiszolgáló helyreállítása után a felhasználó vagy alkalmazás hibája

A szolgáltatás biztonsági mentések segítségével kiszolgáló helyreállítása a különböző zavaró eseményekről. A felhasználó előfordulhat, hogy véletlenül törli az adatokat, akaratlanul dobja el egy fontos táblát, vagy még dobja el a teljes adatbázis. Egy alkalmazás előfordulhat, hogy véletlenül jó adatokra felülírja az alkalmazás hiba miatt hibás adatokkal, és így tovább.

A pont-a--visszaállítás egy korábbi időpontra időben egy példányát a kiszolgálón, hogy egy ismert helyes pont létrehozásához végezheti el. Ezen a pontján idő már konfigurálta a kiszolgáló biztonsági mentés megőrzési időtartamon belül kell lennie. Miután visszaállította az adatokat az új kiszolgálón, az újonnan visszaállított kiszolgáló lecseréli az eredeti kiszolgálón, vagy másolja a szükséges adatokat a visszaállított kiszolgálóról az eredeti kiszolgálón.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Egy Azure regionális adatközpont-meghibásodás után helyreállítása

Bár ritka, mégis előfordulhat, hogy valamelyik Azure-adatközpont leáll. Nem tervezett kimaradás esetén a üzleti szüneteltetése, előfordulhat, hogy csak az utolsó néhány percet, de óra volt utoljára okoz.

Egy elem várja meg, hogy a kiszolgáló ismét online állapotú lesz, amikor az Adatközpont-meghibásodás után. Ez a módszer olyan alkalmazásnál, amely rendelkezik a kiszolgáló offline bizonyos időszakokra, például a környezet számára is. Adatközpont kimaradás rendelkezik, ha nem tudja mennyi ideig a szolgáltatáskimaradás előfordulhat, hogy utoljára, így ez a beállítás csak akkor működik, ha a kiszolgáló nem kell egy ideig.

A másik lehetőség egy PostgreSQL tartozó georedundáns helyreállítás szolgáltatás, amely a kiszolgáló georedundáns biztonsági mentések használatával állítja vissza az Azure-adatbázist használ. Ezek a biztonsági másolatok elérhetők, akkor is, ha a kiszolgálón tárolt régió offline állapotban. Bármely más régióban vissza a biztonsági mentéseket, és a kiszolgáló újra online állapotba.

> [!IMPORTANT]
> Georedundáns helyreállítás az csak akkor lehetséges, ha a kiszolgáló georedundáns biztonsági mentési tárolóval létesített.

## <a name="next-steps"></a>További lépések
- Az automatikus biztonsági mentés kapcsolatos további információkért lásd: [PostgreSQL az Azure-adatbázis a biztonsági másolatok](concepts-backup.md). 
- A pont visszaállítása az Azure portál használatával időpontra, lásd: [ponttá adatbázis visszaállítása az Azure portál használatával időpontra](howto-restore-server-portal.md).
- Visszaállítása egy alkalommal az Azure parancssori felület használatával, lásd: [adatbázis visszaállítása pontra parancssori felület használatával időpontra](howto-restore-server-cli.md).