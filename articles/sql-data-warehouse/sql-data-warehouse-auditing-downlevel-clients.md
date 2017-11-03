---
title: "Az SQL Data Warehouse Alsószintű ügyfelek támogatása adatok naplózását |} Microsoft Docs"
description: "További tudnivalók az SQL Data Warehouse a régebbi típusú ügyfelek támogatása adatok naplózása"
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: dfe29ff3-dfeb-4309-83c0-c1a300f4f44e
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: security
ms.date: 10/31/2016
ms.author: rortloff;barbkess
ms.openlocfilehash: a7ea6141285a0098339f1e071af2592dd4535c12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="sql-data-warehouse----downlevel-clients-support-for-auditing-and-dynamic-data-masking"></a>Az SQL Data Warehouse - a régebbi típusú ügyfeleknek naplózási és dinamikus Adatmaszkolási támogatása
[Naplózás](sql-data-warehouse-auditing-overview.md) TDS átirányítást támogató SQL-ügyfelek működik.

Bármely olyan ügyfél, amely TDS 7.4 kell is támogatja az átirányítást. A kivételek közé tartozik a JDBC 4.0-s verzióját, amelyben az átirányítás nem teljes mértékben támogatja, és a Node.JS mely funkcióhoz Tedious nem lett megvalósítva.

Az "Alsószintű ügyfelek" azaz mely támogatási TDS verzió 7.3 és az alacsonyabb – a kiszolgáló teljes Tartománynevét a kapcsolat-karakterlánc kell módosítani:

A kapcsolódási karakterláncban eredeti kiszolgálójának teljes Tartományneve: <*kiszolgálónév*>. database.windows.net

A kapcsolati karakterláncban a módosított kiszolgálójának teljes Tartományneve: <*kiszolgálónév*> .database. **biztonságos**. windows.net

"A régebbi típusú ügyfeleknek" részleges listáját tartalmazza:

* A .NET 4.0-s vagy régebbi verzió,
* ODBC 10.0-s vagy régebbi verzió.
* JDBC (JDBC támogatja a TDS 7.4, a TDS-átirányítási funkció még nem teljes mértékben támogatott)
* (A Node.JS) fárasztó

**Megjegyzés:** lehet, hogy a fenti kiszolgáló FQDN módosítását is hasznos egy SQL Server szint naplózási házirend alkalmazása nélkül konfiguráció szükséges lépést az egyes adatbázisok (ideiglenes megoldás).     

