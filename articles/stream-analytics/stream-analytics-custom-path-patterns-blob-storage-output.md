---
title: Egyéni dátum/idő elérésiút-minták az Azure Stream Analytics a blob storage-kimenet (előzetes verzió)
description: ''
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 6945e31825af46628c0945cdf579382b20bdcd68
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47414014"
---
# <a name="custom-datetime-path-patterns-for-azure-stream-analytics-blob-storage-output-preview"></a>Egyéni dátum/idő elérésiút-minták az Azure Stream Analytics a blob storage-kimenet (előzetes verzió)

Az Azure Stream Analytics támogatja a blob storage kimeneti fájlelérési útja egyéni dátum és idő formátumú specifikátory. Egyéni dátum/idő elérésiút-minták lehetővé teszik adjon meg egy kimeneti formátum, amely igazodik a Hive-adatfolyam-egyezmények jogosultságot ad az Azure Stream Analytics képes adatokat küldeni az Azure HDInsight és az Azure Databricks a feldolgozás aktiválásához. Egyéni dátum/idő elérésiút-minták egyszerűen valósíthatók meg használatával a `datetime` kulcsszó kimenet, a formátummegadó együtt a blob elérési út előtagja mezőjében. Például: `{datetime:yyyy}`.

Használja ezt a hivatkozást [az Azure Portal](https://ms.portal.azure.com/?Microsoft_Azure_StreamAnalytics_bloboutputcustomdatetimeformats=true) válthat a szolgáltatás azt a jelzőt, amely lehetővé teszi az egyéni dátum/idő elérésiút-minták blob storage-kimenet előzetes verziójára. Ez a funkció a fő portálon hamarosan engedélyezve lesz.

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

```
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Példa

Hozzon létre egy storage-fiókot, egy erőforráscsoportot, egy Stream Analytics-feladat és egy bemeneti forrás a következők szerint a [Azure Stream Analytics az Azure Portal](stream-analytics-quick-create-portal.md) a rövid útmutató. Az is elérhető, a rövid útmutatóban használt azonos mintaadatok használatával [GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json).

Hozzon létre egy blob kimeneti fogadó a következő beállításokkal:

![Stream Analytics blob kimeneti fogadó létrehozása](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

A teljes elérési út mintája a következőképpen történik:

```
year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}
```

A feladat indításakor a mappastruktúra az elérésiút-minta alapján jön létre a blob-tárolóban. A napi szintű részletesen elemezheti.

![Stream Analytics blob kimeneti az egyéni elérési út mintája](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>További lépések

* [Kimenő adatait az Azure Stream Analytics ismertetése](stream-analytics-define-outputs.md)
