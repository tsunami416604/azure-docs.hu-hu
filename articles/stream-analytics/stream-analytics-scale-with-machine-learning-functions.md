---
title: Méretezés Machine Learning-függvények az Azure Stream Analytics szolgáltatásban
description: Ez a cikk ismerteti, amelyek Machine Learning-függvényekkel, a particionálás és a stream egységek konfigurálásával Stream Analytics-feladatok méretezése.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 115273086eeb88064c4b179f67d2d400d9f84692
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696098"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-functions"></a>Az Azure Machine Learning-függvényekkel a Stream Analytics-feladatok méretezése
Legyen nagyon egyszerű Stream Analytics-feladat beállítását, és néhány Mintaadat végigfuttatása. Mit kell? ha ellenőriznünk kell a magasabb adatkötettel ugyanez a feladat futtatása Ez megköveteli tőlünk, megtudhatja, hogyan konfigurálja a Stream Analytics-feladat, így a méretezés. Ebben a dokumentumban koncentrálunk a Machine Learning-függvényekkel a Stream Analytics-feladatok méretezése különleges szempontjait. A Stream Analytics-feladatok méretezése az általános információkat lásd: a cikk [feladatok méretezése](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Mi az az Azure Machine Learning-függvény a Stream Analytics?
A Stream Analytics a Machine Learning-függvény is használható, mint egy normál függvény hívásához szükséges a Stream Analytics lekérdezési nyelv a. Azonban a jelenet mögött a függvényhívások valójában az Azure Machine Learning Web Service kérelmeket is. Machine Learning webszolgáltatások támogatja a "kötegelés" több sort a azonos webszolgáltatás API-hívások, általános teljesítmény növelése érdekében a mini batch nevezett. További információkért lásd: [a Stream Analytics az Azure Machine Learning-függvények](https://blogs.technet.microsoft.com/machinelearning/2015/12/10/azure-ml-now-available-as-a-function-in-azure-stream-analytics/) és [Azure Machine Learning webszolgáltatások](../machine-learning/studio/consume-web-services.md).

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Machine Learning-függvényekkel a Stream Analytics-feladat konfigurálása
Stream Analytics-feladat egy Machine Learning-függvény konfigurálásakor nincsenek két paramétert kell figyelembe venni, a Machine Learning függvényhívások és a streamelési egységek (su), a Stream Analytics-feladat kiépítve a Köteg mérete. Ezeket a megfelelő értékek meghatározásáról, először egy kell döntést közötti teljesítmény, és a késés, a Stream Analytics-feladat, és mindegyik Adategység átviteli sebességet a késés. SUS-t mindig vehetők egy feladathoz, növelheti a teljesítményt és particionált Stream Analytics-lekérdezés, bár további SUS-t a feladat futtatásával járó költségeket.

Ezért fontos meghatározni a *tolerancia* a Stream Analytics-feladat futtatása a késés. A batch-méretet, amelyet a Stream Analytics-feladat késleltetése vegyületek természetes módon megnöveli a futtatását az Azure Machine Learning szolgáltatáskérések késést. Másrészről, köteg méretének növelése lehetővé teszi, hogy a Stream Analytics-feladat feldolgozása * további események az *azonos számú* a gépi tanulás webes kérelmeket. Gyakran a Machine Learning web service késés növekedése, a köteg méretének növekedésével sublinear, így fontos, hogy fontolja meg a leginkább költséghatékony kötegméret bármilyen adott helyzetben a Machine Learning webszolgáltatáshoz. Az alapértelmezett köteg mérete, a webszolgáltatás 1000, előfordulhat, hogy módosítható használatával kér a [Stream Analytics REST API](https://msdn.microsoft.com/library/mt653706.aspx "Stream Analytics REST API") vagy a [Stream PowerShell-ügyfél Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md "Stream Analytics-ügyfél PowerShell").

A Köteg mérete meghatározása után, amely a függvénynek szüksége van az események száma alapján egységek (su) az meghatározható, a streamelési száma másodpercenként feldolgozása. További információ a streamelési egységek: [Stream Analytics feladatok méretezése](stream-analytics-scale-jobs.md).

Általánosságban nincsenek 20 egyidejű kapcsolódások a Machine Learning-webszolgáltatást minden 6 SUS-t, azzal a különbséggel, hogy 1 SU-feladatok és a 3 SU feladat első 20 egyidejű kapcsolatok is.  Például ha a bemeneti adatokra vonatkozó 200 000 esemény / másodperc, és a kötegméret van hátra, a rendszer az alapértelmezett 1000 webes szolgáltatás késés az 1000 esemény mini batch, a 200 ms. Ez azt jelenti, hogy minden kapcsolat segítségével győződjön meg arról, öt kérés a Machine Learning web Service másodpercenként. 20 egyéni kapcsolattal a Stream Analytics-feladat feldolgozhatja a 200 ms 20 000 esemény, és ezért 100 000 események másodpercenként. 200 000 esemény feldolgozására, így a Stream Analytics-feladat 40 egyidejű kapcsolat, amely 12 SUS-t térjen kell. Az alábbi ábra a Stream Analytics-feladat kérelmeit, a Machine Learning web service-végpont – 6 SUS-t minden rendelkezik Machine Learning web Service 20 egyidejű kapcsolatok maximális száma.

![Stream Analytics méretezés Machine Learning-függvényekkel két feladat például](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "méretezési Stream Analytics a Machine Learning-függvényekkel két feladat példa")

Általánosságban véve ***B*** a Köteg mérete, ***L*** webes szolgáltatás késésének a Köteg mérete B (MS), az átviteli sebességet egy Stream Analytics feladat ***N*** SUs van:

![Méretezheti a Stream Analytics a Machine Learning függvények képletben](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Stream Analytics a Machine Learning függvények képletben méretezése")

Egy további figyelembe veszi a "max párhuzamos hívások" lehet a Machine Learning web service oldalon, azt javasoljuk, hogy ezt a maximális értéknél (200-as jelenleg).

Ezzel a beállítással kapcsolatban további információért olvassa el a [méretezés a cikk a Machine Learning webszolgáltatások](../machine-learning/studio/scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Példa – Hangulatelemzés
Az alábbi példában megtalálhatja az hangulatelemzés Machine Learning-függvény, a Stream Analytics-feladat leírtak szerint a [Stream Analytics Machine Learning integrációs oktatóanyagát](stream-analytics-machine-learning-integration-tutorial.md).

A lekérdezés végrehajtása egy egyszerű teljes követ lekérdezés particionálva az **vélemények** működhetnek a következő látható:

    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery

A következő esetet; 10 000, másodpercenként tweeteket egy sebessége (események) tweetek hangulatelemzés végrehajtásához egy Stream Analytics-feladat kell létrehozni. 1 SU használja, a Stream Analytics-feladat lehet tudja kezelni az adatforgalom? Alapértelmezett mérettel batch 1000 a feladat képesnek kell lennie a bemeneti tartani. Továbbá az új Machine Learning-függvény kell létrehoznia több mint egy másodperc késéssel, amely az általános alapértelmezett késését a hangulatelemzés Machine Learning webszolgáltatás (az alapértelmezett kötegméret 1000). A Stream Analytics-feladat **általános** vagy a végpontok közötti késés általában lehet néhány másodpercre. Részletesebb figyelje azokat a Stream Analytics-feladat *különösen* a Machine Learning függvényhívásokat. Egy átviteli sebesség 10 000 esemény, amelynek a Köteg mérete 1000, web Service igénybe körülbelül 10 kérést. 1 SU mellett is van elég egyidejű kapcsolatok, amelyek a bemeneti forgalmat.

Ha a bemeneti események száma növekszik a 100-szor, majd a Stream Analytics-feladat kell feldolgozni másodpercenként 1 000 000 tweeteket. A nagyobb méretezés elvégzéséhez két lehetőség van:

1. A köteg méretének növeléséhez, vagy
2. Partíció párhuzamosan az események feldolgozása a bemeneti stream

Az első lehetőség, a feladat a **késés** növeli.

A második lehetőség, a több SUS-t kellene üzembe helyezhető, és ezért készítése több egyidejű Machine Learning webszolgáltatási kérelmeket. Ez azt jelenti, hogy a feladat **költség** növeli.

Tegyük fel a hangulatelemzés Machine Learning webszolgáltatás késését 200 ms alatti, vagy 1000-esemény kötegek 5000-esemény kötegek 250 ms, 10 000-esemény kötegek vagy 500 ms 25 000-esemény kötegek 300 ms.

1. Az első lehetőség használatával (**nem** kiépítése több SUS-t). Sikerült a Köteg mérete növelhető **25 000**. Ez pedig lehetővé teszi a feladat 20 egyidejű kapcsolatok a Machine Learning web Service 1 000 000 eseményeinek feldolgozása (500 ms hívásonként késéssel). Így a Stream Analytics-feladat miatt a Machine Learning webszolgáltatási kérelmeket vélemények függvény kérelmeket a további késleltetés szeretné növelni a **200 ms** való **500 ms**. Azonban a kötegméret **nem** korlátlanul a Machine Learning-webszolgáltatások megköveteli a kérelem hasznos adat mérete 4 MB vagy kisebb növelni webszolgáltatás időtúllépése kérelmek művelet 100 másodperc múlva.
2. Használja a második lehetőség, a kötegméret marad, 1000, és 200 ms webes szolgáltatás késleltetés, a web Service minden 20 egyidejű kapcsolatok tudná események feldolgozása 1000 * 20 * 5 = 100 000, másodpercenként. Másodpercenként 1 000 000 eseményeinek feldolgozására, tehát a feladat kellene 60 SUS-t. Az első lehetőség képest, Stream Analytics-feladat biztosítja, további webszolgáltatás batch-kérelmek, egy nagyobb költséget viszont létrehozása.

Az alábbi, a táblát, az átviteli sebességet a Stream Analytics-feladat különböző SUS-t és a batch-méretek (események másodpercenkénti száma).

| Kötegméret (gépi tanulás késése) | 500 (200 ms) | 1,000 (200 ms) | 5,000 (250 ms) | 10,000 (300 ms) | 25 000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **3 SUS-t** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **6 SUS-t** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **12 SUS-t** |5000 |10,000 |40,000 |60,000 |100 000 |
| **18 SUS-t** |7500 |15,000 |60,000 |90,000 |150,000 |
| **24 SUS-t** |10,000 |20,000 |80,000 |120,000 |200,000 |
| **…** |… |… |… |… |… |
| **60 SUS-t** |25,000 |50,000 |200,000 |300,000 |500,000 |

Már rendelkezik már jól ismerik a Machine Learning-függvényekkel a Stream Analytics működését. Valószínűleg is ismernie, hogy a Stream Analytics-feladatok "PULL"parancs adatokat adatforrásokból, és minden egyes "PULL"parancs eseményeinek feldolgozása a Stream Analytics-feladat kötegelt adja vissza. Hogyan a lekéréses modell hatás a Machine Learning szolgáltatáskérések webes?

A Machine Learning-függvényekkel megadni kötegméret általában pontosan nem osztható minden Stream Analytics-feladat "PULL parancs" által visszaadott események száma. Ha ez történik, a Machine Learning web service-kötegek "részleges" nevezzük. Ez nem jár többletköltséggel további feladat késéses többletterhelést okoz az összevonási események lekéréses lekéréses történik.

## <a name="new-function-related-monitoring-metrics"></a>Új függvény kapcsolatos figyelési metrikák
A Stream Analytics-feladat figyelése területen három további függvény kapcsolatos metrikák érhetők el. Az alábbi ábrán látható módon FÜGGVÉNYKÉRÉSEK, a FÜGGVÉNY események és a sikertelen FÜGGVÉNYKÉRÉSEK legyenek.

![Méretezheti a Stream Analytics a Machine Learning funkciók metrikák](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "méretezése a Stream Analytics a Machine Learning funkciók metrikák")

A rendszer a következők:

**FÜGGVÉNYKÉRÉSEK**: A függvény kérések száma.

**FÜGGVÉNYESEMÉNYEK**: a függvény kérelmeket az események számát.

**SIKERTELEN FÜGGVÉNYKÉRÉSEK**: sikertelen függvény kérések száma.

## <a name="key-takeaways"></a>Kulcs Takeaways
Összefoglalva a fő pontokat, annak érdekében, hogy méretezhető Machine Learning-függvényekkel a Stream Analytics-feladat, a következő elemek figyelembe kell venni:

1. A bemeneti események száma
2. A eltűrt késését a futó Stream Analytics-feladat (és így a Machine Learning webszolgáltatási kérelmeket a Köteg mérete)
3. Az üzembe helyezett Stream Analytics SUS-t és a Machine Learning webszolgáltatási kérelmeket (a további funkciót kapcsolatos költségek) száma

Egy teljes körűen particionált Stream Analytics lekérdezési példa lett megadva. Ha egy összetettebb lekérdezésre van szükség a [Azure Stream Analytics-fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) további segítség kérése a Stream Analytics-csapattól egy nagyszerű erőforrás.

## <a name="next-steps"></a>További lépések
A Stream Analytics kapcsolatos további információkért lásd:

* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
