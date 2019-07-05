---
title: Válassza ki a valós idejű elemzési és adatfolyam-feldolgozási technológia az Azure-ban
description: További információ arról, hogy válassza a jobb valós idejű elemzési és folyamatos átviteli feldolgozási technology létrehozza az alkalmazást az Azure-ban.
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: f46a35d971c008b61d4899e30101ea562d3cefea
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483411"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Válassza ki a valós idejű elemzési és adatfolyam-feldolgozási technológia az Azure-ban

Nincsenek elérhető valós idejű elemzési és a streamelési feldolgozására az Azure számos szolgáltatást. Ez a cikk a eldönteni, melyik technológia a legmegfelelőbb az alkalmazáshoz szükséges információkat.

## <a name="when-to-use-azure-stream-analytics"></a>Mikor érdemes használni az Azure Stream Analytics

Az Azure Stream Analytics ajánlott szolgáltatása a stream Analytics az Azure-ban. Azt jelenti, számos különböző forgatókönyveket, amelyek közé tartozik, de nem korlátozódik:

* Az irányítópultok adatmegjelenítéshez
* Valós idejű [riasztások](stream-analytics-set-up-alerts.md) historikus és térbeli mintázatok vagy anomáliák
* Kinyerési, átalakítási és betöltési (ETL) feladatok
* [Event Sourcing minta](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

Hozzáadása egy Azure Stream Analytics az alkalmazás feladata a leggyorsabb módot kínálni arra le adatfolyam-továbbítási analytics fel, és futtatása az Azure-ban, az SQL nyelv használatával már jól ismert. Az Azure Stream Analytics szolgáltatása egy feladat, így nem kell töltött idő a fürtök kezelését, és nem kell aggódnia az állásidő, a feladat szinten 99,9 %-os SLA. Számlázási is történik, így alacsony költségek indítási feladat szinten (egy folyamatos átviteli egység), de méretezhető (akár 192 Streamelési egységek). Célszerű a jóval költséghatékonyabb, mint futtassa, és a egy fürt kezelése néhány Stream Analytics-feladatok futtatásához.

Az Azure Stream Analytics egy gazdag-az-beépített tapasztalattal rendelkezik. Azonnal igénybe veheti a következő szolgáltatások további beállítások nélkül:

* Beépített az ideiglenes operátorok, mint például [ablakos összesítéseket](stream-analytics-window-functions.md), időalapú illesztéseket, és a historikus elemzési funkciók.
* Natív Azure [bemeneti](stream-analytics-add-inputs.md) és [kimeneti](stream-analytics-define-outputs.md) adapterek
* A lassan változó támogatási [referenciaadatok](stream-analytics-use-reference-data.md) (más néven a keresési táblák), beleértve a geokerítések térinformatikai adatokkal való csatlakozás.
* Integrált megoldásokat, mint például [Anomáliadetektálás](stream-analytics-machine-learning-anomaly-detection.md)
* Több idő windows ugyanabban a lekérdezésben
* Lehetővé teszi tetszőleges feladatütemezések több historikus operátor compose.
* 100 ms alatt az Event Hubs, az Event Hubs alkotóelemeit kimeneti érkező bemeneti végpontok közötti késés többek között a hálózati késést, és az Event Hubsba, tartósan nagy adatátviteli kapacitással

## <a name="when-to-use-other-technologies"></a>Mikor érdemes használni más technológiákkal

### <a name="you-need-to-input-from-or-output-to-kafka"></a>A bemeneti vagy kimeneti kafka kell

Az Azure Stream Analytics nem rendelkezik egy Apache Kafka bemeneti vagy kimeneti adapter. Ha üzenetsorokra az események vagy kell küldenie a Kafka, és nem kell saját Kafka-fürt futtatásához egy követelményt, folytathatja a Stream Analytics használatával az események küldése az Event Hubs használatával az Event Hubs Kafka API-t az esemény küldő módosítása nélkül. Ha saját Kafka-fürt futtatásához szükséges, a Spark strukturált Stream, amelyek teljes körűen támogatja az használhatja [Azure Databricks](../azure-databricks/index.yml), vagy a Storm [Azure HDInsight](../hdinsight/storm/apache-storm-overview.md).

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>Nem a JavaScript nyelvű UDF-EK, Uda és egyéni deserializers írási szeretne, vagyC#

Az Azure Stream Analytics támogatja a felhasználó által definiált függvények (UDF) vagy a felhasználó által definiált összesítések (UDA) a JavaScript a felhőalapú feladat, és C# IoT Edge-feladatok. C#felhasználó által definiált deserializers is támogatottak. Ha szeretné végrehajtani egy deszerializáló, egy UDF és UDA más nyelveken, például Java vagy Python, a Spark strukturált Stream is használhatja. Az Event Hubs is futtathat **EventProcessorHost** tetszőleges adatfolyam-feldolgozó ehhez a saját virtuális gépeken.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>A megoldás több felhőalapú vagy helyszíni környezetben van

Az Azure Stream Analytics a Microsoft saját fejlesztésű technológia, és csak az Azure-ban érhető el. Ha az eredményesebb lehet a hordozható felhőket vagy a helyszíni megoldás van szüksége, fontolja meg a nyílt forráskódú technológiák, például a Spark strukturált Stream vagy Storm.

## <a name="next-steps"></a>További lépések

* [Stream Analytics-feladat létrehozása az Azure portal használatával](stream-analytics-quick-create-portal.md)
* [Stream Analytics-feladat létrehozása az Azure PowerShell-lel](stream-analytics-quick-create-powershell.md)
* [Stream Analytics-feladat létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md)
* [Stream Analytics-feladat létrehozása a Visual Studio Code használatával](quick-create-vs-code.md)