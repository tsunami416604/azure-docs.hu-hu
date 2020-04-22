---
title: Elemezze & dolgozza fel a JSON-t az Apache Hive segítségével - Azure HDInsight
description: Ismerje meg, hogyan használhatja a JSON-dokumentumokat, és hogyan elemezheti őket az Apache Hive használatával az Azure HDInsightban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: 8e0abf780589207b065b7262afb99de81e625fe8
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732208"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>JSON-dokumentumok feldolgozása és elemzése az Apache Hive használatával az Azure HDInsightban

Ismerje meg, hogyan dolgozhat fel és elemezhet JavaScript-objektumnotúcot (JSON) fájlokat az Apache Hive használatával az Azure HDInsightban. Ez a cikk a következő JSON-dokumentumot használja:

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

A fájl a `wasb://processjson@hditutorialdata.blob.core.windows.net/`található. Az Azure Blob storage HDInsight-szolgáltatással való használatáról a [HDInsight HDFS-kompatibilis Azure Blob-tároló használata az Apache Hadoop szolgáltatással](../hdinsight-hadoop-use-blob-storage.md)című témakörben talál további információt. A fájlt átmásolhatja a fürt alapértelmezett tárolójába.

Ebben a cikkben az Apache Hive konzolt használja. A Hive konzol megnyitásáról az [Apache Ambari Hive View használata apache hadoop használatával a HDInsightban című témakörben talál.](apache-hadoop-use-hive-ambari-view.md)

> [!NOTE]  
> A Hive-nézet már nem érhető el a HDInsight 4.0-s verzióban.

## <a name="flatten-json-documents"></a>JSON-dokumentumok összeolvasztása

A következő szakaszban felsorolt módszerek hez a JSON-dokumentumnak egyetlen sorból kell lennie. Ezért a JSON-dokumentumot karakterláncba kell simítani. Ha a JSON-dokumentum már összevan olvasztva, kihagyhatja ezt a lépést, és egyenesen a JSON-adatok elemzéséről szóló következő szakaszra léphet. A JSON-dokumentum összeolvasztásához futtassa a következő parancsfájlt:

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

A nyers JSON-fájl `wasb://processjson@hditutorialdata.blob.core.windows.net/`a helyen található. A **StudentsRaw** Hive tábla a nyers JSON-dokumentumra mutat, amely nem lapított.

A **StudentsOneLine** Hive tábla tárolja az adatokat a HDInsight alapértelmezett fájlrendszer alatt a **/json/students/** elérési út.

Az **INSERT** utasítás feltölti a **StudentOneLine** táblát az összeolvasztott JSON-adatokkal.

A **SELECT** utasítás csak egy sort ad vissza.

Itt van a **SELECT** utasítás kimenete:

![A JSON-dokumentum HDInsight-összeolvasztása](./media/using-json-in-hive/hdinsight-flatten-json.png)

## <a name="analyze-json-documents-in-hive"></a>JSON-dokumentumok elemzése a Hive-ban

Hive három különböző mechanizmusok at futtatni lekérdezéseket JSON-dokumentumok, vagy írhat a saját:

* Használja a get_json_object felhasználó által definiált függvényt (UDF).
* Használja a json_tuple UDF.
* Használja az egyéni szerializáló/deszerializáló (SerDe).
* Írja meg saját UDF python vagy más nyelvek használatával. A saját Python-kód Hive-mal való futtatásáról a [Python UDF apache hive-vel és Apache Pig alkalmazással](./python-udf-hdinsight.md)című témakörben talál további információt.

### <a name="use-the-get_json_object-udf"></a>A get_json_object UDF használata

Hive biztosít egy beépített UDF nevű [get_json_object,](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) amely lekérdezi a JSON futásidőben. Ez a módszer két argumentumot vesz fel: a tábla nevét és a metódus nevét. A metódus neve az összeolvasztott JSON-dokumentummal és az elemzésre váró JSON mezővel rendelkezik. Nézzünk egy példát, hogy hogyan működik ez az UDF.

A következő lekérdezés minden tanuló keresztnevét és vezetéknevét adja vissza:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

A lekérdezés konzolablakban történő futtatásakor az alábbiak at a következő kimenettel teszi le:

![Az Apache Hive json objektumUDF-et kap](./media/using-json-in-hive/hdinsight-get-json-object.png)

Az UDF get_json_object korlátai vannak:

* Mivel a lekérdezés minden egyes mezője a lekérdezés újraelemzéséhez szükséges, ez hatással van a teljesítményre.
* **Get\_JSON_OBJECT()** függvény egy tömb karakterlánc-ábrázolását adja vissza. Ahhoz, hogy ezt a tömböt Hive tömbdé alakítsa, reguláris kifejezéseket kell használnia a "[" és "]" szögletes zárójelek cseréjéhez, majd a tömb lehívásához meg kell hívnia a splitet.

Ez az átalakítás az oka annak, hogy a Hive wiki azt javasolja, hogy használja **json_tuple.**  

### <a name="use-the-json_tuple-udf"></a>A json_tuple UDF használata

Egy másik UDF által nyújtott Hive hívják [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), ami nem jobb, mint [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Ez a módszer kulcsokat és JSON-karakterláncot vesz igénybe. Ezután értékek egy tősúját adja vissza. A következő lekérdezés a diákazonosítót és a JSON-dokumentum osztályzatát adja vissza:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

A parancsfájl kimenete a Hive konzolon:

![Apache Hive json lekérdezés eredményei](./media/using-json-in-hive/hdinsight-json-tuple.png)

Az `json_tuple` UDF az [oldalsó nézet](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) szintaxisát használja a\_Hive-ban, amely lehetővé teszi, hogy a json-törzs virtuális táblát hozzon létre az UDT függvény alkalmazásával az eredeti tábla minden egyes sorára. Az oldalsó **nézet**ismételt használata miatt a komplex JSON-ok túl nehézkessé válnak. Továbbá, **JSON_TUPLE** nem tudja kezelni a beágyazott JSON-okat.

### <a name="use-a-custom-serde"></a>Egyéni SerDe használata

A SerDe a legjobb választás beágyazott JSON-dokumentumok elemzéséhez. Lehetővé teszi a JSON-séma definiálását, majd a séma segítségével elemezheti a dokumentumokat. További információt az [Egyéni JSON-serde használata a Microsoft Azure HDInsight szolgáltatással kapcsolatban.](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/)

## <a name="summary"></a>Összefoglalás

A Hive-ban választott JSON-operátor típusa a forgatókönyvtől függ. Egy egyszerű JSON-dokumentummal és egy kikeresni kívánt mezővel válassza a Hive UDF **get_json_object.** Ha egynél több billentyűt kell keresnie, akkor használhatja **a json_tuple**. Beágyazott dokumentumok esetén használja a **JSON SerDe**.

## <a name="next-steps"></a>További lépések

Kapcsolódó cikkek, lásd:

* [Az Apache Log4j minta elemzéséhez az Apache Struktúra és a HiveQL és az Apache Hadoop segítségével elemezheti a minta Apache log4j fájlt](../hdinsight-use-hive.md)
* [A járatkésési adatok elemzése a HDInsight interaktív lekérdezése segítségével](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
