---
title: Az Azure SQL adatbázis dinamikus adatmaszkolási |} Microsoft docs
description: SQL-adatbázis dinamikus adatmaszkolási korlátozza a bizalmas adatok veszélyeztetettségének által maszkolás a jogosulatlan felhasználók számára
services: sql-database
author: ronitr
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: ronitr
ms.openlocfilehash: e5ed226c0d2f849e59abcf3b46c9ec6fb7cc679a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645249"
---
# <a name="sql-database-dynamic-data-masking"></a>SQL-adatbázis dinamikus adatmaszkolási

SQL-adatbázis dinamikus adatmaszkolási maszkolás a jogosulatlan felhasználók által bizalmas adatok veszélyeztetettségének korlátozása. 

A dinamikus adatmaszkolás azzal segít megelőzni a bizalmas adatokhoz való jogosulatlan hozzáférést, hogy az ügyfél által meghatározhatóvá teszi az alkalmazásrétegre gyakorolt minimális következményekkel felfedhető bizalmas adatok menyiségét. Ez a szabályzatalapú biztonsági funkció elrejti a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja.

Például a képviselőjével egy ügyfélszolgálatával, előfordulhat, hogy azonosíthatja a hívók a hitelkártya száma több számjegyével, de ezeket az elemeket kell nem teljesen elérhetővé tehető a munkatársának. Egy maszkolási szabályra meghatározása, hogy minden maszkok, de az eredményben bármely hitelkártyaszám utolsó négy számjegye bármely lekérdezés beállítva. Másik példaként a megfelelő adatok maszk definiálható személyes azonosításra alkalmas adatokat (PII) adatok védelmét, hogy a fejlesztő lekérdezheti a termelési környezetben hibaelhárítási célból megfelelőségi szabályzat megsértése nélkül.

## <a name="sql-database-dynamic-data-masking-basics"></a>SQL-adatbázis dinamikus adatmaszkolási alapjai
Dinamikus adatmaszkolási házirend az Azure-portálon a dinamikus adatmaszkolási műveletet az SQL-adatbázis konfigurációs panel vagy a beállítások panel kiválasztásával beállítása.

### <a name="dynamic-data-masking-permissions"></a>Dinamikus adatok maszkolása engedélyek
Dinamikus adatmaszkolási konfigurálhatja az Azure-adatbázis rendszergazdai, kiszolgálói rendszergazda vagy biztonsági tisztviselő szerepkörök.

### <a name="dynamic-data-masking-policy"></a>Dinamikus adatok maszkolása házirend
* **Az SQL-felhasználók ki zárva a maszkolásból** – SQL-felhasználók csoportja A vagy AAD identitásokat tartalmaz, amelyek látható adatok beolvasása a SQL-lekérdezés eredménye. Rendszergazdai jogosultságokkal rendelkező felhasználók vannak mindig ki vannak zárva a maszkolásból, és tekintse meg az eredeti adatok bármely maszk nélkül.
* **Szabályok maszkolás** -a kijelölt mezőket a maszkolandó meghatározó szabályok és a használt maszkolási függvényt. A kijelölt mező egy séma adatbázisnév, a tábla neve és az oszlop neve adható meg.
* **Maszkolás funkciók** -módszereket a különböző alkalmazási helyzetek adatok veszélyeztetettségének szabályozó készlete.

| Maszkolási függvényt | Maszkolási logika |
| --- | --- |
| **Alapértelmezett** |**A kijelölt mezők az adattípusok alapján teljes maszkolási**<br/><br/>• Az XXXX vagy kevesebb Xs, ha a mező mérete legalább 4 karakterből kell állnia a karakterláncos adattípusokkal (nchar, ntext, nvarchar).<br/>• A numerikus adattípusú (bigint, bit, decimal, int, pénzt, numerikus, smallint, kis pénz típusú értékké, tinyint, lebegőpontos, valós) nulla értéket használja.<br/>• Az 01-01-1900 dátum és idő adattípus (dátum, datetime2, datetime, datetimeoffset, smalldatetime, idő).<br/>• Az SQL variant, az alapértelmezett érték az aktuális típus használatos.<br/>• A dokumentum XML- <masked/> szolgál.<br/>• Különleges adattípust alkotnak üres értéket használ (időbélyeg táblázatra, hierarchyid, GUID, binary, image, varbinary térbeli típusok). |
| **Hitelkártya** |**Módszer, amely a kijelölt mezők utolsó négy számjegye maszkolás** , és hozzáadja egy állandó karakterlánc hitelkártya formájában előtagjaként.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **E-mail** |**Maszkolás metódus, amely az első betűjének mutatja, és a tartomány helyére XXX.com** egy e-mail cím egy állandó karakterlánc-előtagot használja.<br/><br/>aXX@XXXX.com |
| **Véletlenszerű számot** |**Módszer, amely véletlenszerűen generálja maszkolás** a kiválasztott határokat és a tényleges adatok típusa alapján. Ha a kijelölt határokon azonos, majd a maszkolási függvény számának állandó.<br/><br/>![Navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Egyéni szöveg** |**Módszer, amely az első és utolsó karakter maszkolás** és egy egyéni kitöltési karakterláncot hozzáadja a középső. Ha az eredeti karakterláncot rövidebb, mint a kitett előtag és utótagot, csak a kitöltési karakterláncot kell használni. <br/>[kitöltési] előtag utótag<br/><br/>![Navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Ajánlott maszkolandó mezők
A DDM javaslatok motor egyes mezőket az adatbázisból jelzők a potenciálisan bizalmas mezői, esetleg maszkolási használható jól. A dinamikus Adatmaszkolás paneljét a portálon, a látni fogja az ajánlott oszlopok az adatbázis számára. Ehhez szüksége, kattintson a **maszk hozzáadása** egy vagy több oszlopnál, majd **mentése** alkalmazni ezeket a mezőket maszkot.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>A Powershell-parancsmagok használatával adatbázis dinamikus adatmaszkolási beállítása
Lásd: [Azure SQL adatbázis parancsmagok](https://msdn.microsoft.com/library/azure/mt574084.aspx).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>A REST API használatával adatbázis dinamikus adatmaszkolási beállítása
Lásd: [műveletek az Azure SQL-adatbázisok](https://msdn.microsoft.com/library/dn505719.aspx).

