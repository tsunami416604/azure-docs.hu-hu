---
title: Válasszon valós idejű és streamelési feldolgozási megoldást az Azure-ban
description: Ismerje meg, hogyan választhatja ki a megfelelő valós idejű elemzési és streamelési feldolgozási technológiát az azure-beli alkalmazás létrehozásához.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 2146b1bd782aba5d98729a2d37d956744e469ba1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75860248"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Válasszon valós idejű elemzési és streamelési feldolgozási technológiát az Azure-ban

Számos szolgáltatás érhető el a valós idejű elemzéshez és a streamelésfeldolgozáshoz az Azure-ban. Ez a cikk azokkal az információkkal szolgál, amelyek alapján eldöntheti, hogy melyik technológia felel meg leginkább az alkalmazásnak.

## <a name="when-to-use-azure-stream-analytics"></a>Mikor kell használni az Azure Stream Analytics szolgáltatást?

Az Azure Stream Analytics az azure-beli adatfolyam-elemzés ajánlott szolgáltatása. Ez azt jelentette, a legkülönbözőbb forgatókönyvek, amelyek magukban foglalják, de nem kizárólagosan:

* Irányítópultok adatmegjelenítéshez
* Időbeli és térbeli mintákból vagy anomáliákból származó valós idejű [riasztások](stream-analytics-set-up-alerts.md)
* Kinyerési, átalakítási és betöltési (ETL) feladatok
* [Eseményforrás-minta](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

Az Azure Stream Analytics-feladat hozzáadása az alkalmazáshoz a leggyorsabb módja annak, hogy a streamelési elemzéseket az Azure-ban a már ismert SQL-nyelven használja. Az Azure Stream Analytics egy feladatszolgáltatás, így nem kell időt töltenie a fürtök kezelésével, és nem kell aggódnia az állásidő miatt a 99,9%-os SLA-val a feladat szintjén. A számlázás is történik a feladat szintjén így indítási költségek alacsony (egy streaming egység), de skálázható (akár 192 streaming egység). Sokkal költséghatékonyabb néhány Stream Analytics-feladat futtatása, mint a fürt futtatása és karbantartása.

Az Azure Stream Analytics gazdag, azonnali élményt nyújt. További beállítások nélkül azonnal kihasználhatja az alábbi funkciókat:

* Beépített időbeli operátorok, például [ablakos aggregátumok,](stream-analytics-window-functions.md)időbeli illesztések és időbeli analitikus függvények.
* Natív Azure [bemeneti](stream-analytics-add-inputs.md) és [kimeneti](stream-analytics-define-outputs.md) adapterek
* Lassú [módosítási referenciaadatok](stream-analytics-use-reference-data.md) (más néven keresőket) támogatása, beleértve a geokerítés térinformatikai referenciaadataihoz való csatlakozást is.
* Integrált megoldások, például [anomáliadetektálás](stream-analytics-machine-learning-anomaly-detection.md)
* Több időablak ugyanabban a lekérdezésben
* Több időbeli operátor tetszőleges szekvenciákban való összeállításának képessége.
* 100 ms-os végpontok közötti késés alatt az Event Hubs-ba érkező bemenettől az Event Hubs-ban történő kimeneti leszállásig, beleértve a hálózati késleltetést az Event Hubs-ból és az Event Hubs-ba, tartósan nagy átviteli sebességgel

## <a name="when-to-use-other-technologies"></a>Mikor kell más technológiákat használni?

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>UdF-eket, UDA-kat és egyéni deszerializálókat szeretne írni a JavaScripttől vagy a C-től eltérő nyelven #

Az Azure Stream Analytics támogatja a felhasználó által definiált függvények (UDF) vagy a felhasználó által definiált összesítések (UDA) a JavaScript a felhőbeli feladatok és a C# IoT Edge-feladatok. C# felhasználó által definiált deszerializálók is támogatottak. Ha egy deszerializáló, egy UDF vagy egy UDA más nyelveken, például a Java vagy a Python, használhatja a Spark strukturált streamelési. Az Event Hubs **EventProcessorHost-t** a saját virtuális gépeken is futtathatja tetszőleges streamelési feldolgozáshoz.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>A megoldás többfelhős vagy helyszíni környezetben van

Az Azure Stream Analytics a Microsoft saját technológiája, és csak az Azure-ban érhető el. Ha szüksége van a megoldás, hogy a felhők között vagy a helyszíni, fontolja meg a nyílt forráskódú technológiák, például a Spark strukturált streamelésvagy a Storm.

## <a name="next-steps"></a>További lépések

* [Stream Analytics-feladat létrehozása az Azure Portal használatával](stream-analytics-quick-create-portal.md)
* [Stream Analytics-feladat létrehozása az Azure PowerShell használatával](stream-analytics-quick-create-powershell.md)
* [Stream Analytics-feladat létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md)
* [Stream Analytics-feladat létrehozása a Visual Studio-kód használatával](quick-create-vs-code.md)
