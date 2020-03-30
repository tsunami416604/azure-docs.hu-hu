---
title: Lekérdezési teljesítmény – Azure Database for MySQL
description: Megtudhatja, hogyan használhatja a EXPLAIN-t a lekérdezési teljesítmény hibaelhárításához a MySQL-hez készült Azure Database-ben.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: 6b27e47339b80cc46290065c4d17150a301f2534
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067842"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mysql"></a>A EXPLAIN használata a profillekérdezés teljesítményének profilteljesítményéhez a MySQL Azure-adatbázisában
**EXPLAIN** egy praktikus eszköz, hogy optimalizálja lekérdezések. A EXPLAIN utasítás segítségével információkat kaphat az SQL-utasítások végrehajtásáról. A következő kimenet egy EXPLAIN utasítás végrehajtására mutat be példát.

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

Amint az ebben a példában látható, a *kulcs* értéke NULL. Ez a kimenet azt jelenti, hogy a MySQL nem talál a lekérdezésre optimalizált indexeket, és teljes táblavizsgálatot hajt végre. Optimalizáljuk ezt a lekérdezést egy index hozzáadásával az **Azonosító** oszlopban.

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

Az új MAGYARÁZAT azt mutatja, hogy a MySQL most egy indexet használ a sorok számának 1-re való korlátozására, ami viszont drámaian lerövidítette a keresési időt.
 
## <a name="covering-index"></a>Az index lefedése
A fedőindex az indexben lévő lekérdezés összes oszlopát foglalja magában, hogy csökkentse az adattáblákérték-lekérését. Itt egy illusztráció a következő **GROUP BY** utasítást.
 
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

Amint az a kimenetből is látható, a MySQL nem használ indexeket, mert nem állnak rendelkezésre megfelelő indexek. Azt is mutatja *használata ideiglenes; A fájlrendezés használata*, ami azt jelenti, hogy a MySQL létrehoz egy ideiglenes táblát a **GROUP BY** záradék kielégítésére.
 
Az index létrehozása a **c2** oszlopban önmagában nem számít, és a MySQL-nek még létre kell hoznia egy ideiglenes táblát:

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

Ebben az esetben **létrehozható** egy fedett index mind a **c1,** mind a **c2** esetében, amely a **c2**" értékét közvetlenül az indexben adja hozzá a további adatkeresés kiküszöbölése érdekében.

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

Amint azt a fenti EXPLAIN mutatja, a MySQL most már a fedett indexet használja, és nem hoz létre ideiglenes táblát. 

## <a name="combined-index"></a>Kombinált index
A kombinált index több oszlop értékeiből áll, és sorok tömbjének tekinthető, amelyek az indexelt oszlopok értékeinek összefűzése szerint vannak rendezve.Ez a módszer hasznos lehet a **GROUP BY** utasításban.

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

A MySQL olyan *fájlrendezési* műveletet hajt végre, amely meglehetősen lassú, különösen akkor, ha sok sort kell rendeznie. A lekérdezés optimalizálása érdekében a rendszer mindkét rendezendő oszlopban létrehozható kombinált indexet.

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

A MAGYARÁZAT most azt mutatja, hogy a MySQL képes kombinált indexet használni a további rendezés elkerülésére, mivel az index már rendezve van.
 
## <a name="conclusion"></a>Összegzés
 
A EXPLAIN és a különböző típusú indexek használata jelentősen növelheti a teljesítményt. Miután egy index az asztalon nem feltétlenül jelenti azt, MySQL képes lenne használni a lekérdezések. Mindig érvényesítse a feltételezéseket az EXPLAIN használatával, és optimalizálja a lekérdezéseket indexek használatával.


## <a name="next-steps"></a>További lépések
- Ha szeretné megtalálni a társak válaszait a leginkább érintett kérdésekre, vagy tegye az új kérdés / válasz, látogasson el [MSDN fórum](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) vagy [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
