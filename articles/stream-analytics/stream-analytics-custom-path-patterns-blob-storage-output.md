---
title: Az Azure Stream Analytics egyéni blob kimeneti particionálása
description: Ez a cikk ismerteti az egyéni dátum/idő elérésiút-minták és a blob storage-kimenet az Azure Stream Analytics-feladatok egyéni mező vagy attribútumok szolgáltatásokról.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/07/2019
ms.custom: seodec18
ms.openlocfilehash: fc28ddd006e8a117dddd67a6d6668b9639dddec5
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55765195"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Az Azure Stream Analytics egyéni blob kimeneti particionálása

Az Azure Stream Analytics támogatja a particionálást a egyéni mezők és attribútumok és egyéni dátum/idő elérésiút-minták egyéni blob kimeneti. 

## <a name="custom-field-or-attributes"></a>Egyéni mező vagy attribútumok

Egyéni mező vagy a bemeneti attribútumok aktiválásához adatfeldolgozási és jelentéskészítés a munkafolyamatokat azáltal, hogy jobban szabályozhatja a kimeneti javítása.

### <a name="partition-key-options"></a>Partíciós kulcs beállítások

A partíciós kulcs, vagy a bemeneti adatok particionálásához használt oszlopnév alfanumerikus karaktereket és kötőjeleket, aláhúzásjeleket és szóközöket tartalmazhat. Nem alkalmas beágyazott mezői használja partíciókulcsként, kivéve, ha az aliasok együtt használható.

### <a name="example"></a>Példa

Tegyük fel, hogy a feladat bemeneti adatokat egy külső videojátékok szolgáltatáshoz való kapcsolódás, ahol a betöltött oszlopot tartalmaz élő felhasználói munkamenetekből alatt **client_id** a munkamenetek azonosításához. Az adatok particionálásához **client_id**, állítsa be a Blob elérési út mintája mezőre, hogy a partíció jogkivonat **{client_id}** blob kimeneti tulajdonságai egy feladat létrehozásakor. A különböző adatként **client_id** értékek áthaladhat a Stream Analytics-feladat, a kimeneti adatok mentése egy alapján különböző mappákba **client_id** érték / mappa.

![Ügyfél-azonosítót az elérésiút-minta](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

Hasonlóképpen ha a feladat bemeneti volt érzékelőkből érkező érzékelőadatokat, ahol minden érzékelő kellett egy **sensor_id**, lenne, az elérési út mintája **{sensor_id}** minden érzékelő adatokat különböző mappákba.  


Használja a REST API, a kimeneti szakaszban egy JSON-fájlt a kérelmében előfordulhat, hogy a következőhöz hasonló:  

![REST API-kimenet](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

Miután a feladat elindult, a *ügyfelek* tároló nézhet ki a következő:  

![Az ügyfelek tároló](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Minden mappa több blobok, ahol minden egyes blob egy vagy több rekordot tartalmaz is tartalmazhat. A fenti példában egy blobot egy mappájában nincs címkézve "06000000", a következő tartalommal:

![A BLOB tartalma](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Figyelje meg, hogy a blobban lévő minden egyes rekord tartalmazza-e egy **client_id** a mappa megfelelő oszlopnév az oszlop a kimenetét a kimeneti elérési út particionálásához használt óta **client_id**.

### <a name="limitations"></a>Korlátozások

1. Az elérésiút-minta kimeneti blobtulajdonság csak egy egyéni partíciókulccsal megengedett. A következő elérésiút-minták összes érvényes:

   * cluster1/{date}/{aFieldInMyData}  
   * cluster1/{time}/{aFieldInMyData}  
   * cluster1/{aFieldInMyData}  
   * cluster1/{date}/{time}/{aFieldInMyData}  

2. Partíciókulcsok olyan kis-és nagybetűket, így például a "János" és "János" partíciókulcsok egyenértékűek. Kifejezések is, mint a partíciókulcsok nem használható. Ha például **{columnA + columnB}** nem működik.  

3. Ha egy bemeneti stream áll rögzíti a partíciós kulcs számosságuk a 8000-es, a rekordok meglévő blobok lesz hozzáfűzve, és csak a szükség esetén új blobok létrehozása. Ha a számosság keresztül van 8000-es nem garantált, meglévő blobok lesz írva, és új blobok nem hozható létre tetszőleges számú rekord ugyanazzal a partíciókulccsal rendelkező.  

## <a name="custom-datetime-path-patterns"></a>Egyéni dátum/idő elérésiút-minták

Egyéni dátum/idő elérésiút-minták lehetővé teszik adjon meg egy kimeneti formátum, amely igazodik a Hive-adatfolyam-egyezmények jogosultságot ad az Azure Stream Analytics képes adatokat küldeni az Azure HDInsight és az Azure Databricks a feldolgozás aktiválásához. Egyéni dátum/idő elérésiút-minták egyszerűen valósíthatók meg használatával a `datetime` kulcsszó kimenet, a formátummegadó együtt a blob elérési út előtagja mezőjében. Például: `{datetime:yyyy}`.

Használja ezt a hivatkozást [az Azure Portal](https://portal.azure.com/?Microsoft_Azure_StreamAnalytics_bloboutputcustomdatetimeformats=true) válthat a szolgáltatás azt a jelzőt, amely lehetővé teszi az egyéni dátum/idő elérésiút-minták blob storage-kimenet előzetes verziójára. Ez a funkció a fő portálon hamarosan engedélyezve lesz.

### <a name="supported-tokens"></a>A következő tokenek támogatottak

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

### <a name="extensibility-and-restrictions"></a>Bővítési és korlátozások

Tetszőleges számú jogkivonatokkal `{datetime:<specifier>}`, az az elérési út mintája van lehetősége, amíg el nem éri a útvonalának előtagja karakterszámot. Formátum specifikátory belül túl már szerepel a dátum és idő legördülő menük által kombinációit, amelyeket egyetlen token nelze kombinovat. 

Az elérési út partíciójának `logs/MM/dd`:

|Érvénytelen kifejezés   |Érvénytelen kifejezés   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

Használhatja a azonos formátummegadó többször is feldolgozza a útvonalának előtagja. A jogkivonat minden egyes alkalommal meg kell ismételni.

### <a name="hive-streaming-conventions"></a>Hive-Streamelési konvenciók

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
