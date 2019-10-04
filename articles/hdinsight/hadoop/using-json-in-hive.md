---
title: JSON-dokumentumok elemzése és feldolgozása Apache Hive az Azure HDInsight
description: Megtudhatja, hogyan használhatja a JSON-dokumentumokat, és hogyan elemezheti őket az Azure HDInsight Apache Hive használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 629a7c98a7b46b470470445cc56a6f53d9e4f4b4
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077213"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>JSON-dokumentumok feldolgozása és elemzése Apache Hive használatával az Azure HDInsight

Megtudhatja, hogyan dolgozhatja fel és elemezheti JavaScript Object Notation (JSON) fájlokat az Azure HDInsight Apache Hive használatával. Ez a cikk a következő JSON-dokumentumot használja:

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

A fájl a következő címen érhető `wasb://processjson@hditutorialdata.blob.core.windows.net/`el:. Az Azure Blob Storage és a HDInsight használatával kapcsolatos további információkért lásd: [HDFS-kompatibilis Azure Blob Storage használata a HDInsight-ben való Apache Hadoop](../hdinsight-hadoop-use-blob-storage.md). A fájlt átmásolhatja a fürt alapértelmezett tárolójába.

Ebben a cikkben a Apache Hive-konzolt használja. A kaptár-konzol megnyitásával kapcsolatos utasításokért lásd: az [Apache Ambari kaptár nézet használata a HDInsight-ben való Apache Hadoop](apache-hadoop-use-hive-ambari-view.md).

## <a name="flatten-json-documents"></a>JSON-dokumentumok összeolvasztása
A következő szakaszban felsorolt metódusok megkövetelik, hogy a JSON-dokumentum egyetlen sorból legyen kibontva. Ezért a JSON-dokumentumot egy sztringbe kell lelapulnia. Ha a JSON-dokumentum már össze van tömörítve, kihagyhatja ezt a lépést, és közvetlenül a következő szakaszhoz juthat a JSON-adatok elemzéséhez. A JSON-dokumentum lelapulához futtassa a következő parancsfájlt:

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

A nyers JSON-fájl a következő `wasb://processjson@hditutorialdata.blob.core.windows.net/`helyen található:. A **StudentsRaw** struktúra táblázata a nyers JSON-dokumentumra mutat, amely nem lapított.

A **StudentsOneLine** -struktúra tábla a **/JSON/Students/** elérési útja alatt tárolja az HDInsight alapértelmezett fájlrendszerében tárolt értékeket.

Az **Insert** utasítás feltölti a **StudentOneLine** TÁBLÁT az összeolvasztott JSON-adatokkal.

A **Select** utasítás csak egy sort ad vissza.

Itt látható a **Select** utasítás kimenete:

![A JSON-dokumentum HDInsight összeolvasztása](./media/using-json-in-hive/hdinsight-flatten-json.png)

## <a name="analyze-json-documents-in-hive"></a>JSON-dokumentumok elemzése a kaptárban
A kaptár három különböző mechanizmust biztosít a lekérdezések JSON-dokumentumokon való futtatásához, vagy saját maga is írhat:

* Használja a get_json_object felhasználó által definiált függvényt (UDF).
* Használja a json_tuple UDF-t.
* Használja az egyéni szerializáló/deszerializáló (SerDe) használatát.
* Saját UDF-t írhat a Python vagy más nyelvek használatával. A saját Python-kódok struktúrával való futtatásával kapcsolatos további információkért lásd: [Python UDF Apache Hive és Apache Pig használatával] [hdinsight-Python].

### <a name="use-the-get_json_object-udf"></a>A get_json_object UDF használata
A kaptár egy [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) nevű beépített UDF-t biztosít, amely a Futtatás közben JSON-lekérdezéseket hajt végre. Ez a metódus két argumentumot vesz igénybe: a tábla neve és a metódus neve, amely az összeolvasztott JSON-dokumentummal és az elemezni kívánt JSON-mezővel rendelkezik. Lássunk egy példát, hogy lássuk, hogyan működik ez az UDF.

A következő lekérdezés az egyes tanulók vezetéknevét és vezetéknevét adja vissza:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

A következő kimenet jelenik meg, amikor futtatja ezt a lekérdezést a konzol ablakban:

![Apache Hive JSON-objektum beolvasása UDF](./media/using-json-in-hive/hdinsight-get-json-object.png)

A get_json_object UDF korlátai vannak:

* Mivel a lekérdezés egyes mezői a lekérdezés újraelemzését igénylik, hatással vannak a teljesítményre.
* **A\_Get JSON_OBJECT ()** függvény egy tömb sztring-ábrázolását adja vissza. Ahhoz, hogy a tömböt egy struktúra-tömbre konvertálja, reguláris kifejezésekkel kell helyettesítenie a szögletes zárójeleket ([) és a "]"), majd meg kell hívnia a felosztást a tömb beszerzéséhez.

Ezért javasoljuk, hogy a kaptár wiki a json_tuple használatát javasolja.  

### <a name="use-the-json_tuple-udf"></a>A json_tuple UDF használata
A kaptár által biztosított egy másik UDF-t a [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple)nevezik, amely jobb, mint a [get_ JSON-_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Ez a metódus kulcsok és JSON-karakterláncok készletét veszi át, és az értékek egy rekordját egy függvény használatával adja vissza. A következő lekérdezés a tanulói azonosítót és a JSON-dokumentum fokozatát adja vissza:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

A szkript kimenete a kaptár-konzolon:

![Apache Hive JSON-lekérdezés eredményei](./media/using-json-in-hive/hdinsight-json-tuple.png)

Az json_tuple UDF az [oldalirányú nézet](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) szintaxisát használja a kaptárban, amely\_lehetővé teszi, hogy a JSON-rekord hozzon létre egy virtuális táblázatot úgy, hogy a UDT függvényt alkalmazza az eredeti tábla minden egyes sorára. Az összetett JSON-okból az **oldalirányú nézet**ismételt használata miatt túlságosan nehézkesek lesznek. Emellett a **JSON_TUPLE** nem tudja kezelni a beágyazott JSON-ket.

### <a name="use-a-custom-serde"></a>Egyéni SerDe használata
A SerDe a legjobb választás a beágyazott JSON-dokumentumok elemzéséhez. Lehetővé teszi a JSON-séma definiálását, majd a séma segítségével elemezheti a dokumentumokat. Útmutatásért lásd: [Egyéni JSON-SerDe használata Microsoft Azure HDInsight használatával](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Összegzés
Végezetül, a kiválasztott kaptárban található JSON-operátor típusa a forgatókönyvtől függ. Ha egy egyszerű JSON-dokumentummal rendelkezik, és csak egyetlen mezővel rendelkezik a kereséshez, dönthet úgy, hogy a kaptár UDF-get_json_object használja. Ha egynél több kulccsal rendelkezik a kereséshez, használhatja a json_tuple. Ha beágyazott dokumentummal rendelkezik, akkor a JSON-SerDe kell használnia.

## <a name="next-steps"></a>További lépések

Kapcsolódó cikkek esetében lásd:

* [A Apache Hive és a HiveQL használata a HDInsight Apache Hadoop a minta Apache log4j-fájl elemzéséhez](../hdinsight-use-hive.md)
* [Repülési késleltetési adatelemzések elemzése a HDInsight interaktív lekérdezés használatával](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Twitter-adatelemzések elemzése Apache Hive használatával a HDInsight-ben](../hdinsight-analyze-twitter-data-linux.md)
