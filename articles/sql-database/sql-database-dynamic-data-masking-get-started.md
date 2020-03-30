---
title: Dinamikus adatmaszkolás
description: A dinamikus adatmaszkolás korlátozza a bizalmas adatexpozíciót azáltal, hogy elfedi azt a nem kiemelt jogosultságú felhasználók számára az SQL Database és az Azure Synapse számára
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192914"
---
# <a name="dynamic-data-masking-for-azure-sql-database-and-azure-synapse-analytics"></a>Dinamikus adatmaszkolás az Azure SQL Database és az Azure Synapse Analytics számára

Az SQL Database dinamikus adatmaszkolása korlátozza a bizalmas adatok nak való kitettséget azáltal, hogy nem emelt szintű jogosultsággal rendelkező felhasználók számára maszkolja azt. 

A dinamikus adatmaszkolás azzal segít megelőzni a bizalmas adatokhoz való jogosulatlan hozzáférést, hogy az ügyfél által meghatározhatóvá teszi az alkalmazásrétegre gyakorolt minimális következményekkel felfedhető bizalmas adatok menyiségét. Ez a szabályzatalapú biztonsági funkció elrejti a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja.

Előfordulhat például, hogy egy telefonos ügyfélszolgálati képviselő a hívókat a hitelkártyaszámának több számjegyével azonosítja, de ezeket az adatelemeket nem szabad teljes mértékben kitenni a szolgáltatás képviselőjének. Olyan maszkolási szabály definiálható, amely bármely lekérdezés eredményhalmazában a hitelkártyaszám utolsó négy számjegyét kivételével az összes elfedi. Egy másik példa, egy megfelelő adatmaszk definiálható a személyazonosításra alkalmas adatok (PII) adatainak védelmére, hogy a fejlesztő hibaelhárítási célokból lekérdezhesse az éles környezeteket hibaelhárítási célokra.

## <a name="dynamic-data-masking-basics"></a>A dinamikus adatmaszkolás alapjai

Dinamikus adatmaszkolási szabályzatot állíthat be az Azure Portalon az SQL Database konfigurációs panelen vagy a beállítások panelen a dinamikus adatmaszkolási művelet kiválasztásával. Ez a funkció nem állítható be az Azure Synapse portálhasználatával (használja a Powershellt vagy a REST API-t)

### <a name="dynamic-data-masking-permissions"></a>Dinamikus adatmaszkolási engedélyek

A dinamikus adatmaszkolást az Azure SQL Database rendszergazda, a kiszolgáló rendszergazdája vagy az [SQL Security Manager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) szerepkörök konfigurálhatják.

### <a name="dynamic-data-masking-policy"></a>Dinamikus adatmaszkolási házirend

* **SQL-felhasználók ki zárva maszkolás** – Sql-felhasználók vagy AAD-identitások, amelyek maszkolatlan adatokat kap az SQL-lekérdezés eredményei. A rendszergazdai jogosultságokkal rendelkező felhasználók mindig ki vannak zárva a maszkolásból, és maszk nélkül láthatják az eredeti adatokat.
* **Maszkolási szabályok** – A maszkolandó kijelölt mezőket és a maszkolandó maszkolási funkciót meghatározó szabálykészlet. A kijelölt mezők adatbázisséma nevével, táblanevével és oszlopnevével definiálhatók.
* **Maszkolási függvények** – Olyan módszerek készlete, amelyek a különböző forgatókönyvek adatainak expozícióját szabályozzák.

| Maszkolás i | Maszkolási logika |
| --- | --- |
| **Alapértelmezett** |**Teljes maszkolás a kijelölt mezők adattípusai szerint**<br/><br/>• XXXX vagy kevesebb X s-t használjon, ha a mező mérete 4 karakternél kisebb a karakterlánc-adattípusoknál (nchar, ntext, nvarchar).<br/>• Használjon nulla értéket numerikus adattípusokhoz (bigint, bit, decimális, int, pénz, numerikus, smallint, smallmoney, tinyint, float, real).<br/>• Használja 01-01-1900 dátum / idő adattípusok (dátum, datetime2, datetime, datetimeoffset, smalldatetime, idő).<br/>• Az SQL változatoknál az aktuális típus alapértelmezett értéke használatos.<br/>• XML esetén \<a maszkolt/> dokumentum használatos.<br/>• Használjon üres értéket speciális adattípusokhoz (időbélyeg tábla, hierarchyid, GUID, bináris, kép, varbinary térbeli típusok). |
| **Hitelkártya** |**Maszkolási módszer, amely a kijelölt mezők utolsó négy számjegyét teszi elérhetővé,** és egy állandó karakterláncot ad hozzá előtagként hitelkártya formájában.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **E-mail** |**Maszkolási módszer, amely felfedi az első betűt, és lecseréli a tartományt XXX.com** egy állandó karakterlánc-előtag használatával e-mail cím formájában.<br/><br/>aXX@XXXX.com |
| **Véletlen szám** |**Maszkolási módszer, amely véletlenszerű számot hoz létre** a kiválasztott határok és a tényleges adattípusok szerint. Ha a kijelölt határok egyenlőek, akkor a maszkolási funkció állandó szám.<br/><br/>![Navigációs panel](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Egyéni szöveg** |**Maszkolási módszer, amely az első és az utolsó karaktert teszi elérhetővé,** és egy egyéni kitöltési karakterláncot ad hozzá a közepén. Ha az eredeti karakterlánc rövidebb, mint a kitett előtag és utótag, csak a kitöltési karakterlánc lesz használatban. <br/>előtag[padding]utótag<br/><br/>![Navigációs panel](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>A maszkolni javasolt mezők

A DDM ajánlási motorja az adatbázis bizonyos mezőit potenciálisan bizalmas mezőkként jelölmeg, amelyek jó jelöltek lehetnek a maszkoláshoz. A dinamikus adatmaszkolás panelen a portálon, látni fogja az adatbázis ajánlott oszlopait. Mindössze annyit kell tennie, hogy kattintson a **Maszk hozzáadása** egy vagy több oszlophoz, majd a **Mentés gombra** kattintva maszkot alkalmazhat ezekre a mezőkre.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Dinamikus adatmaszkolás beállítása az adatbázishoz PowerShell-parancsmagokkal

Lásd: [Azure SQL Database parancsmagok.](https://docs.microsoft.com/powershell/module/az.sql)

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Dinamikus adatmaszkolás beállítása az adatbázishoz a REST API használatával

Lásd: [Operations for Azure SQL Database](https://docs.microsoft.com/rest/api/sql/).
