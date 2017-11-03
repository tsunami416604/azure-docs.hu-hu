---
title: "Megoldás migrálása az SQL Data Warehouse |} Microsoft Docs"
description: "Áttelepítési útmutató az Azure SQL Data Warehouse platform, hogy a megoldás."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 198365eb-7451-4222-b99c-d1d9ef687f1b
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 06/27/2017
ms.author: joeyong;barbkess
ms.openlocfilehash: 771b9456e66b8a1e41f72340b695b19e2adaf793
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-your-solution-to-azure-sql-data-warehouse"></a>Megoldás migrálása az Azure SQL Data Warehouse
Tekintse meg a meglévő adatbázis megoldás áttelepítése az Azure SQL Data Warehouse szerepet játszanak. 

## <a name="profile-your-workload"></a>Profilhoz, a számítási feladatok
Az áttelepítést, mielőtt kívánt legyen egyes SQL Data Warehouse a munkaterheléshez ideális megoldás. Az SQL Data Warehouse egy olyan elosztott rendszer készült elemzés végrehajtásához, nagy.  Az SQL Data Warehouse áttelepítése igényel az egyes módosításokat nem túl merevlemez annak megértése, de eltarthat egy ideig megvalósításához. Ha a vállalat által igényelt egy vállalati szintű data warehouse-ba, a következő előnyöket is megéri. Azonban az SQL Data Warehouse power nincs szüksége, esetén költséghatékonyabb SQL Server vagy az Azure SQL Database használatához.

Fontolja meg az SQL Data Warehouse amikor Ön:
- Rendelkezik egy vagy több terabájtos adatkészleteket
- Szeretné futtatni az analytics a nagy mennyiségű adat
- Képes számítási és tárolási méretek kell 
- Szeretné költségeket takaríthat felfüggesztése számítási erőforrásokat, amikor már nincs szükség.

Ne használjon az SQL Data Warehouse működési (OLTP) munkaterhelések esetén, amelyek rendelkeznek:
- Nagy gyakoriságú olvasása és írása
- Egypéldányos nagy számú kiválasztása
- Egyetlen sor beszúrása jelentős mennyiségű
- A soronkénti igényeinek feldolgozása
- Inkompatibilis formátumban (JSON, XML)


## <a name="plan-the-migration"></a>Az áttelepítés tervezése

Miután eldöntötte, hogy egy meglévő megoldás áttelepítéséhez az SQL Data Warehouse, fontos tervezze meg az áttelepítés elkezdése előtt. 

Egy tervezési célja az adatok, a táblasémákat és a kód kompatibilisek-e az SQL Data Warehouse. Bizonyos kompatibilitási különbségek vannak a jelenlegi rendszer és az SQL Data Warehouse közötti megoldása. Áttelepíti, valamint időt Azure vesz nagy mennyiségű adat. Gondosan meg kell tervezni gyorsítja olvashatja be adatait az Azure-bA. 

Egy másik tervezési célja annak érdekében, hogy a megoldás kihasználja a célja, hogy az SQL Data Warehouse biztosításához a lekérdezési teljesítmény tervezési módosításokra. Az adatraktárak bővített tervezése vezet be a különböző kialakítási minták és így hagyományos módszerekkel nem mindig a legjobb. Bár bizonyos tervezési módosításokra áttelepítés után, a folyamat hamarabb módosítása menti a későbbi.

A sikeres áttelepítéshez szüksége a táblasémákat, a kódot és az adatok áttelepítéséhez. Az alábbi áttelepítési témakörök útmutatást lásd:

-  [Telepítse át a sémák](sql-data-warehouse-migrate-schema.md)
-  [Telepítse át a kódot](sql-data-warehouse-migrate-code.md)
-  [Adatok áttelepítése](sql-data-warehouse-migrate-data.md). 

<!--
## Perform the migration


## Deploy the solution


## Validate the migration

-->

## <a name="next-steps"></a>Következő lépések
A CAT (Ügyféltanácsadói csapatának) is rendelkezik néhány nagy az SQL Data Warehouse útmutatást, amelyek ezek teszik közzé a blogok keresztül.  Tekintse meg a cikk [adatok áttelepítése az Azure SQL Data Warehouse a gyakorlatban] [ Migrating data to Azure SQL Data Warehouse in practice] további útmutatást nyújt az áttelepítési.

<!--Image references-->

<!--Article references-->

<!--MSDN references-->

<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
