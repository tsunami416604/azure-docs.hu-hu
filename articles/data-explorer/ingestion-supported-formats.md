---
title: Az Azure Data Explorer által a betöltéshez támogatott adatformátumok.
description: Ismerje meg az Azure Data Explorer által a betöltéshez támogatott különböző adat- és tömörítési formátumokat.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 16ecdf7ac3f0062637e4bbea86d26e2560f38222
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235298"
---
# <a name="data-formats-supported-by-azure-data-explorer-for-ingestion"></a>Az Azure Data Explorer által támogatott adatformátumok a betöltéshez

Az adatok betöltése az a folyamat, amelynek során az adatok hozzáadódnak egy táblához, és elérhetővé válik a lekérdezés az Azure Data Explorerben. A lekérdezésből történő betöltésen kívül minden betöltési módszer esetében az adatoknak a támogatott formátumok egyikében kell lenniük. Az alábbi táblázat felsorolja és ismerteti azOkat a formátumokat, amelyeket az Azure Data Explorer támogat az adatok betöltéséhez.

|Formátum   |Mellék   |Leírás|
|---------|------------|-----------|
|Avro     |`.avro`     |[Avro tárolófájl](https://avro.apache.org/docs/current/). A következő kódok `null`támogatottak: , `deflate` (`snappy` jelenleg nem támogatott).|
|CSV      |`.csv`      |Vesszővel tagolt értékekkel (`,`) rendelkező szövegfájl. Lásd: [RFC 4180: _A vesszővel tagolt értékek (CSV) fájljainak közös formátuma és MIME-típusa_](https://www.ietf.org/rfc/rfc4180.txt).|
|JSON     |`.json`     |JSON-objektumokkal rendelkező szövegfájl, `\r\n`amelyet a vagy `\n` a hatorral határoltak. Lásd [JSON-vonalak (JSONL)](http://jsonlines.org/).|
|MultiJSON között|`.multijson`|Egy szöveges fájl, amelynek tulajdonságcsomagjai (amelyek mindegyike rekordnak jelöl), vagy tetszőleges számú, szóközön határolt tulajdonságcsomaggal, `\n` vagy `\r\n`. Minden ingatlan táska lehet elosztani több sorban. Ez a formátum `JSON`elsőbbséget, kivéve, ha az adatok nem ingatlan táskák.|
|Ork      |`.orc`      |Egy [ork fájl](https://en.wikipedia.org/wiki/Apache_ORC).|
|Parketta  |`.parquet`  |[Parkettafájl](https://en.wikipedia.org/wiki/Apache_Parquet).|
|Psv      |`.psv`      |Szövegfájl csővel elválasztott értékekkel (<code>&#124;</code>).|
|Nyers      |`.raw`      |Olyan szövegfájl, amelynek teljes tartalma egyetlen karakterlánc-érték.|
|Csv között     |`.scsv`     |Pontosvesszővel tagolt értékekkel (`;`) rendelkező szövegfájl.|
|SOHsv között    |`.sohsv`    |SOH-elválasztott értékekkel rendelkező szövegfájl. (Az SOH az ASCII kódpont 1; ezt a formátumot a Hive használja a HDInsight-on.)|
|Tsv      |`.tsv`      |Tabulátorral tagolt értékekkel`\t`( ) rendelkező szövegfájl.|
|TSVE     |`.tsv`      |Tabulátorral tagolt értékekkel`\t`( ) rendelkező szövegfájl. A meneküléshez`\`fordított perjel karakter ( ) használatos.|
|TXT      |`.txt`      |Szövegfájl a program malmára határolt sorokkal. `\n` Az üres sorok kimaradnak.|

## <a name="supported-data-compression-formats"></a>Támogatott adattömörítési formátumok

A blobok és a fájlok az alábbi tömörítési algoritmusok bármelyikén tömöríthetők:

|Tömörítés|Mellék|
|-----------|---------|
|Gzip       |.gz      |
|Irányítószám        |.zip     |

Jelölje meg a tömörítést úgy, hogy hozzáfűzi a bővítményt a blob vagy fájl nevéhez.

Példa:
* `MyData.csv.zip`CSV-ként formázott blobot vagy fájlt jelöl, ZIP-vel tömörítve (archívum vagy egyetlen fájl)
* `MyData.csv.gz`egy blobot vagy CSV formátumú fájlt jelöl, gzip-val tömörítve

Blob- vagy fájlnevek, amelyek nem tartalmazzák a formátumkiterjesztéseket, de csak a tömörítést (például ) is támogatottak. Ebben az esetben a fájlformátumot betöltési tulajdonságként kell megadni, mert nem lehet kikövetkeztetni.

> [!NOTE]
> Egyes tömörítési formátumok a tömörített adatfolyam részeként nyomon követik az eredeti fájlkiterjesztést. A fájlformátum meghatározásához ezt a kiterjesztést általában figyelmen kívül hagyja. Ha a fájlformátum nem határozható meg a (tömörített) blobvagy fájlnév `format` alapján, akkor azt a betöltési tulajdonságon keresztül kell megadni.

## <a name="next-steps"></a>További lépések

* További információ az [adatok betöltéséről](/azure/data-explorer/ingest-data-overview)
* További információ az [Azure Data Explorer adatbetöltési tulajdonságairól](ingestion-properties.md)
