---
title: Verziószámozási házirend – Azure Database for MySQL – egyetlen kiszolgáló és egy rugalmas kiszolgáló (előzetes verzió)
description: A MySQL főbb és alverzióinak házirendjét ismerteti Azure Database for MySQL
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.date: 11/03/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 0670107d84374589aa60cc18f184b9b3d3facce1
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331804"
---
# <a name="azure-database-for-mysql-versioning-policy"></a>Azure Database for MySQL verziószámozási házirend

Ez az oldal leírja a Azure Database for MySQL verziószámozási házirendjét, és alkalmazható az Azure Database for MySQL-Single Server és a Azure Database for MySQL-rugalmas kiszolgáló (előzetes verzió) üzembe helyezési módjaira.

## <a name="supported--mysql-versions"></a>Támogatott MySQL-verziók

A Azure Database for MySQL a következő adatbázis-verziókat támogatja.

| Verzió | Önálló kiszolgáló | Rugalmas kiszolgáló (előzetes verzió) |
| ----- | :------: | :----: |
| MySQL 8 | X |  | 
| MySQL 5,7 | X | X |
| MySQL 5,6| X |  |


## <a name="major-version-support"></a>Főverzió támogatása
A MySQL minden jelentős verzióját a Azure Database for MySQL fogja támogatni attól az időponttól kezdve, amikor az Azure megkezdi a verzió támogatását, egészen addig, amíg a MySQL-Közösség el nem távolítja a verziót, ahogy azt a [verziószámozási házirend](https://en.wikipedia.org/wiki/mysql)biztosítja.

## <a name="minor-version-support"></a>Másodlagos verzió támogatása
A Azure Database for MySQL a rendszeres karbantartás részeként automatikusan végrehajtja az Azure-beli elsődleges MySQL-verzióra való frissítést. 

## <a name="major-version-retirement-policy"></a>Főverzió kivezetési szabályzata
Az alábbi táblázat a MySQL főverzióinak kivonulási részleteit tartalmazza. A dátumok a [MySQL verziószámozási házirendjét](https://www.mysql.com/support/eol-notice.html)követik.

| Verzió | Újdonságok | Azure-támogatás kezdő dátuma | Nyugdíjazás dátuma|
| ----- | ----- | ------ | ----- |
| [MySQL 5,6](https://dev.mysql.com/doc/relnotes/mysql/5.6/)| [Funkciók](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | Március 20., 2018 | Február 2021
| [MySQL 5,7](https://dev.mysql.com/doc/relnotes/mysql/5.7/) | [Funkciók](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-31.html) | Március 20., 2018 | Október 2023
| [MySQL 8](https://mysqlserverteam.com/whats-new-in-mysql-8-0-generally-available/) | [Szolgáltatások](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)) | December 11., 2019 | Április 2026


## <a name="retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql"></a>A kivont MySQL Engine-verziók nem támogatottak Azure Database for MySQL

Ha továbbra is a kivont verziót futtatja, az egyes MySQL-adatbázisok lejárati dátuma után vegye figyelembe a következő korlátozásokat:
- Mivel a Közösség nem szabadít fel semmilyen további hibajavítást vagy biztonsági javítást, Azure Database for MySQL nem fogja feljavítani a kivont adatbázismotor számára az esetleges hibákat vagy biztonsági problémákat, vagy egyéb módon biztonsági intézkedéseket hajt végre a kivont adatbázismotor tekintetében. Az Azure azonban továbbra is rendszeres karbantartást és javítást végez a gazdagép, az operációs rendszer, a tárolók és a szolgáltatásokkal kapcsolatos egyéb összetevők számára.
- Ha bármilyen támogatási probléma merülhet fel a MySQL-adatbázissal kapcsolatban, előfordulhat, hogy nem tudunk támogatást nyújtani Önnek. Ilyen esetekben frissítenie kell az adatbázist ahhoz, hogy támogatást nyújtson Önnek.
- Nem fog tudni új adatbázis-kiszolgálókat létrehozni a kivont verzióhoz. Ugyanakkor időponthoz kapcsolódó helyreállításokat is végrehajthat, és a meglévő kiszolgálókhoz olvasási replikákat hozhat létre.
- A Azure Database for MySQL által fejlesztett új szolgáltatási képességek csak a támogatott adatbázis-kiszolgáló verziók esetében érhetők el.
- Az üzemidőre vonatkozó SLA-kat kizárólag Azure Database for MySQL szolgáltatással kapcsolatos problémákra, és nem az adatbázismotor által okozott hibák okozta állásidőre alkalmazza a rendszer.  
- Ha súlyos fenyegetést észlelt a szolgáltatás a kivont adatbázis-verzióban azonosított MySQL adatbázismotor biztonsági rése miatt, az Azure úgy dönthet, hogy leállítja az adatbázis-kiszolgáló számítási csomópontját, hogy először biztonságossá tegye a szolgáltatást. A rendszer a kiszolgáló online állapotba hozása előtt frissíti a kiszolgálót. A frissítési folyamat során a rendszer mindig a szolgáltatásban végzett automatikus biztonsági mentéssel védi az adatokat, amelyek igény szerint visszaállíthatók a régebbi verzióra. 



## <a name="next-steps"></a>További lépések
- Lásd: Azure Database for MySQL – egy kiszolgáló által [támogatott verziók](./concepts-supported-versions.md)
- Lásd: Azure Database for MySQL – rugalmas kiszolgáló (előzetes [verzió) támogatott verziók](flexible-server/concepts-supported-versions.md)
- A frissítések végrehajtásához tekintse meg a MySQL [-memóriaképet és a visszaállítást](./concepts-migrate-dump-restore.md) .
