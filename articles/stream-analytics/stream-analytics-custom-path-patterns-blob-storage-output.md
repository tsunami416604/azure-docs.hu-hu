---
title: Az Azure Stream Analytics egyéni blobkimeneti particionálása
description: Ez a cikk ismerteti az egyéni DateTime elérési út minták és az egyéni mező vagy attribútumok jellemzői blob storage kimenet az Azure Stream Analytics-feladatok.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/07/2019
ms.custom: seodec18
ms.openlocfilehash: e978771eaafafe4120f9eec802525c293fb9c7c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426388"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Az Azure Stream Analytics egyéni blobkimeneti particionálása

Az Azure Stream Analytics egyéni blobkimenet-particionálást támogat egyéni mezőkkel vagy attribútumokkal és egyéni DateTime elérési útmintákkal. 

## <a name="custom-field-or-attributes"></a>Egyéni mező vagy attribútumok

Az egyéni mező- vagy bemeneti attribútumok javítják a kisebb adatfeldolgozási és jelentéskészítési munkafolyamatokat azáltal, hogy nagyobb ellenőrzést tesznek lehetővé a kimenet felett.

### <a name="partition-key-options"></a>Partíciókulcs beállításai

A bemeneti adatok particionálásához használt partíciókulcs vagy oszlopnév kötőjellel, aláhúzásjellel és szóközzel rendelkező alfanumerikus karaktereket tartalmazhat. A beágyazott mezők csak aliasokkal együtt használhatók. A partíciókulcsnak NVARCHAR(MAX) lehet.

### <a name="example"></a>Példa

Tegyük fel, hogy egy feladat bemeneti adatokat vesz fel egy külső videojáték-szolgáltatáshoz kapcsolódó élő felhasználói munkamenetekből, ahol a bevitt adatok egy **oszlopot tartalmaznak, client_id** a munkamenetek azonosításához. Az adatok **client_id**particionálásához állítsa be a Blob Path Pattern mezőt úgy, hogy a feladat létrehozásakor a blob kimeneti tulajdonságai tartalmazzon **{client_id}** partíciós tokent. Mivel a különböző **client_id** értékeket tartalmazó adatok a Stream Analytics feladaton keresztül folynak, a kimeneti adatok at külön mappákba menti a rendszer mappaenként egyetlen **client_id** érték alapján.

![Elérési út mintája ügyfélazonosítóval](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

Hasonlóképpen, ha a feladat bemeneti érzékelő adatok több millió **érzékelő,** ahol minden érzékelő sensor_id, a Path Pattern lenne **{sensor_id}** particionálásminden érzékelő adatait különböző mappákba.  


A REST API használatával a kérelemhez használt JSON-fájl kimeneti szakasza a következőkre néz ki:  

![REST API kimenet](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

A feladat futásának megkezdése után az *ügyfelek* tárolója a következőkre néz ki:  

![Ügyfelek tárolója](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Minden mappa több blobot tartalmazhat, ahol minden blob egy vagy több rekordot tartalmaz. A fenti példában egyetlen blob található a "06000000" címkével ellátott mappában, amelynek tartalma a következő:

![Blob tartalma](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Figyelje meg, hogy a blob minden rekordjának van egy **client_id** oszlopa, amely megfelel a mappa nevének, mivel a kimenet kimenetének particionálására használt oszlop **client_id**volt.

### <a name="limitations"></a>Korlátozások

1. Csak egy egyéni partíciókulcs engedélyezett a Path Pattern blob kimeneti tulajdonságban. Az alábbi görbeminták mindegyike érvényes:

   * cluster1/{date}/{aFieldInMyData}  
   * cluster1/{time}/{aFieldInMyData}  
   * cluster1/{aFieldInMyData}  
   * cluster1/{date}/{time}/{aFieldInMyData} 
   
2. A partíciókulcsok nem különböznek a kis- és nagybetűktől, ezért a partíciós kulcsok, például a "János" és a "john" egyenértékűek. A kifejezések nem használhatók partíciókulcsként is. **A(z) {columnA + columnB}** például nem működik.  

3. Ha egy bemeneti adatfolyam 8000 alatti partíciókulcs-számosságú rekordokból áll, a rekordok hozzáfűzése a meglévő blobokhoz lesz hozzáfűzve, és csak szükség esetén hoz létre új blobokat. Ha a számosság több mint 8000 nincs garancia a meglévő blobok lesz írva, és az új blobok nem jön létre tetszőleges számú rekordok azonos partíciókat.

## <a name="custom-datetime-path-patterns"></a>Egyéni DateTime elérési út mintái

Az egyéni DateTime-útvonalminták lehetővé teszik, hogy olyan kimeneti formátumot adjon meg, amely megfelel a Hive streamelési konvencióknak, így az Azure Stream Analytics lehetővé teszi az adatok küldését az Azure HDInsight és az Azure Databricks számára későbbi feldolgozásra. Egyéni DateTime elérési út minták `datetime` könnyen megvalósítható a kulcsszó a blob kimenet útelőtag mezőjében, valamint a formátum megíró. Például: `{datetime:yyyy}`.

### <a name="supported-tokens"></a>Támogatott jogkivonatok

Az alábbi formátummegváltó tokenek használhatók egyedül vagy kombinálva az egyéni DateTime formátumok eléréséhez:

|Formátummegadható   |Leírás   |Eredmények a példa idő 2018-01-02T10:06:08|
|----------|-----------|------------|
|{datetime:yyyy}|Az év, mint egy négyjegyű szám|2018|
|{datetime:MM}|Hónap 01-től 12-ig|01|
|{datetime:M}|Hónap 1-től 12-ig|1|
|{datetime:dd}|Nap 01-től 31-ig|02|
|{datetime:d}|Nap 1-től 12-ig|2|
|{datetime:HH}|Óra 24 órás formátumban, 00 és 23 között|10|
|{datetime:mm}|Perc 00 és 24 között|06|
|{datetime:m}|Perc 0 és 24 között|6|
|{datetime:ss}|Másodperc 00-tól 60-ig|08|

Ha nem kíván egyéni DateTime mintákat használni, hozzáadhatja a {date} és/vagy {time} jogkivonatot az Elérési út előtaghoz, hogy beépített DateTime formátumokkal rendelkező legördülő menüt hozzon létre.

![Stream Analytics régi DateTime formátumok](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

### <a name="extensibility-and-restrictions"></a>Bővíthetőség és korlátozások

Annyi jogkivonatot használhat, `{datetime:<specifier>}`amennyit csak szeretne az elérési út mintájában, amíg el nem éri a Path-előtag karakterkorlátot. A formátummegíró konklúzókat nem lehet egyetlen jogkivonaton belül kombinálni a dátum- és időlegördülő listákon már felsorolt kombinációkon túl. 

A következő elérési `logs/MM/dd`út partíciója esetén:

|Érvényes kifejezés   |Érvénytelen kifejezés   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

Ugyanazt a formátummegadható t többször is használhatja az Elérési út előtagban. A jogkivonatot minden alkalommal meg kell ismételni.

### <a name="hive-streaming-conventions"></a>Hive-streamelési konvenciók

A blobstorage egyéni elérési útmintái a Hive-streamelési konvencióval `column=` használhatók, amely elvárja, hogy a mappák a mappanevében címkével legyenek ellátva.

Például: `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

Az egyéni kimenet kiküszöböli a táblák módosításával és a partíciók manuális hozzáadásával kapcsolatos bajt az Azure Stream Analytics és a Hive közötti portadatokhoz. Ehelyett sok mappa automatikusan hozzáadható a következő kontőket használva:

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Példa

Hozzon létre egy tárfiókot, egy erőforráscsoportot, egy Stream Analytics-feladatot és egy bemeneti forrást az [Azure Stream Analytics Azure Portal](stream-analytics-quick-create-portal.md) rövid útmutatójának megfelelően. Használja ugyanazokat a mintaadatokat, amelyeket a [GitHubon](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json)is elérhető rövid útmutatóban használnak.

Hozzon létre egy blob kimeneti fogadót a következő konfigurációval:

![A Stream Analytics blobkimeneti fogadót hoz létre](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

A teljes útvonal minta a következő:


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


A feladat indításakor egy mappastruktúra az elérési út minta alapján jön létre a blob tárolóban. Akkor lefúranak a nap szintjén.

![Stream Analytics blobkimenet egyéni elérési útmintával](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics kimeneteinek megismerése](stream-analytics-define-outputs.md)
