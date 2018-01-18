---
title: "Az Azure SQL Data Warehouse naplózása |} Microsoft Docs"
description: "Ismerkedés az Azure SQL Data Warehouse naplózás"
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: 0e6af148-b218-4b43-bb5f-907917d20330
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: security
ms.date: 01/16/2018
ms.author: rortloff;barbkess
ms.openlocfilehash: 5400f29d8c7579809ef7b2a084115473df7baa85
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2018
---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse naplózás

Az SQL Data Warehouse naplózás segítségével az adatbázist a naplózási események jelentkezzen be az Azure Storage-fiók bejegyzéshez. Naplózás segít törvényi megfelelőség fenntartásában, ismerje meg adatbázis-tevékenység, és azok az eltérések és rendellenességek, amelyek üzleti problémát jelenthetnek, vagy a biztonság megsértésére betekintést. Az SQL Data Warehouse naplózását is integrálható a Microsoft Power BI jelentéskészítés és elemzés céljára.

A naplózási eszközök engedélyezése, és lehetővé teszi a megfelelést a megfelelőségi követelményeket, de nem garantálják megfelelőségi. További információ az Azure programokat, hogy támogatási szabványoknak való megfelelés, a következő témakörben: a <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Azure biztonsági és adatkezelési központ</a>.

## <a id="subheading-1"></a>Naplózási alapjai
Az SQL Data Warehouse-adatbázis naplózásának teszi lehetővé:

* **Tartsa meg** kijelölt események egy naplóban. Adatbázis-műveleteket kell naplózni kategóriáinak adhat meg.
* **A jelentés** adatbázis-tevékenység. Előre konfigurált jelentések és egy irányítópult segítségével gyorsan Ismerkedés a tevékenységet és az események naplózásához.
* **Elemezze** jelentéseket. Gyanús események, a szokatlan tevékenység és a trendeket találja.

Konfigurálhatja a naplózás a következő kategóriák:

**Egyszerű SQL** és **paraméteres SQL** , amelynek a gyűjtött naplók besorolt  

* **Adatokhoz való hozzáférés**
* **Sémaváltozások (DDL)**
* **Adatok módosításait (DML)**
* **Fiókok, szerepkörök és engedélyeket (DCL)**
* **Tárolt eljárás**, **bejelentkezési** , és **tranzakció felügyeleti**.

Minden esemény kategóriához a naplózási **sikeres** és **hiba** műveleteket külön-külön vannak konfigurálva.

A tevékenységek és a naplózott események kapcsolatos további információkért tekintse meg a <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">naplózási napló fájlformátum referenciája (doc fájl letöltése)</a>.

Naplók az Azure storage-fiókok vannak tárolva. Megadhatja, hogy az ellenőrzési napló megőrzési időtartam.

Meghatározhatja az adott adatbázishoz, vagy az alapértelmezett házirend-kiszolgáló a naplózási házirend. Kiszolgáló alapértelmezett naplózási házirend vonatkozik egy kiszolgálón lévő összes olyan adatbázis, amely nem rendelkezik egy adott felülbíráló adatbázis naplózási házirend lett meghatározva.

Jelölőnégyzet naplózás használata naplózási beállítása előtt egy ["Régebbi típusú ügyfelekhez."](sql-data-warehouse-auditing-downlevel-clients.md)

## <a id="subheading-2"></a>Az adatbázis naplózásának beállítása
1. Indítsa el a <a href="https://portal.azure.com" target="_blank">Azure-portálon</a>.
2. Ugrás a **beállítások** a naplózni kívánt SQL-adatraktár. Válassza ki **naplózási & Threat detection**.
   
    ![][1]
3. Következő lépésként engedélyezze a nyomkövetést oly módon, kattintson a **ON** gombra.
   
    ![][3]
4. Jelölje ki a naplózási konfiguráció panelen **tárolási részletek** a vizsgálati naplók tárolási panel megnyitásához. Válassza ki a naplókat, és a megőrzési idő az Azure storage-fiókjához. 
>[!TIP]
>Ugyanazt a tárfiókot az összes naplózott adatbázis segítségével a legtöbbet hozhatja ki az előre konfigurált jelentések sablonokat.
   
    ![][4]
5. Kattintson a **OK** gombra kattintva mentse a tárkonfiguráció részleteit.
6. A **-NAPLÓZÁS által esemény**, kattintson a **sikeres** és **hiba** lehetőséget az összes esemény naplózása, vagy az egyes kategóriák.
7. Naplózási adatbázis beállításakor, szükség lehet megváltoztatni a kapcsolati karakterlánc az ügyfél annak érdekében, hogy megfelelően rögzített adatok naplózását. Ellenőrizze a [módosíthatja a kiszolgáló teljes Tartománynevet a kapcsolódási karakterláncban](sql-data-warehouse-auditing-downlevel-clients.md) témakör korábbi verziójú ügyfél-kommunikációhoz.
8. Kattintson az **OK** gombra.

## <a id="subheading-3"></a>Elemezze a vizsgálati naplók és jelentések
Naplófájlok tárolási táblákon, amelyekhez a gyűjtemény összesítése egy **SQLDBAuditLogs** a telepítéskor választott Azure-tárfiók-előtagot. Megtekintheti a naplófájlok egy eszközzel, mint <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Azure Tártallózó</a>.

Előre konfigurált irányítópult jelentéssablon áll rendelkezésre, mert egy <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">letölthető Excel-táblázat</a> naplóadatokat gyorsan elemzéséhez. A naplók a sablon használatához kell Excel 2013 vagy újabb verzió és a Power Query, amely letölthető <a href="http://www.microsoft.com/download/details.aspx?id=39379">Itt</a>.

A sablon a képzeletbeli mintaadatok rendelkezik, és a napló közvetlenül importálása az Azure-tárfiókot Power Query állíthat be.

## <a id="subheading-4"></a>Tárolási kulcs újragenerálása
Éles akkor valószínűleg a kulcsok rendszeresen frissíteni. Ha a kulcsok frissítése, kell menteni a házirendet. A folyamat a következőképpen történik:

1. A naplózás konfigurációs panelen, amelyet az előző beállítások szakasz naplózását, módosítsa a **Tárelérési kulcs** a *elsődleges* való *másodlagos* és  **MENTÉS**.

   ![][4]
2. Nyissa meg a tárolási konfiguráció panelre és **újragenerálja** a *elsődleges elérési kulcsot*.
3. Lépjen vissza a naplózási konfiguráció panel 
4. Váltás a **Tárelérési kulcs** a *másodlagos* való *elsődleges* nyomja le az ENTER **mentése**.
4. Lépjen vissza a felhasználói felület tárolására és **újragenerálja** a *másodlagos elérési kulcsot* (mint a következő előkészítése kulcsok a frissítés.

## <a id="subheading-5"></a>Automatizálási (PowerShell/REST API)
A következő automation eszközök használatával az Azure SQL Data Warehouse naplózását is konfigurálhatja:

* **PowerShell-parancsmagok**:

   * [Get-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/get-azurermsqldatabaseauditingpolicy)
   * [Get-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditingPolicy)
   * [Remove-AzureRMSqlDatabaseAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing)
   * [Remove-AzureRMSqlServerAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing)
   * [Set-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditingPolicy)
   * [Set-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditingPolicy)
   * [Use-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Use-AzureRMSqlServerAuditingPolicy)


## <a name="downlevel-clients-support-for-auditing-and-dynamic-data-masking"></a>A régebbi típusú ügyfeleknek naplózási és dinamikus adatmaszkolási támogatása
SQL-ügyfelek számára, amely támogatja a TDS-átirányítás naplózási együttműködik.

Bármely ügyfél, amely megvalósítja a TDS 7.4 kell is támogatja az átirányítást. A kivételek közé tartozik a JDBC 4.0-s verzióját, amelyben az átirányítás nem teljes mértékben támogatja, és a Node.JS mely funkcióhoz Tedious nem lett megvalósítva.

Az "Alsószintű ügyfelek", amely támogatja a TDS 7.3-as verzió és az alábbi módosíthatja a kiszolgáló teljes Tartománynevét a kapcsolódási karakterláncban az alábbiak szerint:

- A kapcsolódási karakterláncban eredeti kiszolgálójának teljes Tartományneve: <*kiszolgálónév*>. database.windows.net
- A kapcsolati karakterláncban a módosított kiszolgálójának teljes Tartományneve: <*kiszolgálónév*> .database. **biztonságos**. windows.net

"A régebbi típusú ügyfeleknek" részleges listáját tartalmazza:

* A .NET 4.0-s vagy régebbi verzió,
* ODBC 10.0-s vagy régebbi verzió.
* JDBC (JDBC támogatja a TDS 7.4, a TDS-átirányítási funkció még nem teljes mértékben támogatott)
* (A Node.JS) fárasztó

**Megjegyzés:** lehet, hogy az előző kiszolgáló FQDN módosítását is hasznos egy SQL Server szint naplózási házirend alkalmazása nélkül konfiguráció szükséges lépést az egyes adatbázisok (ideiglenes megoldás).     




<!--Anchors-->
[Database Auditing basics]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png


