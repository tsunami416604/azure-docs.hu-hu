---
title: "Az Azure Database-áttelepítés minimális állásidővel MySQL |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan hajthat végre az Azure Database-MySQL-adatbázis minimális állásidővel áttelepítését a MySQL és beállítása a terhelési kezdeti és folyamatos adatszinkronizálás forrásadatbázis céladatbázist Attunity megfelel a Microsoft Áttelepítés."
services: mysql
author: HJToland3
ms.author: jtoland
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 01/04/2018
ms.openlocfilehash: 429f5c9f939a802184a6513a63fb9115abf4b235
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>MySQL az Azure Database-áttelepítés minimális állásidő érdekében
Áttelepítheti a meglévő MySQL-adatbázis Azure-adatbázis a MySQL Attunity megfelel a Microsoft Migrations, egy közös szponzorált, közös ajánlat Attunity és a Azure adatbázis áttelepítési szolgáltatás nem megfelelően biztosított Microsoft Microsoft-ügyfél további költség nélkül. Microsoft Migrations Attunity megfelel is lehetővé teszi, hogy az adatbázis áttelepítése az állásidő minimális, és a forrás-adatbázis továbbra is fennáll, az áttelepítési folyamat során működőképes legyen.

Attunity megfelel egy olyan adatok replikációs eszköz, amely lehetővé teszi a különböző forrásokból és célok, közötti adatszinkronizálás, propagálása a létrehozási parancsprogrammal és az adatbázis társított adatokat. Attunity replikálás nem propagál minden egyéb összetevők (például SP, eseményindítók, Funkciók, stb.) vagy convert, például ilyen összetevők, T-SQL tárolt PL/SQL-kódot.

> [!NOTE]
> Míg Attunity replikálás áttelepítési forgatókönyvek széles körét támogatja, Microsoft Migrations Attunity megfelel egy adott részének forrás célgazdagép, célfelhő párokból támogatása összpontosít.

A folyamat minimális állásidővel áttelepítés áttekintését tartalmazza:

1. **A MySQL-forrás séma áttelepítése** MySQL-adatbázis használatával egy Azure-adatbázishoz a [MySQL munkaterület](https://www.mysql.com/products/workbench/).

2. **Kezdeti betöltés és az adatok folyamatos szinkronizálásához a forrás-adatbázisból történő beállítása a céladatbázis** Attunity megfelel a Microsoft Migrations. Ezzel minimálisra csökkenti a forrásadatbázis be kell állítania csak olvashatóként Azure váltani az alkalmazásait, és a cél MySQL-adatbázis előkészítése az idejét.

Az Attunity megfelel a Microsoft Migrations ajánlat kapcsolatos további információkért tekintse meg a következőket:
 - A Microsoft Migrations Attunity replikálás [weblap](https://aka.ms/attunity-replicate).
 - [Töltse le](http://discover.attunity.com/download-replicate-microsoft-lp6657.html) Attunity megfelel a Microsoft áttelepítésre.
 - Az Attunity replikálás [közösségi](https://microsoft.attunity.com/), az első lépések útmutatója, oktatóanyagok és támogatás.
 - Az Attunity használatával történő MySQL az Azure-adatbázis MySQL áttelepítésének lépésenkénti útmutatásért tekintse meg a [adatbázis áttelepítési útmutató](https://datamigration.microsoft.com/scenario/mysql-to-azuremysql).