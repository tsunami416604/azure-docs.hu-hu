---
title: "A MySQL az Azure Database-online áttelepítési |} Microsoft Docs"
description: "Ez a cikk ismerteti a MySQL egy online az Azure Database-MySQL-adatbázis áttelepítésének módjáról és a kezdeti betöltés és beállítása a folyamatos adatszinkronizálás forrásadatbázis a céladatbázis Attunity megfelel a Microsoft Migrations módjáról."
services: mysql
author: HJToland3
ms.author: jtoland
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 01/03/2018
ms.openlocfilehash: e3a30510e1b3070bc320faa6071cf546a2024e2f
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="online-migration-to-azure-database-for-mysql"></a>A MySQL az Azure Database-online áttelepítése
Áttelepítheti a meglévő MySQL-adatbázis Azure-adatbázis a MySQL Attunity megfelel a Microsoft Migrations, egy közös szponzorált, közös ajánlat Attunity és a Azure adatbázis áttelepítési szolgáltatás nem megfelelően biztosított Microsoft Microsoft-ügyfél további költség nélkül. Microsoft Migrations Attunity megfelel is lehetővé teszi, hogy az adatbázis áttelepítése az állásidő minimális, és a forrás-adatbázis továbbra is fennáll, az áttelepítési folyamat során működőképes legyen.

Attunity megfelel egy olyan adatok replikációs eszköz, amely lehetővé teszi a különböző forrásokból és célok, közötti adatszinkronizálás, propagálása a létrehozási parancsprogrammal és az adatbázis társított adatokat. Attunity replikálás nem propagál minden egyéb összetevők (például SP, eseményindítók, Funkciók, stb.) vagy convert, például ilyen összetevők, T-SQL tárolt PL/SQL-kódot.

> [!NOTE]
> Míg Attunity replikálás áttelepítési forgatókönyvek széles körét támogatja, Microsoft Migrations Attunity megfelel egy adott részének forrás célgazdagép, célfelhő párokból támogatása összpontosít.

A folyamat egy online áttelepítés áttekintését tartalmazza:

1. **A MySQL forrás áttelepítéséhez** használatával MySQL az Azure Database [MySQL munkaterület](https://www.mysql.com/products/workbench/).

2. **Kezdeti betöltés és beállítása a folyamatos adatszinkronizálás forrásadatbázis a céladatbázis** Attunity megfelel a Microsoft Migrations. Ez így minimálisra csökkenti, amely a forráshely adatbázisára kell állítani írásvédett, amíg előkészíti a Váltás az alkalmazásait, a cél MySQL-adatbázis és az Azure idejét.

Az Attunity megfelel a Microsoft Migrations ajánlat kapcsolatos további információkért tekintse meg a következőket:
 - A Microsoft Migrations Attunity replikálás [weblap](https://aka.ms/attunity-replicate).
 - [Töltse le](http://discover.attunity.com/download-replicate-microsoft-lp6657.html) Attunity megfelel a Microsoft áttelepítésre.
 - Az Attunity használatával történő MySQL az Azure-adatbázis MySQL áttelepítésének lépésenkénti útmutatásért tekintse meg a [adatbázis áttelepítési útmutató](https://datamigration.microsoft.com/scenario/mysql-to-azuremysql).