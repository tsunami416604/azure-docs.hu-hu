---
title: Az Azure Stream Analytics előzetes verziójú funkciók
description: Ez a cikk, amely jelenleg előzetes verzióban az Azure Stream Analytics szolgáltatásokat sorolja fel.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/10/2019
ms.openlocfilehash: 70cc6210a0c047867980de67ac24701df29ce8bf
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55485177"
---
# <a name="azure-stream-analytics-preview-features"></a>Az Azure Stream Analytics előzetes verziójú funkciók

Ez a cikk összefoglalja a jelenleg az Azure Stream Analytics előzetes verzióban elérhető összes funkciót. Előzetes verziójú funkciók használata az éles környezetben nem ajánlott.

## <a name="public-previews"></a>Nyilvános előzetes verziók

A következő funkciók vannak a nyilvános előzetes verzióban érhető el. Kihasználhatja ezeket a szolgáltatásokat még ma, de ne használja éles környezetben.

### <a name="integration-with-azure-machine-learning"></a>Az Azure Machine Learning-integráció

Stream Analytics-feladatok a Machine Learning (gépi tanulás) functions skálázhatja. Többet szeretne megtudni, hogyan használhatja a Stream Analytics-feladat ML-függvényeket, a Microsoft [méretezése az Azure Machine Learning-függvényekkel a Stream Analytics-feladat](stream-analytics-scale-with-machine-learning-functions.md). Tekintse meg a való életből vett forgatókönyv [hangulatelemzés végrehajtása az Azure Stream Analytics és Azure Machine Learning segítségével](stream-analytics-machine-learning-integration-tutorial.md).

### <a name="blob-output-partitioning-by-custom-time"></a>BLOB kimeneti egyéni időpontig particionálása

Az Azure Stream Analytics küldhetnek kimenetet a Blob storage egyéni idő attribútumok alapján. További információért látogasson el [egyéni dátum/idő elérésiút-minták az Azure Stream Analytics a blob storage-kimenet](stream-analytics-custom-path-patterns-blob-storage-output.md).

### <a name="javascript-user-defined-aggregate"></a>A JavaScript, felhasználó által meghatározott összesítés

Az Azure Stream Analytics támogatja a felhasználó által definiált összesítések (UDA), javascriptben írt, amelyek lehetővé teszik az állapotalapú összetett üzleti logikát. Ismerje meg, hogyan használhatja az Uda a [Azure Stream Analytics JavaScript felhasználó által definiált összesítések](stream-analytics-javascript-user-defined-aggregates.md) dokumentációját. 

### <a name="live-data-testing-in-visual-studio"></a>Élő adatok tesztelése a Visual Studióban

Az Azure Stream Analytics Visual Studio-eszközök javíthatja a helyi tesztelési funkciója, amely lehetővé teszi, hogy tesztelje, az élő esemény Streamek felhőbeli forrásokból, például az Event Hub vagy az IoT hub-lekérdezéseket. Ismerje meg, hogyan [tesztelése helyben az Azure Stream Analytics tools for Visual Studio használatával élő adatok](stream-analytics-live-data-local-testing.md).

### <a name="net-user-defined-functions-on-iot-edge"></a>.NET felhasználó által definiált függvények az IoT Edge-ben

.NET standard felhasználó által definiált függvények .NET Standard kódot futtathat a streamelési folyamat részeként. Egyszerű C# osztály létrehozása, vagy a teljes projekt és a szalagtárak importálása. Teljes szerzői műveletek, és felület hibakeresése a Visual Studio használata támogatott. További információért látogasson el [Azure Stream Analytics Edge-feladatok fejlesztése .NET Standard felhasználó által definiált függvények](stream-analytics-edge-csharp-udf-methods.md).

## <a name="private-previews"></a>Privát előzetes verziók

A következő funkciók private preview verzióban van.

### <a name="anomaly-detection"></a>Anomáliadetektálás

Az Azure Stream Analytics bevezeti az új machine learning-modellek támogatása *kiugrás* és *süllyedések* észlelési mellett kétirányú, lassú pozitív, és a lassú negatív trendek észlelését.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge"></a>C# az Azure Stream Analytics az IoT Edge-ben egyéni deszerializáló

A fejlesztők mostantól implementálhatók az egyéni deserializers deszerializálni az Azure Stream Analytics a fogadott események C#-ban. Amely képes lehet deszerializálni formátumok közé Parquet, Protopuf, XML vagy bármilyen bináris formátumot.

### <a name="blob-output-partitioning-by-custom-attribute"></a>Egyéni attribútum által particionálási kimeneti BLOB

Már lehetséges az Azure Stream Analytics kimeneti Blob Storage bármely másik oszlopot a lekérdezés alapján particionálásához.

### <a name="managed-identities-for-azure-resource-authentication-to-azure-data-lake-storage"></a>Felügyelt identitások Azure-erőforrás felé történő hitelesítéshez az Azure Data Lake Storage

Mostantól üzembe helyezheti a valós idejű folyamatok, az Azure-alapú erőforrások hitelesítéshez az Azure Data Lake Storage Gen1 írása közben a felügyelt identitásokból lehetővé teszi, hogy a feladatok programozott módon hozhatók létre. További információért látogasson el [identitások felügyelt használata az Azure-erőforrásokhoz való hitelesítéséhez az Azure Stream Analytics-feladatok az Azure Data Lake Storage Gen1 kimeneti](stream-analytics-managed-identities-adls.md).

## <a name="next-steps"></a>További lépések

* [Nyolc új funkciók az Azure Stream Analytics szolgáltatásban](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/)

* [Négy új funkciók már elérhető az Azure Stream Analytics szolgáltatásban](https://azure.microsoft.com/blog/4-new-features-now-available-in-azure-stream-analytics/)
