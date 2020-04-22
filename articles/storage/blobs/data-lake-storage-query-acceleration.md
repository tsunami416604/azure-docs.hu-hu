---
title: Azure Data Lake Storage lekérdezésgyorsítás (előzetes verzió)
description: A lekérdezésgyorsítás (előzetes verzió) az Azure Data Lake Storage új képessége, amely lehetővé teszi, hogy az alkalmazások és az elemzési keretrendszerek jelentősen optimalizálják az adatfeldolgozást azáltal, hogy csak a feldolgozási művelethez szükséges adatokat kérik le.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 04/21/2020
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 191a3280075403c8c5b57c5ffca1c7707d1ddb11
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771819"
---
# <a name="azure-data-lake-storage-query-acceleration-preview"></a>Azure Data Lake Storage lekérdezésgyorsítás (előzetes verzió)

A lekérdezésgyorsítás (előzetes verzió) az Azure Data Lake Storage új lehetősége, amely lehetővé teszi, hogy az alkalmazások és az elemzési keretrendszerek jelentősen optimalizálják az adatfeldolgozást azáltal, hogy csak azokat az adatokat kérik le, amelyek egy adott művelet végrehajtásához szükségesk. Ez csökkenti a tárolt adatok kritikus elemzési adatainak megszerzéséhez szükséges időt és feldolgozási teljesítményt.

> [!NOTE]
> A lekérdezésgyorsítási szolgáltatás nyilvános előzetes verzióban érhető el, és a Kanadai Közép- és Franciaország-középrégiókban érhető el. A korlátozások áttekintéséhez olvassa el az [Ismert problémák](data-lake-storage-known-issues.md) című cikket. Az előnézetbe való regisztráláshoz tekintse meg [ezt az űrlapot.](https://aka.ms/adls/qa-preview-signup)  

## <a name="overview"></a>Áttekintés

A lekérdezésgyorsítás elfogadja a szűrési *predikátumokat* és *az oszlopvetéseket,* amelyek lehetővé teszik az alkalmazások számára a sorok és oszlopok szűrését a lemezről történő adatok olvasásakor. Csak az adatok, amelyek megfelelnek a feltételeknek egy állítmány át a hálózaton keresztül az alkalmazásba. Ez csökkenti a hálózati késést és a számítási költségeket.  

Az SQL segítségével megadhatja a sorszűrő predikátumok és oszlop vetületek a lekérdezés gyorsítási kérelem. A kérelem csak egy fájlt dolgoz fel. Ezért az SQL speciális relációs szolgáltatásai, például az illesztések és az összesítések szerinti csoportosítás nem támogatottak. A lekérdezésgyorsítás támogatja a CSV és jSON formátumú adatokat az egyes kérelmek bemeneteként.

A lekérdezésgyorsítási szolgáltatás nem korlátozódik a Data Lake Storage (tárfiókok, amelyek a hierarchikus névtér engedélyezve van rajtuk). A lekérdezésgyorsítás teljesen kompatibilis a tárfiókokban lévő blobokkal, amelyeken **nincs** engedélyezve hierarchikus névtér. Ez azt jelenti, hogy a hálózati késés és a számítási költségek csökkentése, ha a már tárolt adatok blobok a tárfiókokban.

A lekérdezésgyorsítás ügyfélalkalmazásokban való használatával kapcsolatban az [Adatok szűrése az Azure Data Lake Storage lekérdezésgyorsításával](data-lake-storage-query-acceleration-how-to.md)című témakörben található.

## <a name="data-flow"></a>Adatfolyam

Az alábbi ábra azt mutatja be, hogy egy tipikus alkalmazás hogyan használja a lekérdezésgyorsítást az adatok feldolgozásához.

> [!div class="mx-imgBorder"]
> ![Lekérdezésgyorsítás – áttekintés](./media/data-lake-storage-query-acceleration/query-acceleration.png)

1. Az ügyfélalkalmazás fájladatokat kér predikátumok és oszlopvetületek megadásával.

2. A lekérdezésgyorsítás elemzi a megadott SQL-lekérdezést, és elosztja a munkát az adatok elemzésével és szűrésével.

3. A processzorok beolvassák az adatokat a lemezről, a megfelelő formátumban elemzik az adatokat, majd a megadott predikátumok és oszlopvetületek alkalmazásával szűrik az adatokat.

4. A lekérdezésgyorsítás egyesíti a válaszszegmenseket az ügyfélalkalmazásba való streameléshez.

5. Az ügyfélalkalmazás fogadja és elemzi az streamelt választ. Az alkalmazásnak nem kell további adatokat szűrnie, és közvetlenül alkalmazhatja a kívánt számítást vagy átalakítást.

## <a name="better-performance-at-a-lower-cost"></a>Jobb teljesítmény alacsonyabb költségek mellett

A lekérdezésgyorsítás optimalizálja a teljesítményt azáltal, hogy csökkenti az alkalmazás által átvitt és feldolgozott adatok mennyiségét.

Az összesített érték kiszámításához az alkalmazások általában lekérik az **összes** adatot egy fájlból, majd helyileg dolgozzák fel és szűrik az adatokat. Az elemzési számítási feladatok bemeneti/kimeneti mintáinak elemzése azt mutatja, hogy az alkalmazások általában csak az általuk olvasott adatok 20%-át igénylik egy adott számítás elvégzéséhez. Ez a statisztika akkor is igaz, ha olyan technikákat alkalmaz, mint a [partíciómetszés](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-optimize-hive-query#hive-partitioning). Ez azt jelenti, hogy az adatok 80%-a feleslegesen kerül átvitelre a hálózaton keresztül, elemezve és alkalmazások szerint szűrve. Ez a minta, amely lényegében a szükségtelen adatok eltávolítására szolgál, jelentős számítási költséggel jár.  

Annak ellenére, hogy az Azure az átviteli és a késés szempontjából is piacvezető hálózattal rendelkezik, az adatok szükségtelen átvitele a hálózaton keresztül még mindig költséges az alkalmazások teljesítménye szempontjából. A nem kívánt adatok kiszűrésével a tárolási kérelem során a lekérdezés gyorsítása kiküszöböli ezt a költséget.

Emellett a cpu-terhelés, amely szükséges a szükségtelen adatok elemzéséhez és szűréséhez, az alkalmazásnak nagyobb számú és nagyobb virtuális gépeket kell kiépítenie a munka elvégzéséhez. A számítási terhelés lekérdezésgyorsításba való átvitelével az alkalmazások jelentős költségmegtakarítást érhetnek el.

## <a name="applications-that-can-benefit-from-query-acceleration"></a>A lekérdezésgyorsítás előnyeit élvező alkalmazások

A lekérdezésgyorsítás elosztott elemzési keretrendszerekhez és adatfeldolgozó alkalmazásokhoz készült. 

Az elosztott elemzési keretrendszerek, például az Apache Spark és az Apache Hive, tartalmaznak egy tárolási absztrakciós réteget a keretrendszeren belül. Ezek a motorok olyan lekérdezésoptimalizálókat is tartalmaznak, amelyek az alapul szolgáló I/O-szolgáltatás képességeinek ismeretét is magukban foglalják a felhasználói lekérdezések optimális lekérdezési tervének meghatározásakor. Ezek a keretrendszerek kezdik integrálni a lekérdezésgyorsítást. Ennek eredményeképpen a rendszerkeretek felhasználói jobb lekérdezési késést és alacsonyabb teljes tulajdonlási költséget fognak látni anélkül, hogy módosítaniuk kellene a lekérdezéseket. 

A lekérdezésgyorsítás adatfeldolgozási alkalmazásokhoz is készült. Az ilyen típusú alkalmazások általában nagyméretű adatátalakításokat hajtanak végre, amelyek nem vezethetnek közvetlenül elemzési elemzésekhez, így nem mindig használják a létrehozott elosztott elemzési keretrendszereket. Ezek az alkalmazások gyakran közvetlenebb kapcsolatban állnak az alapul szolgáló tárolási szolgáltatással, így közvetlenül élvezhetik az olyan funkciók előnyeit, mint például a lekérdezésgyorsítás. 

Ha például egy alkalmazás integrálhatja a lekérdezésgyorsítást, olvassa el [az Adatok szűrése az Azure Data Lake Storage lekérdezésgyorsításhasználatával című témakört.](data-lake-storage-query-acceleration-how-to.md)

## <a name="pricing"></a>Díjszabás

Az Azure Data Lake Storage szolgáltatás megnövekedett számítási terhelése miatt a lekérdezésgyorsítás használatának díjszabási modellje eltér a normál Azure Data Lake Storage tranzakciós modelltől. A lekérdezés gyorsítása a beolvasott adatok mennyiségének költségét, valamint a hívónak visszaadott adatok költségét számítja fel.

A számlázási modell módosítása ellenére a Query acceleration díjszabási modellje úgy van kialakítva, hogy csökkentse a számítási feladatok teljes tulajdonlási költségét, tekintettel a sokkal drágább virtuális gép költségeinek csökkenésére.

## <a name="next-steps"></a>További lépések

- [Lekérdezésgyorsítás igénylési képernyő](https://aka.ms/adls/qa-preview-signup)    
- [Adatok szűrése az Azure Data Lake Storage lekérdezésgyorsításával](data-lake-storage-query-acceleration-how-to.md)
- [Lekérdezésgyorsítás SQL nyelvi hivatkozás (előzetes verzió)](query-acceleration-sql-reference.md)
- Lekérdezésgyorsítás REST API-hivatkozása



