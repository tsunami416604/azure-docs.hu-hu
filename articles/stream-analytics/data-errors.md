---
title: Az Azure Stream Analytics diagnosztikai naplóadat-hibái
description: Ez a cikk ismerteti a különböző bemeneti és kimeneti adatok hibák, amelyek az Azure Stream Analytics használata során fordulhat elő.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 5457308d577b95201fa31bfad0a6634a7a79eda3
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398133"
---
# <a name="azure-stream-analytics-data-errors"></a>Az Azure Stream Analytics adathibái

Az adathibák az adatok feldolgozása során előforduló hibák.  Ezek a hibák leggyakrabban az adatdeszerizálás, a szerializálás és az írási műveletek során fordulnak elő.  Adathibák esetén a Stream Analytics részletes információkat és példaeseményeket ír a diagnosztikai naplókba.  Bizonyos esetekben ezen információk összegzése portálon keresztül is biztosított.

Ez a cikk ismerteti a bemeneti és kimeneti adatok hibáinak különböző hibatípusait, okait és diagnosztikai naplójának részleteit.

## <a name="diagnostic-log-schema"></a>Diagnosztikai napló sémája

Lásd: [Az Azure Stream Analytics hibaelhárítása diagnosztikai naplók használatával](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema) a diagnosztikai naplók sémájának megtekintéséhez. A következő JSON egy példa érték a diagográfiai napló **tulajdonságok** mezőjében egy adathiba.

```json
{
    "Source": "InputTelemetryData",
    "Type": "DataError",
    "DataErrorType": "InputDeserializerError.InvalidData",
    "BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "Message": "Input Message Id: https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt Error: Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "ExampleEvents": "[\"1,2\\\\u000d\\\\u000a3,4\\\\u000d\\\\u000a5,6\"]",
    "FromTimestamp": "2019-03-22T22:34:18.5664937Z",
    "ToTimestamp": "2019-03-22T22:34:18.5965248Z",
    "EventCount": 1
}
```

## <a name="input-data-errors"></a>Bemeneti adatokhibái

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* Ok: A kiválasztott bemeneti tömörítési típus nem egyezik meg az adatokkal.
* A portál értesítése: Igen
* Diagnosztikai napló szintje: Figyelmeztetés
* Érintett terület: Az érvénytelen tömörítési típust is magában foglaló deszerializálási hibákat jelző üzenetek kikerülnek a bemenetből.
* Napló részletei
   * Bemeneti üzenet azonosítója. Az Event Hub esetében az azonosító a PartitionId, Offset és Sequence Number.

**Hibaüzenet**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* Ok: A bemeneti adatok fejléce érvénytelen. A CSV például ismétlődő nevű oszlopokkal rendelkezik.
* A portál értesítése: Igen
* Diagnosztikai napló szintje: Figyelmeztetés
* Érintett terület: Az érvénytelen fejlécet is magában foglaló deszerializálási hibákat jelző üzenetek elkelnek a bemenetről.
* Napló részletei
   * Bemeneti üzenet azonosítója. 
   * Tényleges hasznos teher néhány kilobájtig.

**Hibaüzenet**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* Ok: A CREATE TABLE vagy a TIMESTAMP BY segítségével definiált bemeneti oszlopok nem léteznek.
* A portál értesítése: Igen
* Diagnosztikai napló szintje: Figyelmeztetés
* Érintett terület: A hiányzó oszlopokkal rendelkező események kikerülnek a bemenetből.
* Napló részletei
   * Bemeneti üzenet azonosítója. 
   * A hiányzó oszlopok nevei. 
   * Tényleges hasznos teher néhány kilobájtig.

**Hibaüzenetek**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* Ok: Nem lehet a bemenetet a CREATE TABLE utasításban megadott típusra konvertálni.
* A portál értesítése: Igen
* Diagnosztikai napló szintje: Figyelmeztetés
* Érintett terület: A típuskonverziós hibával rendelkező események kikerülnek a bemenetből.
* Napló részletei
   * Bemeneti üzenet azonosítója. 
   * Az oszlop neve és a várt típus.

**Hibaüzenetek**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* Ok: A bemeneti adatok formátuma nem a megfelelő formátumú. Például a bemenet nem érvényes JSON.
* A portál értesítése: Igen
* Diagnosztikai napló szintje: Figyelmeztetés
* Érintett terület: Az üzenetben lévő összes esemény érvénytelen adathiba esetén kiesik a bemenetből.
* Napló részletei
   * Bemeneti üzenet azonosítója. 
   * Tényleges hasznos teher néhány kilobájtig.

**Hibaüzenetek**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* Ok: A TIMESTAMP BY kifejezés értéke nem konvertálható datetime-ra.
* A portál értesítése: Igen
* Diagnosztikai napló szintje: Figyelmeztetés
* Érintett terület: Az érvénytelen bemeneti időbélyeggel rendelkező események eldobásra kerülnek a bemenetről.
* Napló részletei
   * Bemeneti üzenet azonosítója. 
   * Hibaüzenet. 
   * Tényleges hasznos teher néhány kilobájtig.

**Hibaüzenet**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* Ok: A TIMESTAMP BY TIMESTAMPColumn értéke NULL.
* A portál értesítése: Igen
* Diagnosztikai napló szintje: Figyelmeztetés
* Érintett terület: Az érvénytelen bemeneti időbélyeg-kulccsal rendelkező események eldobásra kerülnek a bemeneti adatokból.
* Napló részletei
   * A tényleges hasznos teher akár néhány kilobájt.

**Hibaüzenet**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* Ok: A kiegyenlítési idő és az érkezési idő közötti különbség nagyobb, mint a késői érkezési tolerancia ablak.
* A portál értesítése megadva: Nem
* Diagnosztikai napló szintje: Információ
* Érintett terület: A késői bemeneti események kezelése a feladatkonfiguráció Eseményrendezés szakaszában található "Egyéb események kezelése" beállításnak megfelelően kerül meghatározásra. További információt az [Időkezelési házirendek című témakörben talál.](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)
* Napló részletei
   * Alkalmazás idoés érkezési ideje. 
   * Tényleges hasznos teher néhány kilobájtig.

**Hibaüzenet**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent esemény

* Ok: Az alkalmazás ideje és az érkezési idő közötti különbség 5 percnél hosszabb.
* A portál értesítése megadva: Nem
* Diagnosztikai napló szintje: Információ
* Érintett terület: A korai bemeneti események kezelése a feladatkonfiguráció Eseményrendezés szakaszában található "Egyéb események kezelése" beállításnak megfelelően kerül meghatározásra. További információt az [Időkezelési házirendek című témakörben talál.](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)
* Napló részletei
   * Alkalmazás idoés érkezési ideje. 
   * Tényleges hasznos teher néhány kilobájtig.

**Hibaüzenet**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutofOrderEvent

* Ok: Az esemény nem megfelelőnek számít a megadott sorrenden kívüli tűréshatár szerint.
* A portál értesítése megadva: Nem
* Diagnosztikai napló szintje: Információ
* Érintett terület: A nem sorrendben lebonyolított események kezelése a feladatkonfiguráció Eseményrendezés szakaszában található "Egyéb események kezelése" beállításnak megfelelően kerül rendezésre. További információt az [Időkezelési házirendek című témakörben talál.](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)
* Napló részletei
   * Tényleges hasznos teher néhány kilobájtig.

**Hibaüzenet**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Kimeneti adathibák

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* Ok: A kimenethez szükséges oszlop nem létezik. Például egy oszlop azure table partitionkey néven definiált nem létezik.
* A portál értesítése: Igen
* Diagnosztikai napló szintje: Figyelmeztetés
* Érintett terület: A kimeneti adatok konvertálási hibáit, beleértve a hiányzó kötelező oszlopot is, a [kimeneti adatok házirendje](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) beállítás nak megfelelően kezeli a rendszer.
* Napló részletei
   * Az oszlop neve és a rekordazonosító vagy a rekord egy része.

**Hibaüzenet**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* Ok: Az oszlop értéke nem felel meg a kimenetnek. Például az oszlop neve nem érvényes Azure-tábla oszlop.
* A portál értesítése: Igen
* Diagnosztikai napló szintje: Figyelmeztetés
* Érintett terület: Az összes kimeneti adatkonverziós hiba, beleértve az érvénytelen oszlopnevet is, a [Kimeneti adatok házirendje](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) beállításnak megfelelően kerül meghatározásra.
* Napló részletei
   * Az oszlop neve és a rekordazonosító vagy a rekord egy része.

**Hibaüzenet**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* Ok: Az oszlop nem konvertálható érvényes típussá a kimenetben. Az oszlop értéke például nem kompatibilis az SQL táblában definiált megkötésekkel vagy típusokkal.
* A portál értesítése: Igen
* Diagnosztikai napló szintje: Figyelmeztetés
* Érintett terület: Az összes kimeneti adatkonverziós hiba, beleértve a típuskonverziós hibát is, a [Kimeneti adatok házirendje](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) beállításnak megfelelően történik.
* Napló részletei
   * Az oszlop neve.
   * Rekordazonosítóvagy a rekord egy része.

**Hibaüzenet**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* Ok: Az üzenet értéke nagyobb, mint a támogatott kimeneti méret. Egy rekord például 1 MB-nál nagyobb egy Event Hub-kimenet esetén.
* A portál értesítése: Igen
* Diagnosztikai napló szintje: Figyelmeztetés
* Érintett terület: A kimeneti adatok konvertálási hibáit, beleértve a rekord túllépte a méretkorlátot, a [kimeneti adatok házirendje](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) beállítás nak megfelelően kezeli a rendszer.
* Napló részletei
   * Rekordazonosítóvagy a rekord egy része.

**Hibaüzenet**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* Ok: A rekord már tartalmaz egy olyan oszlopot, amelynek neve megegyezik a Rendszer oszlopéval. Például cosmosDB kimenet egy oszlop nevű azonosító, ha azonosító oszlop egy másik oszlopban.
* A portál értesítése: Igen
* Diagnosztikai napló szintje: Figyelmeztetés
* Érintett terület: Az összes kimeneti adatkonverziós hiba, beleértve az ismétlődő kulcsot is, a [Kimeneti adatok házirendje](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) beállításnak megfelelően kerül meghatározásra.
* Napló részletei
   * Az oszlop neve.
   * Rekordazonosítóvagy a rekord egy része.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics hibaelhárítása diagnosztikai naplók használatával](stream-analytics-job-diagnostic-logs.md)

* [A Stream Analytics-feladatfigyelés és a lekérdezések figyelésének ismertetése](stream-analytics-monitoring.md)
