---
title: "Egy kiszolgálóhoz az Azure-adatbázis visszaállítása a PostgreSQL |} Microsoft Docs"
description: "Ez a cikk ismerteti az Azure portál használatával PostgreSQL egy kiszolgálóhoz az Azure-adatbázis visszaállítása."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: 903fd2ff446e1963ab5cfcec745766188b74efcf
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-portal"></a>Biztonsági mentése és visszaállítása egy kiszolgálóhoz az Azure-adatbázis az Azure portál használatával PostgreSQL

## <a name="backup-happens-automatically"></a>Automatikusan megtörténik a biztonsági mentés
Ha Azure-adatbázis a PostgreSQL, az adatbázis-szolgáltatás automatikusan lehetővé teszi a kiszolgáló biztonsági másolatának 5 percenként. 

A biztonsági mentések esetén érhetők el a 7 napja alapszintű rétegben, és 35 napon Standard csomag használata esetén. További információkért lásd: [PostgreSQL szolgáltatási szinteket az Azure-adatbázis](concepts-service-tiers.md)

Az automatikus biztonsági mentési szolgáltatás használatával állítsa vissza a kiszolgáló és az adatbázisok be egy új kiszolgálót egy korábbi-időpontban.

## <a name="restore-in-the-azure-portal"></a>Állítsa vissza az Azure-portálon
Azure PostgreSQL számára lehetővé teszi az visszaállítása a kiszolgáló vissza egy időben, és azokat a kiszolgáló egy új példányát. Az új kiszolgáló használatával az adatok helyreállításához. 

Például ha egy táblázat véletlenül dobva délben ma, akkor sikerült visszaállítása előtt déltől idő, a hiányzó tábla és adatokat lekérdezni az új példányt a kiszolgáló.

Az alábbi lépéseket a minta-kiszolgáló helyreállítása pontra időpont:
1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com/)
2. Keresse meg az Azure-adatbázis PostgreSQL-kiszolgáló. Az Azure portálon kattintson **összes erőforrás** a bal oldali menüből és a kiszolgáló nevében típus például **mypgserver-20170401**, a meglévő kiszolgáló kereséséhez. Kattintson a keresési eredményekben listázott kiszolgálónévre. Megnyílik a kiszolgáló **Áttekintés** oldala, amely további konfigurációs lehetőségeket biztosít.

   ![Azure portál – keresse meg a kiszolgáló](media/postgresql-howto-restore-server-portal/1-locate.png)

3. A kiszolgáló – áttekintés oldalra eszköztárán kattintson **visszaállítása**. A Helyreállítás lap nyílik meg.

   ![Azure-adatbázis PostgreSQL - áttekintése - Visszaállítás gomb](./media/postgresql-howto-restore-server-portal/2_server.png)

4. Töltse ki a visszaállítási űrlapot a szükséges adatokkal:

   ![PostgreSQL - visszaállítási információk az Azure-adatbázis ](./media/postgresql-howto-restore-server-portal/3_restore.png)
  - **Visszaállítási pont**: Válasszon egy pont időponthoz kötött, amely akkor fordul elő, mielőtt a kiszolgáló megváltozott.
  - **Célkiszolgáló**: Adjon meg egy új, a visszaállítani kívánt kiszolgálónevet.
  - **Hely**: a régió nem választhatók ki. Alapértelmezés szerint az ugyanaz, mint a forráskiszolgálón.
  - **A tarifacsomag**: Ez az érték nem módosítható, ha egy kiszolgáló visszaállítása. Ugyanaz, mint a forráskiszolgálón. 

5. Kattintson a **OK** visszaállítása a kiszolgáló egy időben visszaállítása. 

6. Miután a visszaállítás befejezését, keresse meg az új kiszolgálóra, amely az adatok helyreállt a várt módon ellenőrzése jön létre.

## <a name="next-steps"></a>Következő lépések
- [Adatkapcsolattárak PostgreSQL Azure-adatbázis](concepts-connection-libraries.md)
