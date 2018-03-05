---
title: "A MySQL az Azure-adatbázis a lekérdezési teljesítmény hibaelhárítása"
description: "A cikk ismerteti a magyarázat használata, hogy a MySQL az Azure-adatbázisban a lekérdezések teljesítményét."
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 3af6ad347cec171132ddfbec21137775c0f71245
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mysql"></a>MAGYARÁZAT profil lekérdezési teljesítmény az Azure-adatbázisban a MySQL használata
**MAGYARÁZÓ** hasznos eszköz optimalizálni a lekérdezéseket. MAGYARÁZÓ utasítás használható SQL-utasítások végrehajtásának módját kapcsolatos információkat. A következő kimeneti magyarázat utasítás a végrehajtás példáját mutatja be.

```sql
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 10.00
        Extra: Using where
```

Ebben a példában a értékének is látható módon *kulcs* null értékű. A kimeneti azt jelenti, hogy MySQL indexekkel optimalizálva a lekérdezés nem található, és egy tábla teljes vizsgálatot végez. Most optimalizálhatja a lekérdezés index hozzáadásával a **azonosító** oszlop.

```sql
mysql> ALTER TABLE tb1 ADD KEY (id);
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ref
possible_keys: id
          key: id
      key_len: 4
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
```

Az új magyarázat jeleníti meg, hogy MySQL most indexszel 1, ami viszont jelentős mértékben csökkenteni a keresési időt sorok számának korlátozása.
 
## <a name="covering-index"></a>Index kiterjedő
Az index értékének lekérését adattáblák csökkentése érdekében a lekérdezés minden oszlop egy fedi le index áll. Ez az alábbi ábrát **GROUP BY** utasítást.
 
```sql
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

A kimenetből látható, mert MySQL használ indexekkel, mert nincs megfelelő indexek állnak rendelkezésre. Azt is bemutatja, *használatával ideiglenes; Fájl rendezés használatával*, ami azt jelenti, MySQL ideiglenes táblát hoz létre kielégítéséhez a **GROUP BY** záradékban.
 
Egy index létrehozását oszlop **c2** nincs különbség, és a MySQL továbbra is létre kell hoznia egy ideiglenes tábla önmagában teszi:

```sql 
mysql> ALTER TABLE tb1 ADD KEY (c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

Ebben az esetben egy **kezelt index** mindkét **c1** és **c2** létrehozhatók, amelyek segítségével a értéke **c2**"közvetlenül a indexe További adatok keresési megszüntetéséhez.

```sql 
mysql> ALTER TABLE tb1 ADD KEY covered(c1,c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: covered
          key: covered
      key_len: 108
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using index
```

Ahogy a fenti magyarázat látható, a MySQL most az érintett index, és ne hozzon létre egy ideiglenes tábla. 

## <a name="combined-index"></a>Kombinált index
A kombinált index több oszlop értékeit tartalmazza, és lehet tekinteni a sorokat rendezi a rendszer az indexelt oszlopok értékek összefűzésével tömbjét. Ez a módszer hasznos lehet egy **GROUP BY** utasítást.

```sql
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using filesort
```

MySQL hajt végre egy *fájl rendezési* művelet, amely viszonylag lassú, különösen ha rendelkezik sorok rendezéséhez. Ez a lekérdezés optimalizálása érdekében a kombinált index mindkét oszlopokon, hogy rendezve hozható létre.

```sql 
mysql> ALTER TABLE tb1 ADD KEY my_sort2 (c1, c2);
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: NULL
          key: my_sort2
      key_len: 108
          ref: NULL
         rows: 10
     filtered: 11.11
        Extra: Using where; Using index
```

A magyarázat most mutatja, hogy MySQL kombinált index használatával elkerülheti a további rendezés, mert az index szerint lett rendezve.
 
## <a name="conclusion"></a>Összegzés
 
MAGYARÁZAT és más típusú indexek használatával növelheti a teljesítményt jelentősen. Pusztán azért, mert az index a tábla nem feltétlenül jelenti azt MySQL lenne a lekérdezések használni tudja. Mindig a magyarázat használatával Előfeltételek ellenőrzése és a lekérdezések használatával indexek optimalizálása.


## <a name="next-steps"></a>További lépések
- A legtöbb érintett kérdésekre adott válaszok társ, vagy egy új kérdés-válasz utáni, látogasson el a [MSDN fórum](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) vagy [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
