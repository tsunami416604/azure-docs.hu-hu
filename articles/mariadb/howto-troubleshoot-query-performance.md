---
title: Lekérdezési teljesítmény – Azure Database for MariaDB
description: Megtudhatja, hogy miként háríthatja el a EXPLAIN segítségével a lekérdezési teljesítmény hibaelhárítását a MariaDB Azure Database szolgáltatásában.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: b06fe37b63494eb4ee0ca680733a801c26415d67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530053"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mariadb"></a>A EXPLAIN használata a Profillekérdezés teljesítményének profilteljesítményéhez a MariaDB Azure Database-ben
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

Amint az ebben a példában látható, a *kulcs* értéke NULL. Ez a kimenet azt jelenti, hogy a MariaDB nem talál a lekérdezésre optimalizált indexeket, és teljes táblavizsgálatot hajt végre. Optimalizáljuk ezt a lekérdezést egy index hozzáadásával az **Azonosító** oszlopban.

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

Az új MAGYARÁZAT azt mutatja, hogy a MariaDB most egy indexet használ a sorok számának 1-re való korlátozására, ami viszont drámaian lerövidítette a keresési időt.
 
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

Amint az a kimenetből is látható, a MariaDB nem használ indexeket, mert nincsenek megfelelő indexek. Azt is mutatja *használata ideiglenes; A fájlrendezés használatával*a MariaDB ideiglenes táblát hoz létre a **GROUP BY** záradék teljesítéséhez.
 
Az index létrehozása csak a **c2** oszlopban nem számít, és a MariaDB-nek továbbra is létre kell hoznia egy ideiglenes táblát:

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

Amint azt a fenti EXPLAIN mutatja, a MariaDB most antól a fedett indexet használja, és nem hoz létre ideiglenes táblát. 

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

A MariaDB meglehetősen lassú *fájlrendezési* műveletet hajt végre, különösen akkor, ha sok sort kell rendeznie. A lekérdezés optimalizálása érdekében a rendszer mindkét rendezendő oszlopban létrehozható kombinált indexet.

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

A MAGYARÁZAT most azt mutatja, hogy a MariaDB képes kombinált indexet használni a további rendezés elkerülésére, mivel az index már rendezve van.
 
## <a name="conclusion"></a>Összegzés
 
A EXPLAIN és a különböző típusú indexek használata jelentősen növelheti a teljesítményt. Az index a táblázatban nem feltétlenül jelenti azt, hogy a MariaDB használhatja azt a lekérdezésekhez. Mindig érvényesítse a feltételezéseket az EXPLAIN használatával, és optimalizálja a lekérdezéseket indexek használatával.

## <a name="next-steps"></a>További lépések
- Ha szeretné megtalálni a társak válaszait a leginkább érintett kérdésekre, vagy tegye az új kérdés / válasz, látogasson el [MSDN fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDatabaseforMariadb) vagy [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mariadb).
