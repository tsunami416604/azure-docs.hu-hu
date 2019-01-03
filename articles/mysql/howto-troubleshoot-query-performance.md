---
title: Hogyan háríthatók el a lekérdezési teljesítmény az Azure Database for MySQL-hez
description: Ez a cikk ismerteti a magyarázat használata a lekérdezési teljesítmény az Azure Database for MySQL-hez.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 819e2393619766d46385cdd6fe550fff1e1a7631
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53537664"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mysql"></a>MAGYARÁZAT használatával profil lekérdezési teljesítmény az Azure Database for MySQL-hez
**ISMERTETIK** egy hasznos eszköz optimalizálni a lekérdezéseket. ISMERTETIK utasítás használható SQL-utasítások végrehajtásának módját információt szeretne kapni. A következő kimenet magyarázat utasítás a végrehajtás egy példát mutat be.

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

Ebben a példában értékét is látható módon *kulcs* má hodnotu NULL. Ez a kimenet azt jelenti, hogy a MySQL nem található a lekérdezés memóriaoptimalizált indexek és a teljes tábla beolvasásával hajtja végre. Nézzük optimalizálása a lekérdezés ad hozzá egy index a **azonosító** oszlop.

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

Az új magyarázat bemutatja, hogy, hogy MySQL most már használja a index 1, ami jelentősen viszont lerövidítettük a keresési időt sorok számának korlátozására.
 
## <a name="covering-index"></a>Index kiterjedő
Egy fedi le index adattáblák értékének lekérését csökkentése érdekében az indexben a lekérdezés az összes oszlopból áll. Íme az alábbi illusztrációban **GROUP BY** utasítást.
 
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

A kimenetből látható, MySQL, mert nincs megfelelő indexek állnak rendelkezésre nem használ indexekkel. Azt is bemutatja *ideiglenes; használatával Fájl rendezés használatával*, ami azt jelenti, a MySQL teljesítéséhez egy ideiglenes táblát hoz létre a **GROUP BY** záradékban.
 
Egy index létrehozását az oszlopok **c2** nincs különbség, és a MySQL továbbra is létre kell hoznia egy ideiglenes táblát önálló teszi:

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

Ebben az esetben egy **kezelt index** mindkét **c1** és **c2** hozható létre, amellyel értékének hozzáadása **c2**"közvetlenül az indexe További adatok keresési kiküszöbölése.

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

Azt mutatja, a fenti magyarázat, mint a MySQL most a kezelt index használja, és ne hozzon létre egy ideiglenes táblát. 

## <a name="combined-index"></a>Kombinált index
Egy kombinált index érték több oszlopból áll, és sorok rendezve az indexelt oszlopok értékek összefűzésével álló tömb lehet tekinteni. Ez a módszer hasznos lehet egy **GROUP BY** utasítást.

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

MySQL hajt végre egy *fájl rendezési* művelet, amely viszonylag lassú, különösen ha rendelkezik rendezheti a sorok számát. Ez a lekérdezés optimalizálása érdekében egy kombinált index hozható létre a mindkét oszlopot, amely rendezi a rendszer.

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

A magyarázat mostantól látható, hogy MySQL kombinált index használatával elkerülheti a további rendezési, mivel az index már van rendezve.
 
## <a name="conclusion"></a>Összegzés
 
MAGYARÁZAT és más típusú indexeket teljesítménye jelentősen megnőhet. Csak, mert rendelkezik egy indexet a tábla nem feltétlenül jelenti MySQL tudná, a lekérdezések használatával. Mindig magyarázat támaszthatja a, és optimalizálhatja a lekérdezések indexekkel.


## <a name="next-steps"></a>További lépések
- A legtöbb érintett kérdésekre adott válaszok társ, vagy egy új kérdés-válasz küldése, látogasson el [MSDN-fórum](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) vagy [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
