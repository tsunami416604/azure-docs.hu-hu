---
title: "Data Factory használati eset - termék javaslatok"
description: "További tudnivalók az Azure Data Factory használatával más szolgáltatásokkal együtt használati eset."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 6f1523c7-46c3-4b8d-9ed6-b847ae5ec4ae
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 04504d1e32243f752e488a24e04ec5ba73fbadc1
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="use-case---product-recommendations"></a>Használati eset – Termékajánlások
Az Azure Data Factory a Cortana Intelligence Suite a megoldás gyorsítók végrehajtásához használt sok szolgáltatások egyike.  Lásd: [Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics) lap ennek a programcsomagnak vonatkozó további információért. Ebben a dokumentumban azt ismertetik, amelyek az Azure felhasználók már lehet megoldani, és Azure Data Factory és az egyéb Cortana Intelligence Komponensszolgáltatások készletével megvalósított gyakori használati eset.

## <a name="scenario"></a>Forgatókönyv
Online kiskereskedőktől gyakran szeretne rávenni a megvásárolt termékek feltehetően érdekli, és ezért valószínűleg megvásárlása termékekkel bemutatásával az ügyfeleknek. Ennek megvalósítása érdekében online kiskereskedőktől kell, hogy az adott felhasználó személyre szabott termék javaslatok segítségével online a felhasználói élmény testre. Ezek a személyre szabott javaslatok még az aktuális és korábbi vásárlás viselkedési adatokat, termékinformációk, újonnan bevezetett márkákat és termék- és felhasználói szegmentálást adatok alapján történik.  Emellett azok megadhatják a felhasználó termék ajánlások összes felhasználóik kombinált összesített használati viselkedés elemzése alapján.

Ezek a kiskereskedőktől célja felhasználói kattintson pénztári átalakítás esetén optimalizálása és vett szolgáltatás érvényessége alatt magasabb értékesítési bevétel.  Környezetfüggő, viselkedés alapú termék ajánlásainkat ügyfél érdeklődési és műveletek mivoltát elérésének az átalakításhoz. A használati eset, az online kiskereskedőktől vállalatok számára, az ügyfelek számára optimalizálása szeretne példát vesszük. Az alapelvek azonban bármely üzleti, hogy az ügyfelek a termékek és szolgáltatások köré és az ügyfelek vásárló élmény személyre szabott termék ajánlásokkal próbál alkalmazni.

## <a name="challenges"></a>Kihívásai
Sok akadályok merülnek, hogy online kiskereskedőktől arcfelismerési használati eset az ilyen típusú végrehajtására tett kísérlet során. 

Először különböző méretű és alakú adatainak kell fogyasztanak több adatforrásokból, mind a helyszíni és a felhőben. Ezen adatok tartalmazzák a termék adatokat, a korábbi felhasználói viselkedési adatokat és a felhasználói adatok, a felhasználó megnyitja a online kereskedelmi webhelyen. 

Második, személyre szabott termék ajánlásokat kell ésszerűen és pontosan számított és előre jelezni. Online kiskereskedőktől termék, a márka és a viselkedést és a böngésző ügyféladatok kívül is kell adnia a múltbeli vásárlások számításba a megállapíthatja, hogy a felhasználó a legjobb termék javaslatok a felhasználók visszajelzéseit. 

A javaslatok harmadik, azonnal termék, a felhasználó egy zökkenőmentes keresse meg és a felhasználói élmény beszerzési, és a legutóbbi és a vonatkozó ajánlásokat kell lennie. 

Végül illetve a kiskereskedelemben általános felfelé-értékesít nyomon követése révén a megközelítést hatékonyságát mérésére és kereszt-értékesít kattintson-átalakítás értékesítési sikeres, és módosítsa az újabb ajánlások kell.

## <a name="solution-overview"></a>Megoldás áttekintése
A példa használati eset orvosolhatók és Azure Data Factory és az egyéb Cortana Intelligence Komponensszolgáltatások, beleértve a valódi Azure-felhasználók által megvalósított [HDInsight](https://azure.microsoft.com/services/hdinsight/) és [Power BI](https://powerbi.microsoft.com/).

Az online közvetítő használja, mint az adatok tárolási lehetőségek a munkafolyamaton keresztül egy Azure Blob-tároló, egy a helyszíni SQL server, Azure SQL Database és a relációs adatközpont.  A blob a tárolóban ügyféladatok, a felhasználói viselkedési adatokat és a termék adatok. A termék adatok termék márkáját információkat tartalmaz, és a termék a helyszínen tárolt SQL data warehouse a katalógusban. 

Az adatok kombinált és táplált képes biztosítani a személyre szabott meg ajánlásainkat ügyfél érdeklődési és a műveletek, amíg a felhasználó megnyitja a webhelyen a katalógusban szereplő termékek egy termék javaslat rendszerbe. Az ügyfelek azt is láthatja, termékek, a termék azok nézi kapcsolódó bármely egy felhasználó nem kapcsolódó általános webhely használati minták alapján.

![nagybetűk diagramok](./media/data-factory-product-reco-usecase/diagram-1.png)

A nyers webes naplófájlok gigabájt jönnek létre naponta az online közvetítő webhelyről félig strukturált fájlként. A nyers webes naplófájlokat és az ügyfél és a termék katalógusadatokat van keresztül a szervezetbe rendszeresen globálisan telepített adatátvitelt jelölik a Data Factory használatával szolgáltatásként Azure Blob-tárolóba. A nyers naplófájlokat a nap (által hónap és év) használata particionálja a blob Storage tárolóban, a hosszú távú tároláshoz.  [Az Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) particionálása a nyers naplófájlok helyét a blob-tárolóban, és a Hive és a Pig-parancsfájlok segítségével léptékű feldolgozott naplók feldolgozásához használatos. A particionált webnaplók adatok majd a bontsa ki a szükséges bemeneti adatok a machine learning-javaslat rendszer hoz létre a személyre szabott termék javaslatok feldolgozása.

A javaslat használt gépi tanulási ebben a példában a rendszer egy nyílt forráskódú gépi, az ajánlás platform tanulási [Apache Mahout](http://mahout.apache.org/).  Bármely [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) egyéni modell alkalmazhatja a forgatókönyvet.  A Mahout modell előrejelzése a hasonló elemek összesített használati minták alapján webhelyen között, és az egyes felhasználó alapján személyre szabott ajánlások létrehozására szolgál.

Végezetül az eredménykészlet személyre szabott termék ajánlások átkerül egy relációs adatpiacot felhasználásra a közvetítő webhely által.  Az eredménykészlet sikerült is közvetlenül a blob Storage tárolóban egy másik alkalmazás érhető el, vagy más fogyasztók és a használati esetek további tárolók át.

## <a name="benefits"></a>Előnyök
A termék javaslat stratégia optimalizálása, és azt az üzleti céljaihoz igazítása, a megoldás teljesül, az online közvetítő termékkihelyezési és célok marketing. Emellett voltak azok és kezelje a termék javaslat munkafolyamat egy hatékony, megbízható és költséghatékony módon. A módszer egyszerűen, hogy a modell frissítése és értékesítési kattintson-átalakítás sikeres intézkedések alapján hatékonyságának finomhangolásához. Azure Data Factory használatával voltak azok időigényes és költséges manuális felhő erőforrás-kezelés abandon és áthelyezése az igény szerinti felhő erőforrás-kezelés. Ezért ezek sikerült megoldás üzembe helyezése, időt és pénzt, és az idő. Leszármaztatás adatnézetek és működési szolgáltatásának állapota inaktívvá vált könnyen megjeleníthetővé és intuitív adat-előállító monitorozásának és kezelésének Azure-portálról elérhető felhasználói felületi elhárítása. A megoldás most ütemezett és felügyelt, hogy befejeződött az adatok megbízhatóan előállított és kézbesíteni a felhasználók számára, és adatokat és feldolgozási függőségek automatikusan kezeli emberi beavatkozás nélkül.

A személyre szabott bevásárlási élményt biztosít, a létrehozott verseny, kommunikáció zajlik az ügyfél online közvetítő észlelnek, és ezért az értékesítési és a teljes ügyfelek elégedettségének növelése.

