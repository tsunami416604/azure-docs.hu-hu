---
title: Lekérdezési címkék használata a szinapszis SQL-ben
description: Ebben a cikkben a lekérdezési címkék a szinapszis SQL-ben való használatához szükséges tippek találhatók.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 0b2c03c5c7ea8c65cb1cde3cbdb73b6bb838dc06
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324466"
---
# <a name="use-query-labels-in-synapse-sql"></a>Lekérdezési címkék használata a szinapszis SQL-ben

Ebben a cikkben a lekérdezési címkék a szinapszis SQL-ben való használatához szükséges tippek találhatók.

> [!NOTE]
> A kiszolgáló nélküli SQL-készlet (előzetes verzió) nem támogatja a címkézési lekérdezéseket.

## <a name="what-are-query-labels"></a>A lekérdezési címkék ismertetése

A dedikált SQL-készlet támogatja a lekérdezési címkék nevű koncepciót. Mielőtt bármilyen mélységbe kerül, tekintsük át a következő példát:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Az utolsó sorban a "saját lekérdezés" karakterláncot kell a lekérdezéshez felcímkézni. Ez a címke hasznos lehet, mivel a címke lekérdezéssel képes a DMV. A címkék lekérdezése lehetővé teszi a problémák lekérdezését, és segít azonosítani a folyamatot egy ELT futtatásával.

A helyes elnevezési konvenciók hasznosak. Ha például a címkét a projekt, eljárás, utasítás vagy Megjegyzés alapján indítja el, egyedi módon azonosítja a lekérdezést a verziókövetés összes kódja között.

A következő lekérdezés dinamikus felügyeleti nézetet használ a címke szerinti kereséshez:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> A lekérdezés során elengedhetetlen, hogy szögletes zárójeleket vagy idézőjeleket helyezzen el a szó címkéje körül. A címke egy foglalt szó, és hibát okoz, ha nincs elválasztva. 
> 
> 

## <a name="next-steps"></a>Következő lépések
További fejlesztési tippek: a [fejlesztés áttekintése](develop-overview.md).


