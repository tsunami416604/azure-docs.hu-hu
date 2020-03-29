---
title: SQL-átalakítás alkalmazása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja az SQL Transformation modul t az Azure Machine Learning ben egy SQLite lekérdezés futtatásához a bemeneti adatkészleteken az adatok átalakításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 2e44a4861e2522b766aab9c7151d76c471dd2d8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314538"
---
# <a name="apply-sql-transformation"></a>SQL-átalakítás alkalmazása

Ez a cikk ismerteti az Azure Machine Learning designer modulját (előzetes verzió).

Az SQL Transformation modul alkalmazása segítségével a következőket teheti:
  
-   Táblákat hozhat létre az eredményekhez, és mentheti az adatkészleteket egy hordozható adatbázisba.  
  
-   Egyéni átalakításokat hajtson végre adattípusokon, vagy hozzon létre összesítéseket.  
  
-   SQL-lekérdezési utasítások végrehajtása az adatok szűréséhez vagy módosításához, és a lekérdezés eredményének adattáblaként való visszaadása.  

> [!IMPORTANT]
> A modulban használt SQL motor az **SQLite**. Az SQLite szintaxisról további információt az [SQL as SQLite című](https://www.sqlite.org/index.html) témakörben talál további információért.  

## <a name="how-to-configure-apply-sql-transformation"></a>Az SQL-átalakítás alkalmazása konfigurálása  

A modul bemenetként legfeljebb három adatkészletet vehet igénybe. Amikor az egyes bemeneti portokhoz kapcsolódó adatkészletekre `t1` `t2`hivatkozunk, a nevét a és a `t3`nevét kell használnia. A táblaszám a bemeneti port indexét jelzi.  
  
A fennmaradó paraméter egy SQL-lekérdezés, amely az SQLite szintaxist használja. Ha több sort ír be az **SQL Script** szövegmezőbe, használjon pontosvesszőt az egyes utasítások leállításához. Ellenkező esetben a sortörések szóközökké alakulnak át.  

Ez a modul támogatja az SQLite szintaxis összes szabványos utasítását. A nem támogatott utasítások listáját a Műszaki megjegyzések szakaszban [található.](#technical-notes)

##  <a name="technical-notes"></a>Technikai megjegyzések  

Ez a szakasz a megvalósítás részleteit, tippjeit és a gyakran feltett kérdésekre adott válaszokat tartalmazza.

-   Az 1-es porton mindig szükség van egy bevitelre.  
  
-   Szóközt vagy más speciális karaktereket tartalmazó oszlopazonosítók esetén mindig tegyünk szögletes zárójelbe vagy `SELECT` idézőjelek közé, amikor a vagy `WHERE` záradékoszlopra hivatkozunk.  
  
### <a name="unsupported-statements"></a>Nem támogatott utasítások  

Bár az SQLite támogatja az ANSI SQL szabvány nagy részét, nem tartalmaz számos olyan funkciót, amelyet kereskedelmi relációs adatbázis-rendszerek támogatnak. További információ: [SQL as Stood by SQLite](http://www.sqlite.org/lang.html). Az SQL utasítások létrehozásakor vegye figyelembe az alábbi korlátozásokat is:  
  
- Az SQLite dinamikus gépelést használ az értékekhez, ahelyett, hogy egy típust hozzárendelne egy oszlophoz, mint a legtöbb relációs adatbázis-rendszerben. Gyengén gépelt, és implicit típusú konverziót tesz lehetővé.  
  
- `LEFT OUTER JOIN`végre, de `RIGHT OUTER JOIN` nem `FULL OUTER JOIN`vagy .  

- `RENAME TABLE` Használhatja és `ADD COLUMN` a `ALTER TABLE` parancs parancsait használhatja, de `DROP COLUMN`más `ALTER COLUMN`záradékok nem támogatottak, beleértve a , a és `ADD CONSTRAINT`a .  
  
- Az SQLite nézetet létrehozhatja, de ezt követően a nézetek csak olvashatók. Nézeten nem `DELETE` `INSERT`hajtható `UPDATE` végre , vagy utasítás. Létrehozhat azonban egy eseményindítót, amely `DELETE`a `INSERT`, `UPDATE` vagy egy nézetre, és az eseményindító törzsében más műveletekvégrehajtására hajt végre.  
  

A hivatalos SQLite oldalon található nem támogatott funkciók listáján kívül a következő wiki tartalmazza az egyéb nem támogatott funkciók listáját: [SQLite - Nem támogatott SQL](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
