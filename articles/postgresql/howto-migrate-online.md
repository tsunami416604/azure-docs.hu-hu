---
title: "Az Azure Database-PostgreSQL online áttelepítés |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan által PostgreSQL-adatbázisból csomagolja memóriaképfájl, a PostgreSQL-adatbázis visszaállítása Azure adatbázisban PostgreSQL, valamint beállítja a kezdeti betöltés pg_dump által létrehozott és folyamatos archív fájl online áttelepítése Adatszinkronizálás forrásadatbázis Attunity megfelel a Microsoft Migrations a cél-adatbázishoz."
services: postgresql
author: HJToland3
ms.author: jtoland
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 01/03/2018
ms.openlocfilehash: cf17ad87b33ed6196d86cebaf6efe699a542bf39
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="online-migration-to-azure-database-for-postgresql"></a>Az Azure Database-PostgreSQL online áttelepítés
Áttelepítheti a meglévő PostgreSQL-adatbázisról Azure Database PostgreSQL az Attunity megfelel a Microsoft Migrations, egy közös szponzorált, közös ajánlat Attunity és az Azure-adatbázis áttelepítése szolgáltatás megfelelően biztosított Microsoft minden Microsoft-ügyfél számára további költség nélkül. Microsoft Migrations Attunity megfelel is lehetővé teszi, hogy az adatbázis áttelepítése az állásidő minimális, és a forrás-adatbázis továbbra is fennáll, az áttelepítési folyamat során működőképes legyen.

Attunity megfelel egy olyan adatok replikációs eszköz, amely lehetővé teszi a különböző forrásokból és célok, közötti adatszinkronizálás, propagálása a létrehozási parancsprogrammal és az adatbázis társított adatokat. Attunity replikálás nem propagál minden egyéb összetevők (például SP, eseményindítók, Funkciók, stb.) vagy convert, például ilyen összetevők, T-SQL tárolt PL/SQL-kódot.

> [!NOTE]
> Míg Attunity replikálás áttelepítési forgatókönyvek széles körét támogatja, Microsoft Migrations Attunity megfelel egy adott részének forrás célgazdagép, célfelhő párokból támogatása összpontosít.

A folyamat egy online áttelepítés áttekintését tartalmazza:

1. **Telepítse át a PostgreSQL-forrás séma** PostgreSQL használatával az Azure-adatbázishoz [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) és [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) - n paraméterrel.

2. **Kezdeti betöltés és beállítása a folyamatos adatszinkronizálás forrásadatbázis a céladatbázis** Attunity megfelel a Microsoft Migrations. Ezzel így minimálisra csökkenti, amely a forráshely adatbázisára kell állítani írásvédett, amíg előkészíti a Váltás az alkalmazások a céladatbázis PostgreSQL Azure idejét.

Az Attunity megfelel a Microsoft Migrations ajánlat kapcsolatos további információkért tekintse meg a következőket:
 - A Microsoft Migrations Attunity replikálás [weblap](https://aka.ms/attunity-replicate).
 - [Töltse le](http://discover.attunity.com/download-replicate-microsoft-lp6657.html) Attunity megfelel a Microsoft áttelepítésre.
 - Az Attunity használatával történő PostgreSQL az Azure-adatbázis PostgreSQL áttelepítésének lépésenkénti útmutatásért tekintse meg a [adatbázis áttelepítési útmutató](https://datamigration.microsoft.com/scenario/postgresql-to-azurepostgresql).