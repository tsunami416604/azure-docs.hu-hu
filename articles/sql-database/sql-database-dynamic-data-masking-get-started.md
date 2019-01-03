---
title: Az Azure SQL Database dinamikus adatmaszkolása |} A Microsoft docs
description: Az SQL Database dinamikus adatmaszkolása korlátozza a bizalmas adatok adatmaszkolás segít Önnek nem kiemelt jogosultságú felhasználók által
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 12/16/2018
ms.openlocfilehash: 3e807033b109b8281057f6881a315f5c1c783a22
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53536372"
---
# <a name="sql-database-dynamic-data-masking"></a>Az SQL Database dinamikus adatmaszkolás

Az SQL Database dinamikus adatmaszkolása korlátozza a bizalmas adatok adatmaszkolás segít Önnek nem kiemelt jogosultságú felhasználók által. 

A dinamikus adatmaszkolás azzal segít megelőzni a bizalmas adatokhoz való jogosulatlan hozzáférést, hogy az ügyfél által meghatározhatóvá teszi az alkalmazásrétegre gyakorolt minimális következményekkel felfedhető bizalmas adatok menyiségét. Ez a szabályzatalapú biztonsági funkció elrejti a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja.

Például egy telefonos szolgáltatás képviselővel előfordulhat, hogy azonosíthatja a hívók a hitelkártya száma több számjegy, de ezeket az elemeket nem teljes mértékben láthatók a munkatársának. Maszkolási szabály, hogy az összes maszkokkal, de az eredményben minden olyan hitelkártyaszám utolsó négy számjegye beállítása minden lekérdezés lehet definiálni. Másik példaként a megfelelő adatok maszk lehet definiálni személyes azonosításra alkalmas adatokat (PII) adatok védelme érdekében, hogy a fejlesztő lekérdezheti az éles környezetekre vonatkozó hibaelhárítási célból megfelelőségi szabályzat megsértése nélkül.

## <a name="sql-database-dynamic-data-masking-basics"></a>Az SQL Database dinamikus adatmaszkolási alapjai
A dinamikus adatmaszkolás az Azure Portalon a házirend a dinamikus adatmaszkolás az SQL Database konfigurálása panel vagy a beállítások panelen művelet kiválasztásával állíthatja.

### <a name="dynamic-data-masking-permissions"></a>Dinamikus adatok maszkolási engedélyek
Dinamikus adatmaszkolás az Azure Database-rendszergazda, a kiszolgálói rendszergazda, konfigurálható vagy [SQL biztonságkezelő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) szerepköröket.

### <a name="dynamic-data-masking-policy"></a>Dinamikus adatok maszkolási házirend
* **SQL-felhasználók ki vannak zárva a maszkolásból** – A készlet, SQL-felhasználók vagy az AAD-identitások, amelyek a maszkolásukat megszüntetni adatok beolvasása az SQL-lekérdezés eredményei. Rendszergazdai jogosultságokkal rendelkező felhasználók mindig ki vannak zárva a maszkolásból, és tekintse meg az eredeti adatok összes maszkja nélkül.
* **Maszkolási szabályok** – a kijelölt mezőket maszkolva meghatározó szabályok és a használt maszkolási függvényt. A kijelölt mezőket egy adatbázis-séma neve, a tábla neve és az oszlop neve lehet definiálni.
* **Maszkolási függvény** -adatokat a különböző helyzetekhez kitettségének szabályozó olyan készletéhez.

| Maszkolási függvényt | Maszkolási logika |
| --- | --- |
| **Alapértelmezett** |**Teljes maszkolási megfelelően a kijelölt mezők adattípusai**<br/><br/>• A XXXX vagy kevesebb Xs, ha a mező mérete 4-nél kevesebb karaktert adattípusokkal (nchar, ntext, nvarchar).<br/>• A numerikus adattípusokról (bigint, bit, decimal, int, pénzt, numerikus, smallint, pénz, tinyint, lebegőpontos, valós) nulla érték használata.<br/>• A 01-01-1900 dátum/idő adattípusok (dátum, datetime2, datetime, datetimeoffset, smalldatetime, ideje).<br/>• Az SQL-változatot, az alapértelmezett érték az aktuális típusú szolgál.<br/>• A dokumentum az XML <masked/> szolgál.<br/>• Az üres érték használata különleges adattípust (timestamp táblára, hierarchyid, GUID, bináris, kép, varbinary térbeli típusok). |
| **Hitelkártya** |**Maszkolási módszer, amely a kijelölt mezők utolsó négy számjegye** , és hozzáadja egy konstans sztring formájában, hitelkártyával előtagjaként.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **E-mail** |**Maszkolási metódussal, amely elérhetővé teszi az első, és a tartomány helyére XXX.com** egy e-mail-cím formájában a konstans sztring előtag használatával.<br/><br/>aXX@XXXX.com |
| **Véletlenszerű szám** |**Maszkolási metódussal, amely létrehoz egy véletlenszerű számot** a kiválasztott határokat és a tényleges adattípusok alapján. Ha a kijelölt határokon azonos, a maszkolási függvényt az a konstans szám.<br/><br/>![Navigációs ablak](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Egyéni szöveg** |**Maszkolási módszer, amely az első és utolsó karaktere** és a egy egyéni kitöltés karakterláncot hozzáadja a középső. Ha az eredeti karakterláncot rövidebb, mint a közzétett előtag és utótagot, csak a kitöltő karakterlánc szolgál. <br/>előtag [kitöltés] utótag<br/><br/>![Navigációs ablak](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Ajánlott maszkolandó mezők
A DDM ajánlatok motor megőrzendő tartalomként jelöli meg az adatbázis egyes mezőit potenciálisan bizalmas a mezők, amelyek lehetnek a deduplikációra maszkolási. A dinamikus Adatmaszkolás paneljét a portálon megtekintheti az adatbázis a javasolt oszlopok. Ehhez szüksége, kattintson a **maszk hozzáadása** egy vagy több oszlopnál, majd **mentése** maszk ezeket a mezőket a alkalmazni.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Az adatbázis Powershell-parancsmagok használatával dinamikus adatmaszkolás beállítása
Lásd: [az Azure SQL Database-parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.sql).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Állítsa be a dinamikus adatmaszkolás az adatbázis, a REST API használatával
Lásd: [műveleteket az Azure SQL Database](https://msdn.microsoft.com/library/dn505719.aspx).

