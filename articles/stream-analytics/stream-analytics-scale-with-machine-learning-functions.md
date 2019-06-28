---
title: Méretezés Machine Learning-függvények az Azure Stream Analytics szolgáltatásban
description: Ez a cikk ismerteti, amelyek Machine Learning-függvényekkel, a particionálás és a stream egységek konfigurálásával Stream Analytics-feladatok méretezése.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 5da09d705246ffd5002a1a21daab2266525f579e
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357513"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-functions"></a>A Stream Analytics-feladatot az Azure Machine Learning Studio functions méretezése

Ez a cikk ismerteti a hatékony méretezése az Azure Stream Analytics-feladatok, amelyek használják az Azure Machine Learning-függvényekkel. A Stream Analytics-feladatok méretezése az általános információkat lásd: a cikk [feladatok méretezése](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Mi az az Azure Machine Learning-függvény a Stream Analytics?

A Stream Analytics a Machine Learning-függvény is használható, mint egy normál függvény hívásához szükséges a Stream Analytics lekérdezési nyelv a. A háttérben azonban az ezen függvényhívásokat is a valójában az Azure Machine Learning Web Service kérelmeket.

"Kötegelés" együtt, az azonos web service API-hívás több sort is javítható az átviteli sebességének Machine Learning webszolgáltatási kérelmeket. Ez a csoportosítás mini köteg neve. További információkért lásd: [Azure Machine Learning Studio-webszolgáltatások](../machine-learning/studio/consume-web-services.md). Az Azure Machine Learning Studio a Stream Analytics támogatása előzetes verzióban van.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Machine Learning-függvényekkel a Stream Analytics-feladat konfigurálása

Nincsenek konfigurálása a Stream Analytics-feladat által használt Machine Learning-függvény két paramétert:

* Meghívja a Köteg mérete, a Machine Learning-függvény.
* A folyamatos átviteli egységek számát (su) a Stream Analytics-feladat kiépítve.

A megfelelő értékek meghatározásáról SUS-t a, döntse el, hogy szeretné-e a Stream Analytics-feladat késleltetése vagy átviteli sebességének SU optimalizálása. SUS-t mindig hozzá egy feladatot, amely egy jól particionált Stream Analytics-lekérdezés növeléséhez. További SUs növelje meg a feladat futtatásával járó költségeket.

A késés meghatározása *tolerancia* a Stream Analytics-feladat. A köteg méretének növelésével növeli a az Azure Machine Learning szolgáltatás kérések késleltetésére és a Stream Analytics-feladat késését.

A köteg méretének növelése lehetővé teszi, hogy a Stream Analytics-feladat feldolgozása **további események** együtt a **azonos számú** a gépi tanulás webes kérelmeket. A Machine Learning web service késés növekedése általában sublinear a köteg méretének növekedésével. 

Fontos figyelembe venni a leginkább költséghatékony kötegméret bármilyen adott helyzetben a Machine Learning webszolgáltatáshoz. A webszolgáltatási kérelmeket kötegméret az alapértelmezett érték 1000. Az alapértelmezett méret használatával módosíthatja a [Stream Analytics REST API](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "Stream Analytics REST API") vagy a [Stream Analytics-ügyfél PowerShell](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

Miután kiválasztotta, az a Köteg mérete, is megadhatja a streamelési egységek (su), a függvénynek szüksége van az események száma alapján száma másodpercenként feldolgozása. További információ a streamelési egységek: [Stream Analytics feladatok méretezése](stream-analytics-scale-jobs.md).

Minden 6 SUS-t kaphat a Machine Learning web Service 20 egyidejű kapcsolatok. 1 SU-feladat, és 3 SU feladat első 20 egyidejű kapcsolatok.  

Ha az alkalmazás másodpercenként 200 000 eseményeket hoz létre, és a Köteg mérete 1000, a web service késés 200 ms. Ez a díjszabás, az azt jelenti, hogy minden olyan kapcsolatot kezdeményezhetik öt kérés a Machine Learning web Service másodpercenként. 20 egyéni kapcsolattal a Stream Analytics-feladat feldolgozhatja a 200 ms 20 000 események és 100 000 események másodpercenként.

200 000, másodpercenként események feldolgozását, a Stream Analytics-feladat 40 egyidejű kapcsolat, amely 12 SUS-t térjen kell rendelkeznie. Az alábbi ábra a Stream Analytics-feladat kérelmeit, a Machine Learning web service-végpont – 6 SUS-t minden rendelkezik Machine Learning web Service 20 egyidejű kapcsolatok maximális száma.

![Stream Analytics méretezés Machine Learning-függvényekkel két feladat például](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "méretezési Stream Analytics a Machine Learning-függvényekkel két feladat példa")

Általánosságban véve ***B*** a Köteg mérete, ***L*** webes szolgáltatás késésének a Köteg mérete B (MS), az átviteli sebességet egy Stream Analytics feladat ***N*** SUs van:

![Méretezheti a Stream Analytics a Machine Learning függvények képletben](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Stream Analytics a Machine Learning függvények képletben méretezése")

A "max párhuzamos hívások" a Machine Learning web service is elvégezheti. Azt javasoljuk, hogy a paraméter értéke a maximális értéket (200-as jelenleg).

Ezzel a beállítással kapcsolatban további információért tekintse át a [méretezés a cikk a Machine Learning webszolgáltatások](../machine-learning/studio/scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Példa – Hangulatelemzés
Az alábbi példában megtalálhatja az hangulatelemzés Machine Learning-függvény, a Stream Analytics-feladat leírtak szerint a [Stream Analytics Machine Learning integrációs oktatóanyagát](stream-analytics-machine-learning-integration-tutorial.md).

A lekérdezés végrehajtása egy egyszerű teljes követ lekérdezés particionálva az **vélemények** működik, az alábbi példában látható módon:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

Most vizsgálja meg a Stream Analytics-feladat, mely segített hangulatelemzés / 10 000 tweetek gyakorisággal tweetet a második létrehozásához szükséges konfigurációt.

1 SU használja, a Stream Analytics-feladat kezelheti a forgalom? A feladat továbbra is a bemeneti kötegmérettel az alapértelmezett 1000. Alapértelmezett késését a hangulatelemzés Machine Learning webszolgáltatás (az alapértelmezett kötegméret 1000) legfeljebb egy másodperc késéssel hoz létre.

A Stream Analytics-feladat **általános** vagy a végpontok közötti késés általában lehet néhány másodpercre. Részletesebb figyelje azokat a Stream Analytics-feladat *különösen* a Machine Learning függvényhívásokat. A Köteg mérete 1000, az egy átviteli sebesség 10 000 esemény körülbelül 10 kérést a webszolgáltatás vesz igénybe. Egy SU mellett is van elég egyidejű kapcsolatok, amelyek a bemeneti forgalmat.

Ha a bemeneti események száma növekszik a 100-szor, majd a Stream Analytics-feladat kell feldolgozni másodpercenként 1 000 000 tweeteket. A nagyobb méretezés elvégzéséhez két lehetőség van:

1. A köteg méretének növeléséhez.
2. Az események párhuzamos feldolgozásához adatfolyam-partíció.

Az első lehetőség, a feladat a **késés** növeli.

A második lehetőség lesz rendelkező kiépítése több SUS-t szeretné, hogy több egyidejű Machine Learning webszolgáltatási kérelmeket. SUS-t, a nagyobb számú növeli a feladat **költség**.

Nézzük meg a méretezési használja a következő késési mérések minden egyes köteg mérete:

| Késés | Köteg mérete |
| --- | --- |
| 200 ms | 1000-esemény kötegek és korábbi verziók |
| 250 ms | 5000-esemény kötegek |
| 300 ms | 10 000-esemény kötegek |
| 500 ms | 25 000-esemény kötegek |

1. Az első lehetőség használatával (**nem** kiépítése több SUS-t). Sikerült a Köteg mérete növelhető **25 000**. Ezzel a módszerrel a köteg méretének növelésével a feladat 20 egyidejű kapcsolatok a Machine Learning web Service 1 000 000 eseményeinek feldolgozása (500 ms hívásonként késéssel) lehetővé teszi. Így a Stream Analytics-feladat miatt a Machine Learning webszolgáltatási kérelmeket vélemények függvény kérelmeket a további késleltetés szeretné növelni a **200 ms** való **500 ms**. Azonban a kötegméret **nem** korlátlanul a Machine Learning-webszolgáltatások megköveteli a kérelem hasznos adat mérete 4 MB vagy kisebb növelni, valamint a szolgáltatás-kérelmek időtúllépési webes művelet 100 másodperc múlva.
1. Használja a második lehetőség, a kötegméret marad, 1000, 200 ezredmásodperces webes szolgáltatás késéssel, a web Service minden 20 egyidejű kapcsolatok tudná események feldolgozása 1000 * 20 * 5 = 100 000, másodpercenként. Másodpercenként 1 000 000 eseményeinek feldolgozására, tehát a feladat kellene 60 SUS-t. Az első lehetőség képest, Stream Analytics-feladat biztosítja, további webszolgáltatás batch-kérelmek, egy nagyobb költséget viszont létrehozása.

Az alábbi, a táblát, az átviteli sebességet a Stream Analytics-feladat különböző SUS-t és a batch-méretek (események másodpercenkénti száma).

| Kötegméret (gépi tanulás késése) | 500 (200 ms) | 1,000 (200 ms) | 5,000 (250 ms) | 10,000 (300 ms) | 25 000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **3 SUS-t** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **6 SUS-t** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **12 SUS-t** |5,000 |10,000 |40,000 |60,000 |100,000 |
| **18 SUS-t** |7,500 |15,000 |60,000 |90,000 |150,000 |
| **24 SUS-t** |10,000 |20,000 |80,000 |120,000 |200,000 |
| **…** |… |… |… |… |… |
| **60 SUS-t** |25,000 |50,000 |200,000 |300,000 |500,000 |

Már rendelkezik már jól ismerik a Machine Learning-függvényekkel a Stream Analytics működését. Valószínűleg is ismernie, hogy a Stream Analytics-feladatok "PULL"parancs adatokat adatforrásokból, és minden egyes "PULL"parancs eseményeinek feldolgozása a Stream Analytics-feladat kötegelt adja vissza. Hogyan a lekéréses modell hatás a Machine Learning szolgáltatáskérések webes?

A Machine Learning-függvényekkel megadni kötegméret általában pontosan nem osztható minden Stream Analytics-feladat "PULL parancs" által visszaadott események száma. Ha ez történik, a Machine Learning webszolgáltatás hívása "részleges" kötegek. Részleges kötegeket használatával elkerülheti a további feldolgozás késéses többletterhelést okoz az összevonási eseményeket generálnak lekéréses lekéréses.

## <a name="new-function-related-monitoring-metrics"></a>Új függvény kapcsolatos figyelési metrikák
A Stream Analytics-feladat figyelése területen három további függvény kapcsolatos metrikák érhetők el. Ezek **FÜGGVÉNYKÉRÉSEK**, **FÜGGVÉNYESEMÉNYEK** és **sikertelen FÜGGVÉNYKÉRÉSEK**, ahogy az alábbi ábrán látható.

![Méretezheti a Stream Analytics a Machine Learning funkciók metrikák](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "méretezése a Stream Analytics a Machine Learning funkciók metrikák")

A rendszer a következők:

**FÜGGVÉNYKÉRÉSEK**: A függvény kérések száma.

**FÜGGVÉNYESEMÉNYEK**: Az események számát felé küldött kérésekben a függvényt.

**SIKERTELEN FÜGGVÉNYKÉRÉSEK**: Sikertelen függvény kérések száma.

## <a name="key-takeaways"></a>Kulcs Takeaways

Méretezhető Machine Learning-függvényekkel a Stream Analytics-feladat, a következő tényezőket kell figyelembe venni:

1. A bemeneti események száma.
2. A futó Stream Analytics-feladat (és így a Machine Learning webszolgáltatási kérelmeket a Köteg mérete) eltűrt késése.
3. Az üzembe helyezett Stream Analytics SUS-t és a Machine Learning webszolgáltatási kérelmeket (a további funkciót kapcsolatos költségek) számát.

Egy teljes körűen particionált Stream Analytics lekérdezési példa lett megadva. Ha egy összetettebb lekérdezésre van szükség, a [Azure Stream Analytics-fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) további segítség kérése a Stream Analytics-csapattól egy nagyszerű erőforrás.

## <a name="next-steps"></a>További lépések
A Stream Analytics kapcsolatos további információkért lásd:

* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
