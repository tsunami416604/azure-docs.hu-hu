---
title: Data Factory használatieset - termékekre vonatkozó javaslatok
description: Ismerje meg egy Azure Data Factory használatával és egyéb szolgáltatások használati eset.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 6f1523c7-46c3-4b8d-9ed6-b847ae5ec4ae
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 4a3d1c513bcfb6449ca73d873c0dd9831c6fe01d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57540130"
---
# <a name="use-case---product-recommendations"></a>Használati eset – Termékajánlások
Az Azure Data Factory a Cortana Intelligence Suite-megoldásgyorsítók megvalósításához használt számos szolgáltatás egyike.  Lásd: [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics) oldalán Ez az alkalmazáscsomag adatait. Ez a dokumentum egy gyakori alkalmazási helyzet, hogy az Azure-felhasználók már megoldott és az Azure Data Factory és az egyéb Cortana Intelligence Komponensszolgáltatások használatával implementált ismertetünk.

## <a name="scenario"></a>Forgatókönyv
Online kiskereskedők gyakran szeretne rávenni a termékek megvásárlására a legnagyobb valószínűséggel hasznos lehet, és ezért nagy valószínűséggel vásárlása termékekkel szabályzatkérelem ügyfeleiknek. Ennek érdekében a online kiskereskedők kell, hogy adott felhasználó számára személyre szabott Termékajánlatok használatával a felhasználó online élmény testreszabása. Ezek személyre szabott ajánlások a jelenlegi és korábbi vásárlási viselkedési adatokat, termékinformációkat, újonnan bevezetett márkái és termék- és Szegmentálás adatok alapján történik.  Emellett azok megadhatják a felhasználó termékekre vonatkozó javaslatok teljes viselkedésébe, az összes felhasználójukat kombinált elemzése alapján.

Az alábbi kiskereskedők célja, hogy magasabb értékesítési bevételről kedvezményekhez és optimalizálhatja a felhasználói kattintson értékesítés átalakításhoz.  Azok az átalakításhoz azáltal, hogy az ügyfelek érdeklődését és műveletek alapján környezetfüggő, viselkedés-alapú termékekre vonatkozó javaslatok érhet el. Ezt a használati esetet online kiskereskedők használjuk példaként szolgál a vállalatok, amelyek az ügyfelek számára, akik optimalizálását. Ezeket az alapelveket azonban minden olyan cég, amely körül a termékek és szolgáltatások az ügyfelek megszólítása és ügyfelek vásárlási élmény a személyre szabott Termékajánlatok szeretne alkalmazni.

## <a name="challenges"></a>Problémák
Nincsenek számos kihívást, hogy online kiskereskedők face használati eset az ilyen típusú végrehajtására tett kísérlet során. 

Először kell-e a különböző méretekre és az adatok betöltött több adatforrásból, mind a helyszíni és a felhőben. Ezeket az adatokat a termék adatait, a korábbi felhasználói viselkedési adatokat és a felhasználói adatok, a felhasználó megnyitja az online kereskedelmi webhely tartalmaz. 

Második, személyre szabott termékajánlatokat lehet kapcsán felmerült költségeiért és nagy pontossággal számítja ki, és előre jelzett. Termék márkáját, és viselkedését, és a böngésző ügyféladatok, mellett online kiskereskedők kell is ügyfélvisszajelzések elmúlt beszerzésekre ajánlott a termékekre vonatkozó javaslatok, a felhasználó meghatározásakor figyelembe vennie. 

A javaslatok harmadik, azonnal a terméket, a felhasználó számára adjon meg egy zökkenőmentes tallózása és vásárlási élményt, és adja meg a legutóbbi és a vonatkozó javaslatok kell lennie. 

Végül a kiskereskedők kell mérni saját megközelítés hatékonyságát átfogó értékesítési nyomon követése révén keresztértékesítési kattintson-átalakítás értékesítési sikeres, és módosítsa a jövőbeni ajánlások.

## <a name="solution-overview"></a>Megoldás áttekintése
Ez a példa használati esetekhez megoldott és az Azure Data Factory és az egyéb Cortana Intelligence Komponensszolgáltatások, beleértve a valós Azure-felhasználók által megvalósított [HDInsight](https://azure.microsoft.com/services/hdinsight/) és [Power BI](https://powerbi.microsoft.com/).

Az online kereskedő az adatok tárolási lehetőségek a munkafolyamaton keresztül egy Azure Blob-tároló, egy helyszíni SQL server, Azure SQL Database és a egy relációs adatpiacot használja.  A blob-tároló ügyfél-információkat, a felhasználói viselkedési adatokat, és a termék információk adatokat tartalmazza. A termék információk adatok tartalmazzák a termék márkáját adatokat, és a katalógus a helyszínen tárolt az SQL data warehouse termék. 

Összes adat kombinált, és a egy termék javaslat rendszerbe, hogy személyre szabott javaslatok alapján ügyfelek érdeklődését és műveleteket, amíg a felhasználó a webhelyen a katalógusban szereplő termékek mappanevekkel adatkéréseket. Az ügyfelek is megtekintheti a termék megnézzük, kapcsolódó termékek bármely egy felhasználó nem kapcsolódó általános webhely használati minták alapján.

![használatieset](./media/data-factory-product-reco-usecase/diagram-1.png)

A nyers webes naplófájlok gigabájt jönnek létre naponta az online kereskedő webhelyről félig strukturált fájlok formájában. A nyers webes naplófájlokat és az ügyfél és a termékkatalógus információit be lett töltve rendszeresen egy Azure Blob storage használatával a Data Factory globálisan üzemelő adatáthelyezési szolgáltatás. A nyers naplófájlokat a nap (év és hónap) szerint vannak particionálva, blob Storage-hosszú távú tárolás céljából.  [Az Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) particionálása a nyers naplófájlokat a blob-tárolóban és a Hive és a Pig-parancsfájlok használatával nagy mennyiségű betöltött naplók feldolgozása. A particionált webnaplók adatok kinyerése a szükséges bemenetek a machine learning-javaslat rendszer hoz létre a személyre szabott Termékajánlatok majd dolgozzák fel.

A javaslat a machine learning-ebben a példában használt a rendszer egy nyílt forráskódú gépi tanulási javaslat-platformja [Apache Mahout](https://mahout.apache.org/).  Bármely [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) vagy egyéni modell forgatókönyvet is alkalmazható.  Előre jelezni az összesített használati mintái alapján a webhelyen található elemek koszinuszhasonlóságát, valamint az egyes felhasználó alapján a személyre szabott javaslatok létrehozása a Mahout modellt használja.

Végül az eredményhalmaz, személyre szabott Termékajánlatok került egy relációs adatpiacot felhasználásra a kereskedő webhely által.  Az eredményhalmaz sikerült is közvetlenül a blob storage-ból egy másik alkalmazás érhető el, vagy más fogyasztók és a használati esetek további áruházak áthelyezni.

## <a name="benefits"></a>Előnyök
Termék javaslat stratégiájuk optimalizálása és üzleti céljaihoz igazodó azt, a megoldás teljesül, az online kereskedő termékkihelyezési és marketing-célkitűzéseket. Emellett a felhasználók sikeresen üzembe helyezése és kezelése a termék javaslat munkafolyamat egy hatékony, megbízható és költséghatékony módon. A módszer egyszerűen a modell frissítése és finomhangolása annak alapján értékesítési kattintson csomagkonverzió létrehozások sikeres intézkedések hatékonyságát. Azure Data Factory segítségével a felhasználók sikeresen abandon az időigényes és drága feladat manuális felhőerőforrás-kezelés és igény szerinti felhőerőforrás-kezelés. Ezért ezek sikerült időt, költséget takaríthat meg, valamint csökkentheti a megoldás üzembe helyezése. Leszármaztatás adatnézetek és üzemeltetési szolgáltatások állapotának vált könnyen megjeleníthetővé és intuitív adat-előállító figyelése és kezelése az Azure Portalon elérhető felhasználói felületi hibaelhárítása. Megoldás most ütemezett és kezelt, hogy befejeződött az adatok megbízhatóan előállított és a felhasználók számára elérhető, és adatokat és a feldolgozási függőségek automatikusan kezeli az emberi beavatkozás nélkül.

Azáltal, hogy a személyre szabott élménnyel, a létrehozott agilisak és vonzó ügyfél online kereskedő élményt, és így növelheti az értékesítési és a teljes ügyfél-elégedettséget.

