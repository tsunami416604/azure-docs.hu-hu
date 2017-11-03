---
title: "Egy kiszolgálóhoz az Azure-adatbázis visszaállítása a MySQL |} Microsoft Docs"
description: "Ez a cikk ismerteti a kiszolgáló Azure-adatbázis visszaállítása a MySQL az Azure portál használatával."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 6c1c0f8a0c0e59661b70b787b551b8cfdb024cda
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Készítsen biztonsági másolatot, és egy kiszolgálóhoz az Azure-adatbázis visszaállítása a MySQL az Azure portál használatával

## <a name="backup-happens-automatically"></a>Automatikusan megtörténik a biztonsági mentés
Amikor MySQL az Azure-adatbázist használja, az adatbázis-szolgáltatás automatikusan teszi a szolgáltatás biztonsági másolatot, ötpercenként. 

A biztonsági mentések esetén érhetők el a 7 napja alapszintű rétegben, és 35 napon Standard csomag használata esetén. További információkért lásd: [MySQL szolgáltatási szinteket az Azure-adatbázis](concepts-service-tiers.md)

Az automatikus biztonsági mentési szolgáltatás használatával állítsa vissza a kiszolgáló és az adatbázisok be egy új kiszolgálót egy korábbi-időpontban.

## <a name="restore-in-the-azure-portal"></a>Állítsa vissza az Azure-portálon
Azure MySQL-adatbázis visszaállítása a kiszolgáló egy adott időben és a teszi a kiszolgáló egy új példányát. Az új kiszolgáló használatával az adatok helyreállításához. 

Például ha egy táblázat véletlenül ma délben el lett dobva, sikerült visszaállítása előtt déltől idő és a hiányzó tábla és adatokat lekérdezni a server új példányát.

Az alábbi lépéseket a minta-kiszolgáló helyreállítása egy adott pontra időpont:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com/)

2. Keresse meg a MySQL-kiszolgálóhoz tartozó Azure-adatbázis. A bal oldali panelen válassza ki a **összes erőforrás**, és válassza ki a kiszolgálót a listából.

3.  A kiszolgáló – áttekintés panel tetején kattintson **visszaállítása** az eszköztáron. A visszaállítás panel nyílik meg.
![Visszaállítás gombra.](./media/howto-restore-server-portal/click-restore-button.png)

4. Töltse ki a visszaállítási űrlapot a szükséges adatokkal:

- **Visszaállítási pont (idő szerint UTC)**: Dátumválasztó és idő kiválasztása, válasszon ki egy pontot időben történő visszaállításához. A megadott ideje UTC formátumban, valószínűleg kell konvertálni a helyi idő (UTC).
- **Új kiszolgáló visszaállításához**: Adjon meg egy új kiszolgálónevet, a meglévő kiszolgálóról történő visszaállítására.
- **Hely**: A régió beállítása automatikusan tölti fel a forrás kiszolgálón régiót, és nem módosítható.
- **IP-címek**: árképzési szint kiválasztása automatikusan tölti fel az azonos árképzési szint a forráskiszolgáló, és az itt nem módosíthatók. 
![PITR visszaállítása](./media/howto-restore-server-portal/pitr-restore.png)

5. Kattintson a **OK** való visszaállítása a kiszolgáló a megadott idő. 

6. A visszaállítás befejezése után keresse meg az új kiszolgálóra lett létrehozva, és ellenőrizze, hogy az adatbázisok visszaállítása várt módon történt.

## <a name="next-steps"></a>Következő lépések
- [MySQL az Azure-adatbázis adatkapcsolattárak](concepts-connection-libraries.md).