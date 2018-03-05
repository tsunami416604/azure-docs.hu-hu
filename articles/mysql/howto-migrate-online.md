---
title: "MySQL az Azure Database-áttelepítés minimális állásidő érdekében"
description: "Ez a cikk ismerteti, hogyan hajthat végre az Azure Database-MySQL-adatbázis minimális állásidővel áttelepítését a MySQL és beállítása a terhelési kezdeti és folyamatos adatszinkronizálás forrásadatbázis céladatbázist Attunity megfelel a Microsoft Áttelepítés."
services: mysql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: e1be72d97570643cc8a7c6eb05d3d363e96357b6
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>MySQL az Azure Database-áttelepítés minimális állásidő érdekében
Áttelepítheti a meglévő MySQL-adatbázis Azure-adatbázishoz a MySQL Attunity megfelel a Microsoft Migrations. Attunity megfelel egy közös ajánlat Attunity és a Microsoft. Azure-adatbázis áttelepítése szolgáltatás megfelelően szerepel a Microsoft ügyfelei további költségek nélkül. 

Attunity megfelel segít, hogy minimalizálják az állásidőt adatbázis áttelepítések során, és a folyamat során működési tartja a forráshely adatbázisára.

Attunity megfelel egy replikációs eszköz, amely lehetővé teszi a különböző forrásokból és tárolók közötti adatszinkronizálás. Azt a létrehozási parancsprogrammal és az adatbázis kapcsolódó adatok propagálása zajlik. Attunity replikálás nem propagál minden egyéb összetevők (például SP, eseményindítók, Funkciók, és így tovább) vagy convert, például, a PL/SQL-kódot, amely az ilyen összetevők T-SQL.

> [!NOTE]
> Bár Attunity replikálás áttelepítési forgatókönyvek széles körét támogatja, egy adott részének forrás célgazdagép, célfelhő párokból támogatása összpontosít.

A folyamat minimális állásidővel áttelepítés áttekintését tartalmazza:

* **A MySQL-forrás séma áttelepítése** egy Azure-adatbázishoz a MySQL adatbázis-szolgáltatás használatával kezelt [MySQL munkaterület](https://www.mysql.com/products/workbench/).

* **Kezdeti betöltés és az adatok folyamatos szinkronizálásához a forrás-adatbázisból történő beállítása a céladatbázis** Attunity megfelel a Microsoft Migrations. Ezzel minimálisra csökkenti a forrásadatbázis be kell állítania csak olvashatóként Azure váltani az alkalmazásait, és a cél MySQL-adatbázis előkészítése az idejét.

A Attunity megfelel az ajánlat Microsoft Migrations kapcsolatos további információkért lásd a következőket:
 - Lépjen a [Attunity megfelel a Microsoft Migrations](https://aka.ms/attunity-replicate) weblap.
 - Töltse le [Attunity megfelel a Microsoft áttelepítések](http://discover.attunity.com/download-replicate-microsoft-lp6657.html).
 - Lépjen a [Attunity replikálása közösségi](https://aka.ms/attunity-community) első lépések útmutatója, oktatóanyagok és támogatási szolgálathoz.
 - A MySQL-adatbázis áttelepítése az Azure Database-MySQL az Attunity megfelel az lépésenkénti útmutatásért lásd: a [adatbázis áttelepítési útmutató](https://datamigration.microsoft.com/scenario/mysql-to-azuremysql).