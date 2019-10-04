---
title: Az Azure Stream Analytics diagnosztikai napló adathibák
description: Ez a cikk ismerteti a különböző bemeneti és kimeneti adathibák, amely akkor fordulhat elő, amikor az Azure Stream Analytics használatával.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: ecc7077bf208adf1ac89adcce2f2e480ce34888e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329598"
---
# <a name="azure-stream-analytics-data-errors"></a>Az Azure Stream Analytics-adatok hibák

Adatok olyan adatainak feldolgozása során előforduló hibákat tartalmaznak.  Ezek a hibák leggyakrabban során adatokat deszerializálás szerializálási, és írási műveleteket.  Adatok hibák esetén Stream Analytics ír, részletes információkat és események például a diagnosztikai naplók.  Bizonyos esetekben ezek az információk összegzését is portál értesítési keresztül biztosított.

Ez a cikk ismerteti a különböző alkalmazáshiba-típusok, okok és a diagnosztikai naplót a bemeneti és kimeneti adatok hibákat.

## <a name="diagnostic-log-schema"></a>Diagnosztikai napló séma

Lásd: [Azure Stream Analytics hibáinak elhárítása a diagnosztikai naplók](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema) a séma a diagnosztikai naplók számára. A következő JSON értéke egy példa a **tulajdonságok** Adathiba diagnosztikai naplót mezőjében.

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

## <a name="input-data-errors"></a>A bemeneti adatok hibák

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* OK: A kiválasztott bemeneti tömörítési típus nem felel meg az adatokat.
* A megadott Portal értesítése: Igen
* Diagnosztikai naplózási szint: Figyelmeztetés
* Hatás: Üzenetek az érvénytelen tömörítési típus többek között a deszerializálás hibákat a bemeneti eldobásakor.
* Napló részletei
   * A bemeneti üzenet azonosítója. Event Hub az azonosító, a PartitionId, Offset sorszáma.

**Hibaüzenet**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* OK: A bemeneti adatok fejléce érvénytelen. Ha például a CSV ismétlődő nevű oszlopot tartalmaz.
* A megadott Portal értesítése: Igen
* Diagnosztikai naplózási szint: Figyelmeztetés
* Hatás: Üzenetek az érvénytelen fejléc többek között a deszerializálás hibákat a bemeneti eldobásakor.
* Napló részletei
   * A bemeneti üzenet azonosítója. 
   * Akár néhány kilobájt tényleges adattartalma.

**Hibaüzenet**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* OK: A CREATE TABLE vagy a TIMESTAMP BY meghatározott bemeneti oszlopok nem létezik.
* A megadott Portal értesítése: Igen
* Diagnosztikai naplózási szint: Figyelmeztetés
* Hatás: Hiányzó oszlopokkal rendelkező eseményeket a rendszer elveti a bemeneti.
* Napló részletei
   * A bemeneti üzenet azonosítója. 
   * Hiányzó oszlopainak nevét. 
   * Akár néhány kilobájt tényleges adattartalma.

**Hibaüzenetek**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* OK: Nem sikerült a CREATE TABLE utasításban megadott típus a bemenet átalakítása.
* A megadott Portal értesítése: Igen
* Diagnosztikai naplózási szint: Figyelmeztetés
* Hatás: Események, a típuskonverziós hiba a bemeneti eldobásakor.
* Napló részletei
   * A bemeneti üzenet azonosítója. 
   * Az oszlop, és a várt típus neve.

**Hibaüzenetek**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* OK: A bemeneti adat nem a megfelelő formátumban van. Ha például a bemenet nem érvényes JSON.
* A megadott Portal értesítése: Igen
* Diagnosztikai naplózási szint: Figyelmeztetés
* Hatás: Összes esemény után egy érvénytelen adatokat hiba történt az üzenet a bemeneti eldobásakor.
* Napló részletei
   * A bemeneti üzenet azonosítója. 
   * Akár néhány kilobájt tényleges adattartalma.

**Hibaüzenetek**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* OK: A TIMESTAMP BY kifejezés értéke nem konvertálható DateTime.
* A megadott Portal értesítése: Igen
* Diagnosztikai naplózási szint: Figyelmeztetés
* Hatás: Érvénytelen bemeneti időbélyeggel eseményeket a rendszer elveti a bemeneti.
* Napló részletei
   * A bemeneti üzenet azonosítója. 
   * Hibaüzenet jelenik meg. 
   * Akár néhány kilobájt tényleges adattartalma.

**Hibaüzenet**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* OK: A TIMESTAMP BY OVER timestampColumn értéke NULL.
* A megadott Portal értesítése: Igen
* Diagnosztikai naplózási szint: Figyelmeztetés
* Hatás: Érvénytelen bemeneti időbélyeg kulccsal rendelkező események a bemeneti eldobásakor.
* Napló részletei
   * A tényleges tartalom akár néhány kilobájt.

**Hibaüzenet**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* OK: Az alkalmazás és érkezés időpontja közötti különbség nagyobb, mint érkezési tolerancia frissítéskibocsátási időszak.
* A megadott Portal értesítése: Nem
* Diagnosztikai naplózási szint: Információ
* Hatás:  Későn érkező bemeneti események kezeli megfelelően a "kezelni az eseményeket" beállítást az rendezése a feladat konfigurálása szakaszát. További információ: [idő-kezelési házirendek](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Napló részletei
   * Alkalmazás és érkezés időpontja. 
   * Akár néhány kilobájt tényleges adattartalma.

**Hibaüzenet**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* OK: Az alkalmazás és érkezés időpontja közötti különbség nagyobb, mint 5 perc.
* A megadott Portal értesítése: Nem
* Diagnosztikai naplózási szint: Információ
* Hatás:  Korai bemeneti események kezeli megfelelően a "kezelni az eseményeket" beállítást az rendezése a feladat konfigurálása szakaszát. További információ: [idő-kezelési házirendek](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Napló részletei
   * Alkalmazás és érkezés időpontja. 
   * Akár néhány kilobájt tényleges adattartalma.

**Hibaüzenet**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* OK: Esemény nem működik megfelelően a meghatározott sorrendben tolerancia ablakban tekinthető.
* A megadott Portal értesítése: Nem
* Diagnosztikai naplózási szint: Információ
* Hatás:  Események kezelése szerint a "kezelni az eseményeket" üzemen kívüli beállítása az rendezés szakaszában a feladat konfigurációját. További információ: [idő-kezelési házirendek](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Napló részletei
   * Akár néhány kilobájt tényleges adattartalma.

**Hibaüzenet**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Kimeneti adathibák

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* OK: A kimenet szükséges az oszlop nem létezik. Például az Azure-tábla PartitionKey does't meghatározva oszlop létezik.
* A megadott Portal értesítése: Igen
* Diagnosztikai naplózási szint: Figyelmeztetés
* Hatás:  Az összes kimeneti az Adatátalakítási hibák például az oszlop hiányzik a következők szerint kezeli a [kimeneti Adatszabályzat](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) beállítás.
* Napló részletei
   * Neve az oszlopban, és a rekord azonosítója vagy az a rekord része.

**Hibaüzenet**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* OK: Az oszlop értéke nem felelnek meg, és a kimenete. Ha például az oszlop neve nem egy érvényes Azure Táblázatoszlop.
* A megadott Portal értesítése: Igen
* Diagnosztikai naplózási szint: Figyelmeztetés
* Hatás:  Az összes kimeneti az Adatátalakítási hibák Érvénytelen oszlopnév többek között a következők szerint kezeli a [kimeneti Adatszabályzat](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) beállítás.
* Napló részletei
   * Neve az oszlopban, és rekord azonosítója vagy egy a rekord része.

**Hibaüzenet**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* OK: Egy oszlopot nem lehet konvertálni a kimenetben szereplő típus érvénytelen. Például a oszlop értéke nem kompatibilis a megkötések vagy SQL-tábla meghatározott típus.
* A megadott Portal értesítése: Igen
* Diagnosztikai naplózási szint: Figyelmeztetés
* Hatás:  Az összes kimeneti az Adatátalakítási hibák típuskonverziós hiba többek között a következők szerint kezeli a [kimeneti Adatszabályzat](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) beállítás.
* Napló részletei
   * Az oszlop neve.
   * Rekord azonosítója vagy a rekord része.

**Hibaüzenet**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* OK: Az üzenet értéke nagyobb, mint a támogatott kimeneti mérete. Például egy rekord egy Event Hub kimeneti 1 MB-nál nagyobb.
* A megadott Portal értesítése: Igen
* Diagnosztikai naplózási szint: Figyelmeztetés
* Hatás:  Az összes kimeneti az Adatátalakítási hibák rekord túllépése méretkorlát többek között a következők szerint kezeli a [kimeneti Adatszabályzat](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) beállítás.
* Napló részletei
   * Rekord azonosítója vagy a rekord része.

**Hibaüzenet**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* OK: Egy rekordot a rendszer oszlopként azonos nevű oszlop már tartalmaz. Például cosmos db kimeneti oszlop neve azonosítója, ha azonosító oszlop egy másik oszlopra.
* A megadott Portal értesítése: Igen
* Diagnosztikai naplózási szint: Figyelmeztetés
* Hatás:  Az összes kimeneti az Adatátalakítási hibák ismétlődő kulcsot is beleértve a következők szerint kezeli a [kimeneti Adatszabályzat](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) beállítás.
* Napló részletei
   * Az oszlop neve.
   * Rekord azonosítója vagy a rekord része.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics hibáinak elhárítása a diagnosztikai naplók használatával](stream-analytics-job-diagnostic-logs.md)

* [Megismerheti a Stream Analytics-feladat figyelése és lekérdezések figyelése](stream-analytics-monitoring.md)
