---
title: Dátum/idő elérésiút-minták az Azure Stream Analytics (előzetes verzió) kimeneti blob
description: Ez a cikk ismerteti az egyéni dátum és idő elérési útja minták funkció az Azure Stream Analytics-feladatok a blob storage-kimenet.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: ba386539c3f3c6740b843575bbccd4b028b8a5a7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090785"
---
# <a name="custom-datetime-path-patterns-for-azure-stream-analytics-blob-storage-output-preview"></a>Egyéni dátum/idő elérésiút-minták az Azure Stream Analytics a blob storage-kimenet (előzetes verzió)

Az Azure Stream Analytics támogatja a blob storage kimeneti fájlelérési útja egyéni dátum és idő formátumú specifikátory. Egyéni dátum/idő elérésiút-minták lehetővé teszik adjon meg egy kimeneti formátum, amely igazodik a Hive-adatfolyam-egyezmények jogosultságot ad az Azure Stream Analytics képes adatokat küldeni az Azure HDInsight és az Azure Databricks a feldolgozás aktiválásához. Egyéni dátum/idő elérésiút-minták egyszerűen valósíthatók meg használatával a `datetime` kulcsszó kimenet, a formátummegadó együtt a blob elérési út előtagja mezőjében. Például: `{datetime:yyyy}`.

Használja ezt a hivatkozást [az Azure Portal](https://portal.azure.com/?Microsoft_Azure_StreamAnalytics_bloboutputcustomdatetimeformats=true) válthat a szolgáltatás azt a jelzőt, amely lehetővé teszi az egyéni dátum/idő elérésiút-minták blob storage-kimenet előzetes verziójára. Ez a funkció a fő portálon hamarosan engedélyezve lesz.

## <a name="supported-tokens"></a>A következő tokenek támogatottak

A következő formátumban specifikátor jogkivonatok használhatók önállóan vagy együtt egyéni DateTime formátumokból eléréséhez:

|Specifikátor formátu.   |Leírás   |Eredmények példa időben 2018-01-02T10:06:08|
|----------|-----------|------------|
|{datetime:yyyy}|Az év négyjegyű számként.|2018.|
|{datetime:MM}|01-től 12 hónap|01|
|{datetime:M}|Hónap 1 és 12|1|
|{datetime:dd}|01-31 nap|02|
|{datetime:d}|Naponta 1 és 12|2|
|{datetime:HH}|A 24 órás formátumban, a 00 és 23 óra|10|
|{datetime:mm}|A 00 perc – 24|06|
|{datetime:m}|0 perc – 24|6|
|{datetime:ss}|A 00 60 másodperc|08|

Ha nem szeretne egyéni dátum és idő minták használata, adja hozzá a {date}, illetve {time} token beépített dátum és idő formátumú legördülő lista létrehozása elérési út előtagja.

![Stream Analytics régi DateTime formátumokból](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

## <a name="extensibility-and-restrictions"></a>Bővítési és korlátozások

Tetszőleges számú jogkivonatokkal `{datetime:<specifier>}`, az az elérési út mintája van lehetősége, amíg el nem éri a útvonalának előtagja karakterszámot. Formátum specifikátory belül túl már szerepel a dátum és idő legördülő menük által kombinációit, amelyeket egyetlen token nelze kombinovat. 

Az elérési út partíciójának `logs/MM/dd`:

|Érvénytelen kifejezés   |Érvénytelen kifejezés   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

Használhatja a azonos formátummegadó többször is feldolgozza a útvonalának előtagja. A jogkivonat minden egyes alkalommal meg kell ismételni.

## <a name="hive-streaming-conventions"></a>Hive-Streamelési konvenciók

Egyéni elérési út minták a blob storage-ba is használható a Hive-Streamelési egyezmény, amely mappák címkézte meg kell vár `column=` a mappa neve.

Például: `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

Egyéni kimeneti kiküszöböli módosítása a táblák és partíciók manuálisan hozzá port adatok között az Azure Stream Analytics és a Hive archiválás vesződségei nélkül. Ehelyett sok mappákat is hozzáadhatók használatával automatikusan:

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Példa

Hozzon létre egy storage-fiókot, egy erőforráscsoportot, egy Stream Analytics-feladat és egy bemeneti forrás a következők szerint a [Azure Stream Analytics az Azure Portal](stream-analytics-quick-create-portal.md) a rövid útmutató. Az is elérhető, a rövid útmutatóban használt azonos mintaadatok használatával [GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json).

Hozzon létre egy blob kimeneti fogadó a következő beállításokkal:

![Stream Analytics blob kimeneti fogadó létrehozása](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

A teljes elérési út mintája a következőképpen történik:


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


A feladat indításakor a mappastruktúra az elérésiút-minta alapján jön létre a blob-tárolóban. A napi szintű részletesen elemezheti.

![Stream Analytics blob kimeneti az egyéni elérési út mintája](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>További lépések

* [Kimenő adatait az Azure Stream Analytics ismertetése](stream-analytics-define-outputs.md)
