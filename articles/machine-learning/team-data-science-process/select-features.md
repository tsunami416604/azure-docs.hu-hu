---
title: Szolgáltatás kiválasztása a csapatadat-elemzési folyamatában
description: Ismerteti a szolgáltatás kiválasztásának célját, és példákat mutat be a gépi tanulás adatjavítási folyamatában betöltött szerepükre.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1127a470a48660ffffa892d24c9f2991ec64c8e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76716682"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Funkcióválasztás a csoportos adatelemzési folyamatban (TDSP)
Ez a cikk ismerteti a szolgáltatás kiválasztásának céljait, és példákat mutat be a gépi tanulás adatjavítási folyamatában betöltött szerepére. Ezek a példák az Azure Machine Learning Studio-ból származnak.

A mérnöki és a kiválasztott funkciók egy része a Team Data Science Process (TDSP) vázolt cikkben [Mi a csapat adatelemzési folyamat?](overview.md). A funkciótervezés és a kiválasztás a TDSP **Funkciók fejlesztése** lépésének részét képezi.

* **funkciótervezés:** Ez a folyamat további releváns funkciókat próbál létrehozni az adatok meglévő nyers funkcióiból, és növelni a tanulási algoritmus prediktív erejét.
* **funkciókiválasztása**: Ez a folyamat kiválasztja az eredeti adatfunkciók legfontosabb részhalmazát, hogy csökkentse a betanítási probléma méretdimenzionális jellegét.

Általában **a szolgáltatásmérnöki** először további funkciók létrehozásához, majd a **szolgáltatás kiválasztási** lépés történik, hogy megszüntesse irreleváns, redundáns, vagy erősen korrelált funkciók.

## <a name="filter-features-from-your-data---feature-selection"></a>Szolgáltatások szűrése az adatokból - funkcióválasztás
A funkciókiválasztása osztályozási vagy regressziós feladatokhoz használható. A cél a szolgáltatások egy részhalmazának kiválasztása az eredeti adatkészletből, amely minimális szolgáltatáskészlethasználatával csökkenti a méreteit az adatok maximális varianciájának jelölésére. A jellemzők ezen részhalmaza a modell betanítására szolgál. A funkcióválasztás két fő célt szolgál.

* Először is, a szolgáltatás kiválasztása gyakran növeli a besorolás pontosságát azáltal, hogy megszünteti a lényegtelen, redundáns vagy erősen korrelált funkciók kiküszöbölése.
* Másodszor, csökkenti a funkciók számát, ami hatékonyabbá teszi a modell betanítási folyamatát. A hatékonyság fontos a tanulók számára, akiknek drága a képzése, mint például a támogató vektorgépek.

Bár a szolgáltatáskiválasztása célja, hogy csökkentse a modell betanításához használt adatkészlet funkcióinak számát, a "méretcsökkentés" kifejezés nem hivatkozik rá. A szolgáltatáskijelölési módszerek az adatok eredeti jellemzőinek egy részét kinyerik anélkül, hogy módosítanák azokat.  Dimenzincia csökkentésmódszerek foglalkoztat mesterséges funkciók, amelyek átalakítják az eredeti funkciókat, és így módosíthatja azokat. A méretcsökkentési módszerek közé tartozik például a fő összetevőelemzés, a kanonikus korrelációs elemzés és az egyes érték-decomposition.

Többek között a felügyelt környezetben alkalmazott funkciókiválasztási módszerek egyik széles körben alkalmazott kategóriáját "szűrőalapú szolgáltatásválasztásnak" nevezzük. Az egyes funkciók és a célattribútum közötti korreláció kiértékelésével ezek a módszerek statisztikai mértéket alkalmaznak az egyes funkciókhoz való pontszám hozzárendeléséhez. A funkciók ezután rangsorolják a pontszám, amely segítségével állítsa be a küszöbértéket tartani vagy kiküszöbölni egy adott funkció. Az ezekben a módszerekben alkalmazott statisztikai intézkedések közé tartozik például a személykorreláció, a kölcsönös információ és a Chi squared teszt.

Az Azure Machine Learning Studio-ban vannak olyan modulok, amelyek a szolgáltatás kiválasztása. Az alábbi ábrán látható módon ezek a modulok [tartalmazzák a szűrőalapú funkcióválasztást][filter-based-feature-selection] és a [Fisher-lineáris diszkrimináns elemzést.][fisher-linear-discriminant-analysis]

![Szolgáltatásválasztó modulok](./media/select-features/feature-Selection.png)

Vegyük például a [szűrőalapú szolgáltatáskijelölési][filter-based-feature-selection] modul használatát. A kényelem érdekében továbbra is használja a szövegbányászati példát. Tegyük fel, hogy egy 256 szolgáltatásból álló készlet létrehozása után szeretne regressziós modellt létrehozni a [Szolgáltatáskivonat-gyűjtés][feature-hashing] modulon keresztül, és hogy a válaszváltozó a "Col1", amely 1 és 5 közötti könyvminősítéseket tartalmaz. Ha a "Szolgáltatáspontozási módszert" "Pearson-korreláció"-ra, a "Cél oszlopot" pedig "Col1"-re, a "Kívánt funkciók száma" pedig 50-re állítja. Ezután a modul [szűrőalapú szolgáltatásválasztása][filter-based-feature-selection] 50 funkciót tartalmazó adatkészletet hoz létre a "Col1" célattribútummal együtt. Az alábbi ábra a kísérlet folyamatát és a bemeneti paramétereket mutatja be:

![Szűrőalapú szolgáltatáskijelölési modul tulajdonságai](./media/select-features/feature-Selection1.png)

Az alábbi ábra az eredményül kapott adatkészleteket mutatja be:

![A szűrőalapú szolgáltatáskijelölési modul eredményül kapott adatkészlete](./media/select-features/feature-Selection2.png)

Minden egyes funkció pontozott a pearsoni korreláció alapján, amely maga és a "Col1" célattribútum alapján történik. A funkciók a legjobb pontszámok at tartják.

A kiválasztott jellemzők megfelelő pontszámai az alábbi ábrán láthatók:

![A szűrőalapú szolgáltatáskijelölési modul kottapontjai](./media/select-features/feature-Selection3.png)

A [szűrőalapú szolgáltatásválasztó][filter-based-feature-selection] modul alkalmazásával a 256 funkcióból 50 van kiválasztva, mert a "Col1" célváltozóval a leginkább korrelált funkciókkal rendelkeznek, a "Pearson korreláció" pontozási módszer alapján.

## <a name="conclusion"></a>Összegzés
A szolgáltatástervezés és a szolgáltatáskiválasztása két általánosan megtervezett és kiválasztott funkció, amelyek növelik a betanítási folyamat hatékonyságát, amely megpróbálja kinyerni az adatokban található legfontosabb információkat. Emellett javítják ezeknek a modelleknek a hatalmát a bemeneti adatok pontos besorolásához és az érdeklődésre számot tartó eredmények erőteljesebb előrejelzéséhez. A funkciótervezés és a kiválasztás kombinálható, hogy a tanulás takarásban kezelhetőbb legyen. Ezt úgy éri el, hogy javítja, majd csökkenti a modell kalibrálásához vagy betanításához szükséges funkciók számát. Matematikailag szólva a modell betanításához kiválasztott funkciók a független változók minimális készletei, amelyek elmagyarázzák az adatok mintáit, majd sikeresen előrejelzik az eredményeket.

Ez nem mindig feltétlenül hajtsa végre a szolgáltatás mérnöki vagy funkció kiválasztása. Az, hogy szükség van-e rá, az összegyűjtött adatoktól, a kiválasztott algoritmustól és a kísérlet céljától függ.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/

