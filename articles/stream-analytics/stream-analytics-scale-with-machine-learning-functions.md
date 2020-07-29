---
title: Machine Learning függvények méretezése Azure Stream Analytics
description: Ez a cikk azt ismerteti, hogyan méretezhetők a Machine Learning függvényeket használó Stream Analytics feladatok a particionálási és a stream-egységek konfigurálásával.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2020
ms.openlocfilehash: 1493a15a97ca88d0ed914f78b1906088c03dff10
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037409"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>A Stream Analytics-feladatok skálázása Azure Machine Learning Studio (klasszikus) függvényekkel

> [!TIP]
> A jobb teljesítmény és megbízhatóság érdekében javasoljuk, hogy Azure Machine Learning Studio (klasszikus) UDF helyett [Azure Machine learning UDF](machine-learning-udf.md) használjon.

Ez a cikk azt ismerteti, hogyan lehet hatékonyan méretezni Azure Stream Analytics Azure Machine Learning függvényeket használó feladatokat. A Stream Analytics feladatok általános méretezésével kapcsolatos információkért tekintse meg a [feladatok skálázását](stream-analytics-scale-jobs.md)ismertető cikket.

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Mi az a Azure Machine Learning függvény a Stream Analyticsban?

A Stream Analytics Machine Learning függvénye, például a Stream Analytics lekérdezési nyelvben egy normál függvény hívásához is használható. A színfalak mögött azonban ezek a függvények ténylegesen Azure Machine Learning webszolgáltatási kérelmeket.

Az Machine Learning webszolgáltatási kérelmek átviteli sebességét a "batching" több sorból is javíthatja ugyanabban a webszolgáltatás API-hívásban. Ezt a csoportosítást mini-batch-nek nevezzük. További információ: [Azure Machine learning Studio (klasszikus) webszolgáltatások](../machine-learning/studio/consume-web-services.md). A Stream Analytics Azure Machine Learning Studio (klasszikus) támogatása előzetes verzióban érhető el.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Stream Analytics-feladatok konfigurálása Machine Learning függvényekkel

A Stream Analytics feladat által használt Machine Learning függvényt két paraméterrel konfigurálhatja:

* A Machine Learning függvény meghívásának batch-mérete.
* A Stream Analytics feladatokhoz kiépített folyamatos átviteli egységek (SUs) száma.

Az SUs megfelelő értékeinek meghatározásához döntse el, hogy szeretné-e optimalizálni a Stream Analytics feladatok késését vagy az egyes SU átviteli sebességét. Az SUs mindig hozzáadható egy feladatokhoz, így növelheti a jól particionált Stream Analytics lekérdezések átviteli sebességét. A további SUs megnövelheti a feladatok futtatásának költségeit.

Határozza meg a Stream Analyticsi feladatokhoz tartozó késési *toleranciát* . A köteg méretének növelése növeli a Azure Machine Learning kérelmek késését és a Stream Analytics feladatok késését.

A Batch méretének növelése lehetővé teszi, hogy a Stream Analytics feladatok **több eseményt** is feldolgozzanak **ugyanazzal a számú** Machine learning webszolgáltatási kéréssel. A Machine Learning webszolgáltatás késésének növekedése általában lineáris a Batch méretének növeléséhez. 

Fontos figyelembe venni a Machine Learning webszolgáltatások leghatékonyabb batch-méretét az adott helyzetben. A webszolgáltatási kérelmek alapértelmezett mérete 1000. Ezt az alapértelmezett méretet a Stream Analytics [Stream Analytics REST API](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "Stream Analytics – REST API") vagy a [PowerShell-ügyfél](stream-analytics-monitor-and-manage-jobs-use-powershell.md)használatával módosíthatja.

Ha úgy döntött, hogy a Batch-méretet választotta, beállíthatja a folyamatos átviteli egységek számát azon események száma alapján, amelyeket a függvénynek másodpercenként kell feldolgoznia. További információ a folyamatos átviteli egységekről: [stream Analytics skálázási feladatok](stream-analytics-scale-jobs.md).

Minden 6 SUs 20 egyidejű kapcsolatot kap a Machine Learning webszolgáltatással. Azonban 1 SU feladat és 3 SU feladat 20 egyidejű kapcsolatot kap.  

Ha az alkalmazás másodpercenként 200 000 eseményt hoz létre, és a köteg mérete 1000, akkor az eredményül kapott webszolgáltatás késése 200 MS. Ez az arány azt jelenti, hogy minden kapcsolat öt kérést tesz elérhetővé a Machine Learning webszolgáltatásnak másodpercenként. A 20 kapcsolattal a Stream Analytics feladatokkal 20 000 eseményt dolgozhat fel 200 MS-és 100 000-eseményekbe egy másodperc alatt.

Az 200 000-események másodpercenkénti feldolgozásához a Stream Analytics feladatoknak 40 egyidejű kapcsolatokra van szükségük, amelyek a 12 SUs-hez tartoznak. Az alábbi ábra a Stream Analytics-feladatokból a Machine Learning webszolgáltatás-végpontra irányuló kérelmeket mutatja be – minden 6 SUs 20 egyidejű kapcsolattal rendelkezik a Machine Learning webszolgáltatás max.

![Stream Analytics méretezése a Machine Learning functions két feladatának példája](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Stream Analytics méretezése a Machine Learning functions két feladatának példája")

A ***"b"*** köteg mérete, a webszolgáltatási késés a (z) "b" köteg méretében ***, Stream Analytics*** a (z) ***N***

![Stream Analytics méretezése Machine Learning functions-képlettel](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Stream Analytics méretezése Machine Learning functions-képlettel")

Az Machine Learning webszolgáltatáson az "egyidejű hívások maximális száma" beállítás is megadható. Ajánlott ezt a paramétert a maximális értékre beállítani (jelenleg 200).

A beállítással kapcsolatos további információkért tekintse át [Machine learning webszolgáltatások méretezési cikkét](../machine-learning/studio/create-endpoint.md).

## <a name="example--sentiment-analysis"></a>Példa – Hangulatelemzés
Az alábbi példa egy Stream Analytics feladatot tartalmaz az a [Stream Analytics Machine learning Integration oktatóanyagban](stream-analytics-machine-learning-integration-tutorial.md)ismertetett, a hangulat elemzése Machine learning függvénnyel.

A lekérdezés egy egyszerű, teljes particionált lekérdezés, amelyet az **érzelem** függvény követ, ahogy az az alábbi példában is látható:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

Vizsgáljuk meg a Stream Analytics feladatok létrehozásához szükséges konfigurációt, amely a tweetek elemzését a másodpercenként 10 000 tweetek alapján végzi.

Az 1 SU használatával a Stream Analytics-feladatok kezelni tudják a forgalmat? A művelet az alapértelmezett batch 1000-as mérettel lépést tarthat a bemenettel. Az érzelmek elemzése Machine Learning webszolgáltatás alapértelmezett késése (a 1000-as alapértelmezett mérettel) nem hoz létre több késést.

A Stream Analytics-feladathoz tartozó **teljes** vagy végpontok közötti késés általában néhány másodperc. Részletesebben tekintse át ezt a Stream Analytics feladatot, *különösen* a Machine learning függvény hívásait. A Batch mérete 1000, a 10 000-es események átviteli sebessége körülbelül 10 kérelmet tesz a webszolgáltatásnak. Még egy SU esetében is elég egyidejű kapcsolat áll rendelkezésre a bemeneti forgalom befogadásához.

Ha a bemeneti esemény sebessége a 100x értékkel növekszik, akkor a Stream Analytics feladatnak másodpercenként 1 000 000 tweetet kell feldolgoznia. A megnövekedett méretezés két lehetőséggel valósítható meg:

1. Növelje a köteg méretét.
2. Particionálja a bemeneti adatfolyamot, hogy párhuzamosan dolgozza fel az eseményeket.

Az első lehetőséggel a feladatok **késése** növekszik.

A második lehetőséggel további SUs-t kell kiépíteni, hogy több párhuzamos Machine Learning webszolgáltatási kérelem legyen. Ez a nagyobb számú SUs, növeli a feladatok **költségeit**.

Nézzük meg a skálázást az egyes batch-méretek következő késési mértékének használatával:

| Késés | Köteg mérete |
| --- | --- |
| 200 MS | 1000 – eseményvezérelt kötegek vagy alul |
| 250 MS | 5 000 – eseményvezérelt kötegek |
| 300 MS | 10 000 – eseményvezérelt kötegek |
| 500 ms | 25 000 – eseményvezérelt kötegek |

1. Az első lehetőség használata (további SUs kiépítés**nélkül** ). A köteg mérete **25 000**-ra növelhető. A Batch méretének növelése lehetővé teszi, hogy a feladatok feldolgozzák a 1 000 000-es eseményt 20 egyidejű kapcsolattal a Machine Learning webszolgáltatással (a 500 ms-os késéssel). Így a Stream Analytics feladat további késése az Machine Learning webszolgáltatási kérelmekkel szembeni, a hangulatra vonatkozó kérések miatt **200 MS** -ról **500 MS**-ra emelkedett. A Batch mérete azonban **nem** növelhető végtelenül, mivel a Machine learning webszolgáltatások esetében a kérések adattartalmának mérete 4 MB vagy kisebb lehet, és a webszolgáltatás a 100-as művelet után időtúllépési időt igényel.
1. A második lehetőség használatával a köteg mérete 1000-kor marad, az 200-MS webszolgáltatás késése esetén pedig a webszolgáltatás 20 egyidejű kapcsolata képes lesz az 1000 * 20 * 5 esemény = 100 000 másodpercenkénti feldolgozására. Így a 1 000 000-események másodpercenkénti feldolgozásához a feladatoknak 60 SUs-re van szükségük. Az első lehetőséghez képest Stream Analytics a feladatok több webszolgáltatási batch-kérést is igénybe vennének, ami nagyobb költségeket eredményez.

Alább látható a különböző SUs-és batch-méretekhez tartozó Stream Analytics-feladatok átviteli sebességének táblázata (az események másodpercenkénti száma).

| köteg mérete (ML késleltetés) | 500 (200 MS) | 1 000 (200 MS) | 5 000 (250 MS) | 10 000 (300 MS) | 25 000 (500 MS) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2500 |5000 |20000 |30 000 |50,000 |
| **3 SUs** |2500 |5000 |20000 |30 000 |50,000 |
| **6 SUs** |2500 |5000 |20000 |30 000 |50,000 |
| **12 SUs** |5000 |10,000 |40,000 |60.000 |100.000 |
| **18 SUs** |7500 |15 000 |60.000 |90,000 |150 000 |
| **24 SUs** |10,000 |20000 |80,000 |120 000 |200,000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25,000 |50,000 |200,000 |300 000 |500 000 |

Mostantól már jól megértette, hogyan Machine Learning függvények a Stream Analytics működésében. Valószínűleg azt is tudomásul veszi, hogy Stream Analytics feladatok "lekéréses" adatai az adatforrásokból és az egyes "lekérések" az Stream Analytics feladathoz tartozó események kötegét adja vissza. Hogyan befolyásolja ez a lekérési modell a Machine Learning webszolgáltatási kérelmeket?

A Machine Learning függvényekhez beállított batch-méret általában nem osztható meg pontosan az egyes Stream Analytics feladatok "lekérése" által visszaadott események számával. Ebben az esetben a Machine Learning webszolgáltatást "részleges" kötegekkel hívja meg a rendszer. A részleges kötegek használata elkerüli, hogy a lekéréses művelet során felmerülő további feladatok késése coalescing-eseményeknél.

## <a name="new-function-related-monitoring-metrics"></a>Új függvényekkel kapcsolatos figyelési metrikák
Egy Stream Analytics feladat figyelés területén három további függvényhez kapcsolódó metrika lett hozzáadva. Ezek a **függvények,** a **függvények** és a **sikertelen függvények kérései**, az alábbi ábrán látható módon.

![Stream Analytics méretezése Machine Learning functions-metrikákkal](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Stream Analytics méretezése Machine Learning functions-metrikákkal")

A a következőképpen van meghatározva:

**Function kérelmek**: a függvények kéréseinek száma.

**Function Events**: a függvény kéréseinek száma.

**Sikertelen függvények kérései**: a sikertelen függvények kéréseinek száma.

## <a name="key-takeaways"></a>Kulcs elvihető

Stream Analytics feladatok Machine Learning függvényekkel való méretezéséhez vegye figyelembe a következő tényezőket:

1. A bemeneti esemény díjszabása.
2. A futó Stream Analytics feladatokhoz (és így a Machine Learning webszolgáltatási kérelmek batch-méretéhez) tartozó tolerált késés.
3. A kiépített Stream Analytics SUs és a Machine Learning webszolgáltatási kérelmek száma (a további függvényekkel kapcsolatos költségek).

Példaként egy teljesen particionált Stream Analytics lekérdezést használtak. Ha összetettebb lekérdezésre van szükség, a [Microsoft Q&a Azure stream Analytics egy kérdés oldalát](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html) , amellyel további segítséget kaphat a stream Analytics csapattól.

## <a name="next-steps"></a>További lépések
A Stream Analyticsról további információt a következő témakörben talál:

* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
