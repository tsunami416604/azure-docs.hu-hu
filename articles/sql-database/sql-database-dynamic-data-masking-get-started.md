---
title: Dinamikus adatmaszkolás
description: A dinamikus adatmaszkolás korlátozza a bizalmas adatokkal való érintkezést azáltal, hogy a SQL Database és az Azure szinapszis számára nem Kiemelt felhasználók számára maszkolást végez
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/06/2020
tags: azure-synpase
ms.openlocfilehash: e5b281d59245d8fbd32b18f4ac5fe577fc7ff309
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78192914"
---
# <a name="dynamic-data-masking-for-azure-sql-database-and-azure-synapse-analytics"></a>Dinamikus adatmaszkolás a Azure SQL Database és az Azure szinapszis Analytics számára

SQL Database dinamikus adatmaszkolás korlátozza a bizalmas adatokkal való érintkezést azáltal, hogy a nem Kiemelt felhasználók számára maszkolást végez. 

A dinamikus adatmaszkolás azzal segít megelőzni a bizalmas adatokhoz való jogosulatlan hozzáférést, hogy az ügyfél által meghatározhatóvá teszi az alkalmazásrétegre gyakorolt minimális következményekkel felfedhető bizalmas adatok menyiségét. Ez a szabályzatalapú biztonsági funkció elrejti a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja.

Előfordulhat például, hogy a Call Center egyik szolgáltatása a hívókat a hitelkártyaszám több számjegye alapján azonosítja, de ezeket az adatelemeket nem szabad teljes mértékben kitenni a szolgáltatás képviselőjének. A maszkolási szabályok határozzák meg, hogy a rendszer az összes, de az utolsó négy számjegyet elrejti a bármely lekérdezés eredmény-készletében. Egy másik példaként egy megfelelő adatmaszkot is meghatározhat a személyazonosításra alkalmas adatok (személyes adatok) adatainak védelme érdekében, így a fejlesztők a megfelelőségi rendeletek megszegése nélkül is lekérhetik a termelési környezeteket a hibaelhárítási célból.

## <a name="dynamic-data-masking-basics"></a>Dinamikus adatmaszkolás alapjai

A dinamikus adatmaszkolási szabályzatot a Azure Portal a dinamikus adatmaszkolási művelet kiválasztásával a SQL Database konfiguráció paneljén vagy a beállítások panelen. Ez a funkció nem állítható be az Azure szinapszis portál használatával (használja a PowerShellt vagy a REST API)

### <a name="dynamic-data-masking-permissions"></a>Dinamikus adatmaszkolási engedélyek

A dinamikus adatmaszkolás konfigurálható a Azure SQL Database-rendszergazda, a kiszolgálói rendszergazda vagy az [SQL Security Manager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) szerepköreivel.

### <a name="dynamic-data-masking-policy"></a>Dinamikus adatmaszkolási szabályzat

* A **maszkolásból kizárt SQL-felhasználók** – olyan SQL-felhasználók vagy HRE-identitások összessége, amelyek az SQL-lekérdezés eredményeiben nem maszkolt adatokkal rendelkeznek. A rendszergazdai jogosultságokkal rendelkező felhasználók mindig ki vannak zárva a maszkolásból, és az eredeti adatok maszk nélkül jelennek meg.
* **Maszkolási szabályok** – a kijelölt mezőket és a használni kívánt maszkolási függvényt meghatározó szabályok halmaza. A kijelölt mezők az adatbázis-séma neve, a tábla neve és az oszlop neve alapján definiálhatók.
* **Maszkolási függvények** – olyan metódusok összessége, amelyek különböző forgatókönyvek esetében szabályozzák az adatok kitettségét.

| Maszkolási függvény | Maszkolási logika |
| --- | --- |
| **Alapértelmezett** |**Teljes maszkolás a kijelölt mezők adattípusainak megfelelően**<br/><br/>• Az XXXX vagy kevesebb XS használata, ha a mező mérete kisebb, mint 4 karakter a sztring adattípusok esetében (NCHAR, ntext, nvarchar).<br/>• Nulla értéket használhat a numerikus adattípusokhoz (bigint, bit, decimális, int, Money, numerikus, smallint, túlcsordulási, tinyint, float, Real).<br/>• Az 01-01-1900-as dátum/idő adattípusok (dátum, datetime2, datetime, DateTimeOffset, idő adattípusúra, Time) használata.<br/>• Az SQL Variant esetében az aktuális típus alapértelmezett értéke lesz használatban.<br/>• Az XML esetében a \<maszkolt/> a dokumentum.<br/>• Üres értéket használhat a speciális adattípusokhoz (timestamp-tábla, hierarchyid, GUID, Binary, képek, varbinary térbeli típusok). |
| **Bankkártya** |**Maszkolási módszer, amely a kijelölt mezők utolsó négy számjegyét teszi elérhetővé** , és egy konstans karakterláncot hoz létre egy hitelkártya formájában.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **E-mail** |**Maszkolási módszer, amely az első betűt teszi elérhetővé, és lecseréli a tartományt a xxx.com** egy állandó karakterlánc-előtaggal e-mail-cím formájában.<br/><br/>aXX@XXXX.com |
| **Véletlenszerű szám** |**Maszkolási módszer, amely** a kiválasztott határok és a tényleges adattípusok alapján véletlenszerűen generált számot hoz létre. Ha a kijelölt határok egyenlőek, akkor a maszkolási függvény állandó szám.<br/><br/>![navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Egyéni szöveg** |**Maszkolási módszer, amely az első és az utolsó karaktert teszi elérhetővé** , és egy egyéni kitöltési karakterláncot helyez el a közepén. Ha az eredeti sztring rövidebb a megjelenő előtagnál és utótagnál, csak a kitöltés karakterláncot használja a rendszer. <br/>előtag [kitöltés] utótag<br/><br/>![navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Maszk ajánlott mezői

A DDM-javaslatok motorja az adatbázis egyes mezőit potenciálisan bizalmas mezőként adja meg, ami valószínűleg a maszkoláshoz is jó választás lehet. A portál dinamikus adatmaszkolás paneljén látni fogja az adatbázis ajánlott oszlopait. Mindössze annyit kell tennie, hogy a **maszk hozzáadása** egy vagy több oszlophoz lehetőséget választja, majd a **Mentés** gombra kattint a mezőkhöz tartozó maszk alkalmazásához.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Dinamikus adatmaszkolás beállítása az adatbázishoz PowerShell-parancsmagok használatával

Lásd: [Azure SQL Database parancsmagok](https://docs.microsoft.com/powershell/module/az.sql).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Dinamikus adatmaszkolás beállítása az adatbázishoz REST API használatával

Tekintse [meg Azure SQL Database műveleteit](https://docs.microsoft.com/rest/api/sql/).
