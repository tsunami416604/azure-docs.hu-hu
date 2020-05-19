---
title: Az adatváltozások nyomon követése az Azure SQL Edge-ben (előzetes verzió)
description: Tudnivalók a változások nyomon követéséről és az adatváltozások rögzítéséről az Azure SQL Edge-ben (előzetes verzió)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a91ed5d4763aa521c9f6ed913dc532b08e37039f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597267"
---
# <a name="tracking-data-changes-in-azure-sql-edge-preview"></a>Az adatváltozások nyomon követése az Azure SQL Edge-ben (előzetes verzió)

Az Azure SQL Edge támogatja a két olyan SQL Server funkciót, amelyek nyomon követik az adatbázisban tárolt adatváltozásokat:[change Tracking](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking) és [adatváltozások rögzítése](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture). Ezek a funkciók lehetővé teszik az alkalmazások számára az adatbázis felhasználói tábláiban végrehajtott DML-változások (INSERT, Update és DELETE műveletek) meghatározását. Ugyanazon az adatbázison engedélyezhető az adatváltozások rögzítése és a változások nyomon követése is. nincs szükség különleges szempontokra.

Az adatbázisokban megváltoztatott Adatlekérdezési képesség fontos követelmény, hogy egyes alkalmazások hatékonyak legyenek. Az adatváltozások meghatározásához általában az alkalmazás fejlesztőknek eseményindítókat, időbélyeg-oszlopokat és további táblázatokat használó egyéni követési módszert kell alkalmazniuk az alkalmazásaikban. Ezeknek az alkalmazásoknak a létrehozása általában sok munkát igényel a megvalósításhoz, a séma frissítéseihez vezet, és gyakran nagy teljesítményű terhelést eredményez. A IoT-megoldás esetében, ahol szükség van az adatoknak a szélétől a felhőbe vagy az adatközpontba való rendszeres áthelyezésére, a változások követése nagyon hasznos lehet. Ez lehetővé teszi, hogy gyorsan és hatékonyabban kérdezze le a lekérdezéseket a legutóbbi szinkronizálás változásai közül, és töltse fel ezeket a változtatásokat a felhőbe vagy az adatközpontba. A Change Tracking és az adatváltozások rögzítésének előnyeiről további részleteket az [adatváltozások rögzítése vagy a Change Tracking használatának előnyei](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking)című témakörben talál. 

A Change Tracking és az adatváltozások rögzítése közötti különbségek megismeréséhez tekintse át az [adatváltozások rögzítése és a Change Tracking funkció közötti különbségeket](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking) .

## <a name="change-data-capture"></a>Adatváltozások rögzítése

Az adatváltozások rögzítésének módjáról az [adatváltozások rögzítése című témakörben olvashat](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server)bővebben.

Az adatváltozások rögzítésének engedélyezésével és letiltásával kapcsolatban tekintse meg az [adatváltozások rögzítésének engedélyezése és letiltása](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server) című témakört.

Az adatváltozások rögzítésének felügyeletéhez és figyeléséhez tekintse át az [adatváltozások rögzítésének felügyelete és figyelése](https://docs.microsoft.com/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server)

Ha szeretné megtudni, hogyan lehet lekérdezni és dolgozni a módosított adatmennyiségeket, tekintse át az [adatváltozások használata](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-data-sql-server) című témakört

## <a name="change-tracking"></a>Változások követése

A Change Tracking működésének részletes ismertetését lásd: [change Tracking](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server).

Az Change Tracking engedélyezésének és letiltásának megismeréséhez tekintse meg az [change Tracking engedélyezése és letiltása](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) című témakört.

Change Tracking felügyeletéhez, figyeléséhez és kezeléséhez tekintse meg a [felügyelet és figyelés Change Tracking](https://docs.microsoft.com/sql/relational-databases/track-changes/manage-change-tracking-sql-server)

Ha szeretné megtudni, hogyan lehet lekérdezni és dolgozni a módosított adatmennyiségeket, tekintse át az [adatváltozások használata](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-tracking-sql-server) című témakört

## <a name="temporal-tables"></a>Ideiglenes táblák

A SQL Server Change Tracking és adatváltozás-rögzítési funkcióinak támogatása mellett az Azure SQL Edge is támogatja a SQL Server időbeli táblázatok funkcióját. Az időbeli táblák (más néven rendszerverziós időszakos táblázatok) olyan adatbázis-szolgáltatásként szolgálnak, amely beépített támogatást biztosít a táblázatban tárolt adatokkal kapcsolatos információk biztosításához, és nem csak az aktuális időpontban helyes adatokat.

A rendszerverzióval ellátott időbeli táblázat egy olyan felhasználói tábla, amely az adatváltozások teljes előzményeinek megőrzését, valamint az egyszerű időpontok elemzését teszi lehetővé. Az ilyen típusú ideiglenes táblázatot rendszerverzióval ellátott időbeli táblázatnak nevezzük, mert az egyes sorok érvényességi idejét a rendszer (azaz az adatbázismotor) kezeli.

Minden időbeli táblázat két explicit módon definiált oszlopot tartalmaz, amelyek mindegyike datetime2 adattípussal rendelkezik. Ezeket az oszlopokat a rendszer időszak oszlopainak nevezzük. Ezeket az időszakos oszlopokat kizárólag a rendszer használja az egyes sorok érvényességi időtartamának rögzítésére, amikor egy sor módosul.

Ezen időszak oszlopain kívül egy időbeli táblázat egy másik, tükrözött sémával rendelkező táblára mutató hivatkozást is tartalmaz. A rendszer ezt a táblázatot használja a sor előző verziójának automatikus tárolására, valahányszor az időbeli táblázat egy sora frissül vagy törölve lesz. Ezt a további táblázatot az előzmény táblázatnak nevezzük, míg az aktuális (tényleges) sor verzióit tároló főtábla az aktuális tábla, vagy egyszerűen az időbeli tábla. Az ideiglenes tábla létrehozásakor a felhasználók megadhatják a meglévő History (sémának megfelelő) táblát, vagy hagyhatják a rendszer létrehozása alapértelmezett előzményei táblát.

Az időbeli táblázatokkal kapcsolatos további információkért tekintse meg az [ideiglenes táblákat](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables)

## <a name="see-also"></a>Lásd még:

- [Adatfolyamok az Azure SQL Edge-ben (előzetes verzió)](stream-data.md)
- [Gépi tanulás és AI a ONNX az Azure SQL Edge-ben (előzetes verzió)](onnx-overview.md)
- [Replikáció konfigurálása az Azure SQL Edge-be (előzetes verzió)](configure-replication.md)
- [Adatbázisok biztonsági mentése és visszaállítása az Azure SQL Edge-ben (előzetes verzió)](backup-restore.md)



