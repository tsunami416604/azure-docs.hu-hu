---
title: Data Factory használati eset - Termékajánlások
description: Ismerje meg az Azure Data Factory és más szolgáltatások használatával megvalósított használati esetet.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 6f1523c7-46c3-4b8d-9ed6-b847ae5ec4ae
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 49ad9be7c70602132436b14234f01a4086d8e1fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70139138"
---
# <a name="use-case---product-recommendations"></a>Használati eset - Termékjavaslatok
Az Azure Data Factory egyike a Cortana Intelligence Suite megoldásgyorsítók megvalósításához használt számos szolgáltatásnak.  A csomaggal kapcsolatos részletekért tekintse meg a [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics) oldalát. Ebben a dokumentumban egy olyan gyakori használati esetet ismertetünk, amelyet az Azure-felhasználók már megoldottak és megvalósítottak az Azure Data Factory és más Cortana Intelligence-összetevő-szolgáltatások használatával.

## <a name="scenario"></a>Forgatókönyv
Az online kiskereskedők általában arra akarják csábítani ügyfeleiket, hogy olyan termékeket vásároljanak, amelyek olyan termékeket mutatnak be, amelyek a legnagyobb valószínűséggel érdeklődnek irántuk, és ezért a legnagyobb valószínűséggel vásárolnak. Ehhez az online kiskereskedőknek testre kell szabniuk a felhasználói élményüket az adott felhasználónak szóló személyre szabott termékjavaslatok használatával. Ezeket a személyre szabott javaslatokat az aktuális és a korábbi vásárlási viselkedési adataik, a termékinformációk, az újonnan bevezetett márkák, valamint a termék- és ügyfélszegmentálási adatok alapján kell meghozni.  Emellett a felhasználói termék javaslatokat is biztosíthatnak az összes felhasználó általános használati viselkedésének elemzése alapján.

Ezeknek a kiskereskedőknek az a célja, hogy optimalizáljanak a felhasználók kattintásra történő konverzióira, és magasabb értékesítési bevételt szerezzenek.  Ezt az átalakítást az ügyfelek érdeklődési körén és tevékenységein alapuló környezetfüggő, viselkedésalapú termékajánlások biztosításával érik el. Ebben a használati esetben az online kiskereskedőket olyan vállalkozások példájaként használjuk, amelyek optimalizálni szeretnék ügyfeleik számára. Ezek az elvek azonban minden olyan vállalkozásra vonatkoznak, amely az ügyfeleit az áruk és szolgáltatások köré kívánja vonni, és személyre szabott termékajánlásokkal kívánja növelni ügyfelei vásárlási élményét.

## <a name="challenges"></a>Problémák
Sok kihívás, hogy az online kiskereskedők szembesülnek, amikor megpróbálja végrehajtani az ilyen típusú használati eset. 

Először a különböző méretű és formájú adatokat több adatforrásból kell bevenni, mind a helyszínen, mind a felhőben. Ezek az adatok közé tartoznak a termékadatok, az ügyfél viselkedési adatai és a felhasználói adatok, miközben a felhasználó az online kiskereskedelmi webhelyen böngészik. 

Másodszor, a személyre szabott termékajánlásokat ésszerűen és pontosan ki kell számítani és előre kell jelezni. A termék-, márka- és ügyfélviselkedési és -felhasználói adatok mellett az online kiskereskedőknek a korábbi vásárlásokra vonatkozó vásárlói visszajelzéseket is fel kell tüntetnie, hogy figyelembe vethessék a felhasználó számára legmegfelelőbb termékajánlások meghatározását. 

Harmadszor, az ajánlásokat azonnal kézbesíthetőnek kell lennie a felhasználónak, hogy zökkenőmentes böngészési és vásárlási élményt nyújtson, és a legfrissebb és releváns ajánlásokat nyújtsa. 

Végezetül, a kiskereskedőknek fel kell mérniük megközelítésük hatékonyságát azáltal, hogy nyomon követik a kattintások és a konverziók közötti általános értékesítési sikereket, és alkalmazkodnak jövőbeli ajánlásaikhoz.

## <a name="solution-overview"></a>Megoldási áttekintés
Ezt a példahasználati esetet valódi Azure-felhasználók oldották meg és valósították meg az Azure Data Factory és más Cortana Intelligence-összetevő-szolgáltatások, köztük a [HDInsight](https://azure.microsoft.com/services/hdinsight/) és a [Power BI](https://powerbi.microsoft.com/)használatával.

Az online kiskereskedő egy Azure Blob-áruházat, egy helyszíni SQL-kiszolgálót, az Azure SQL DB-t és egy relációs adatpiacot használ adattárolási lehetőségekként a munkafolyamat során.  A blob áruház tartalmazza az ügyfél adatait, az ügyfél viselkedési adatait és a termékadatokat. A termékinformációs adatok közé tartoznak a termékmárka-információk és az SQL-adattárházban a helyszínen tárolt termékkatalógus. 

Az összes adatot kombináljuk és betápláljuk egy termékajánlási rendszerbe, hogy személyre szabott ajánlásokat jelenítsünk meg az ügyfelek érdeklődési körei és lépései alapján, miközben a felhasználó a katalógusban lévő termékeket böngészi a webhelyen. Az ügyfelek olyan termékeket is látnak, amelyek kapcsolódnak ahhoz a termékhez, amelyet a webhely használati mintái alapján néznek, és amelyek nem kapcsolódnak egyetlen felhasználóhoz sem.

![kis- és nagybetűk diagramja](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabájt nyers webes naplófájlok jönnek létre naponta az online kiskereskedő honlapján, mint félig strukturált fájlokat. A nyers webnapló-fájlok, valamint az ügyfél- és termékkatalógus-információk rendszeresen bekerülnek egy Azure Blob-tárba a Data Factory globálisan üzembe helyezett adatmozgatatása szolgáltatásként használva. A napi nyers naplófájlok particionálva vannak (év és hónap szerint) a blob storage hosszú távú tároláshoz.  [Az Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) a blob tárolóban lévő nyers naplófájlok particionálására és a bevitt naplók feldolgozása a Hive és a Pig parancsfájlok használatával. A particionált webnaplók adatait ezután feldolgozzák a szükséges bemenetek kinyerése a gépi tanulási ajánlási rendszer a személyre szabott termék javaslatok létrehozásához.

Ebben a példában a gépi tanuláshoz használt ajánlási rendszer az Apache Mahout nyílt forráskódú gépi tanulási ajánlási [platformja.](https://mahout.apache.org/)  Bármely [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) vagy egyéni modell alkalmazható a forgatókönyvre.  A Mahout modell segítségével előre jelezni a hasonlóság az elemek a honlapon az általános használati minták alapján, és az egyes felhasználók alapján személyre szabott javaslatok létrehozásához.

Végül, az eredmény készlet személyre szabott termék ajánlások átkerül a relációs adatok mart fogyasztásra a kiskereskedő honlapján.  Az eredményhalmaz is közvetlenül érhető el blob storage egy másik alkalmazás, vagy áthelyezett további tárolók más fogyasztók és használati esetek.

## <a name="benefits"></a>Előnyök
A megoldás a termékajánlási stratégia optimalizálásával és az üzleti célokhoz való igazításával megfelelt az online kiskereskedő értékesítési és marketing célkitűzéseinek. Emellett hatékony, megbízható és költséghatékony módon tudták működőképessé tenni és kezelni a termékajánlási munkafolyamatot. A megközelítés megkönnyítette számukra, hogy frissítsék a modellt, és finomítsák annak hatékonyságát az értékesítési kattintás-átalakítás sikerei alapján. Az Azure Data Factory használatával felhagyhattak az időigényes és költséges manuális felhőalapú erőforrás-kezeléssel, és átálltak az igény szerinti felhőbeli erőforrás-kezelésre. Ezért időt, pénzt takaríthattak meg, és csökkenthetik a megoldás üzembe helyezésére töltött idejüket. Az azure portalon elérhető intuitív Data Factory-figyelési és -kezelési felhasználói felülettel az adatvonalnézetek és az operatív szolgáltatások állapota könnyen láthatóvá és elháríthatóvá vált. A megoldás most már ütemezhető és kezelhető, hogy a kész adatok megbízhatóan előállított és kézbesített a felhasználók számára, és az adatok és a feldolgozási függőségek automatikusan kezelése emberi beavatkozás nélkül.

Azáltal, hogy ezt a személyre szabott vásárlási élményt, az online kiskereskedő létrehozott egy versenyképesebb, vonzó ügyfélélményt, és ezáltal növeli az értékesítés és az általános ügyfél-elégedettség.

