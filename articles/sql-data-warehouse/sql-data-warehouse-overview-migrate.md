---
title: Megoldás áttelepítése az SQL Data Warehouse |} A Microsoft Docs
description: 'Áttelepítési útmutató a múlté: Azure SQL Data Warehouse platform számára.'
services: sql-data-warehouse
author: jrowlandjones
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: 04c921282d3591e7326d326c230bf72e7f5c1812
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57313021"
---
# <a name="migrate-your-solution-to-azure-sql-data-warehouse"></a>Megoldás áttelepítése az Azure SQL Data warehouse-bA
Tekintse meg az Azure SQL Data Warehouse-ba való migrálás egy meglévő adatbázis-megoldás vesz részt. 

## <a name="profile-your-workload"></a>A számítási feladatok kiértékelése
Mielőtt telepít át, szeretné végrehajtani bizonyos az SQL Data Warehouse a számítási feladathoz megoldást. Az SQL Data Warehouse egy olyan elosztott rendszer analytics végre, a nagy mennyiségű adat.  Az SQL Data Warehouse-ba való migrálás szükséges néhány tervezési módosításaival, amely nem túl keményen annak megértése, de végrehajtása hosszabb időt vehet igénybe. Ha a vállalat által igényelt egy nagyvállalati szintű data warehouse-ba, az előnyöket is, megéri-e. Azonban ha már nincs szüksége az SQL Data Warehouse hatékonyságát, SQL Server vagy az Azure SQL Database használata helyett.

Fontolja meg az SQL Data Warehouse amikor Ön:
- Egy vagy több Terabájtnyi adat rendelkezik
- A nagy mennyiségű adat elemzések futtatására megtervezése
- Méretezhető számítási és tárolási kell 
- Szeretné költségeket takaríthat felfüggesztése számítási erőforrásokat, amikor már nincs szükség rájuk.

Ne használja az SQL Data Warehouse működési (OLTP) számítási feladatokhoz, amelyek rendelkeznek:
- Nagy gyakoriságú írások és olvasások
- Egyszeres nagy számú kiválasztása
- Nagy mennyiségű, egyetlen sor beszúrása
- A soronkénti igények feldolgozása
- Inkompatibilis formátumban (JSON, XML)


## <a name="plan-the-migration"></a>Az áttelepítés tervezése

Miután eldöntötte, hogy egy meglévő megoldás áttelepítése SQL Data warehouse-ba, fontos az első lépések előtt az áttelepítés tervezése. 

Egy tervezési célja, hogy biztosítja az adatok, a táblasémákat, és a kód az SQL Data Warehouse kompatibilisek legyenek. Bizonyos kompatibilitási különbségek vannak a jelenlegi rendszer és az SQL Data Warehouse között megkerüléséhez. Ezen felül időt vesz az Azure-ba való migrálás nagy mennyiségű adat. Gondos tervezést felgyorsítja az Azure-ban adatok beolvasása. 

Egy másik tervezési célja, hogy a megoldás kihasználja az SQL Data warehouse-bA az célja, hogy adja meg a lekérdezési teljesítmény biztosítása érdekében tervezési módosításokat. Méretezési csoport data warehouse-adattárházak kialakítása vezet be a különböző kialakítási minták és így hagyományos megközelítés nem mindig a lehető legjobb. Habár néhány tervezési módosításokat az áttelepítés után, a folyamat hamarabb változtatásokat menti későbbi.

Sikeres áttelepítés végrehajtásához szüksége a táblasémákat, a kód és az adatok áttelepítéséhez. Ezek a témakörök útmutatásért lásd:

-  [A séma migrálása](sql-data-warehouse-migrate-schema.md)
-  [Kód áttelepítése](sql-data-warehouse-migrate-code.md)
-  [Az adatok áttelepítését](sql-data-warehouse-migrate-data.md). 

<!--
## Perform the migration


## Deploy the solution


## Validate the migration

-->

## <a name="next-steps"></a>További lépések
A CAT (Ügyféltanácsadói csapatának) is rendelkezik néhány nagyszerű az SQL Data Warehouse útmutatást, amely tesznek közzé, blogok keresztül.  Vessen egy pillantást a cikk [adatok áttelepítése az Azure SQL Data Warehouse a gyakorlatban] [ Migrating data to Azure SQL Data Warehouse in practice] további útmutatást az áttelepítés.

<!--Image references-->

<!--Article references-->

<!--MSDN references-->

<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-data-to-azure-sql-data-warehouse-in-practice/
