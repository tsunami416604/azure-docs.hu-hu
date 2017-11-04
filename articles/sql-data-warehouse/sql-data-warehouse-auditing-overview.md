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
ms.date: 08/21/2017
ms.author: rortloff;barbkess
ms.openlocfilehash: f851c82ebeaa647f663d499a4d327c3479e36121
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse naplózás
> [!div class="op_single_selector"]
> * [Naplózás](sql-data-warehouse-auditing-overview.md)
> * [Fenyegetések észlelése](sql-data-warehouse-security-threat-detection.md)
> 
> 

Az SQL Data Warehouse naplózás segítségével az adatbázist a naplózási események jelentkezzen be az Azure Storage-fiók bejegyzéshez. Naplózás segít törvényi megfelelőség fenntartásában, ismerje meg adatbázis-tevékenység, és azok az eltérések és rendellenességek, amelyek üzleti problémát jelenthetnek, vagy a biztonság megsértésére betekintést. Az SQL Data Warehouse naplózását is integrálható a Microsoft Power BI leásási jelentéskészítés és elemzés céljára.

A naplózási eszközök engedélyezése, és lehetővé teszi a megfelelést a megfelelőségi követelményeket, de nem garantálják megfelelőségi. További információ az Azure programokat, hogy támogatási szabványoknak való megfelelés, a következő témakörben: a <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Azure biztonsági és adatkezelési központ</a>.

* [Adatbázis naplózási alapjai]
* [Az adatbázis naplózásának beállítása]
* [Elemezze a vizsgálati naplók és jelentések]

## <a id="subheading-1"></a>Az Azure SQL Data Warehouse Database Auditing alapjai
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

A naplózási házirend meghatározása egy adott adatbázis vagy az alapértelmezett házirend-kiszolgáló. Kiszolgáló alapértelmezett naplózási házirend vonatkozik egy kiszolgálón lévő összes adatbázis, amely nem rendelkezik egy adott felülbíráló adatbázis naplózási házirend lett meghatározva.

Jelölőnégyzet naplózás használata naplózási beállítása előtt egy ["Régebbi típusú ügyfelekhez."](sql-data-warehouse-auditing-downlevel-clients.md)

## <a id="subheading-2"></a>Az adatbázis naplózásának beállítása
1. Indítsa el a <a href="https://portal.azure.com" target="_blank">Azure-portálon</a>.
2. Lépjen a **beállítások** a naplózni kívánt SQL Data warehouse panelre. Az a **beállítások** panelen válassza **naplózási & Threat detection**.
   
    ![][1]
3. Következő lépésként engedélyezze a nyomkövetést oly módon, kattintson a **ON** gombra.
   
    ![][3]
4. A naplózási konfiguráció paneljén válassza **tárolási részletek** naplózási naplók tárolási panel megnyitásához. Válassza ki az Azure storage-fiók naplók menteni és, a megőrzési időn. 
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

1. A naplózási konfiguráció panelen (a szakasz naplózásának beállítása fent leírt) kapcsoló a **Tárelérési kulcs** a *elsődleges* való *másodlagos* és **mentése**.

   ![][4]
2. Nyissa meg a tárolási konfiguráció paneljét és **újragenerálja** a *elsődleges elérési kulcsot*.
3. Lépjen vissza a naplózási konfiguráció blade, kapcsoló a **Tárelérési kulcs** a *másodlagos* való *elsődleges* nyomja le az ENTER **mentése**.
4. Lépjen vissza a felhasználói felület tárolására és **újragenerálja** a *másodlagos elérési kulcsot* (mint a következő előkészítése kulcsok a frissítés.

## <a id="subheading-5"></a>Automatizálási (PowerShell/REST API)
A következő automation eszközök használatával az Azure SQL Data Warehouse naplózását is konfigurálhatja:

* **PowerShell-parancsmagok**:

   * [Get-AzureRMSqlDatabaseAuditingPolicy][101]
   * [Get-AzureRMSqlServerAuditingPolicy][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditingPolicy][105]
   * [Set-AzureRMSqlServerAuditingPolicy][106]
   * [Használjon-AzureRMSqlServerAuditingPolicy][107]

<!--Anchors-->
[Adatbázis naplózási alapjai]: #subheading-1
[Az adatbázis naplózásának beállítása]: #subheading-2
[Elemezze a vizsgálati naplók és jelentések]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png


<!--Link references-->
[101]: /powershell/module/azurerm.sql/get-azurermsqldatabaseauditingpolicy
[102]: /powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditingPolicy
[103]: /powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing
[104]: /powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing
[105]: /powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditingPolicy
[106]: /powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditingPolicy
[107]: /powershell/module/azurerm.sql/Use-AzureRMSqlServerAuditingPolicy