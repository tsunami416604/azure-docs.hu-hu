---
title: Az Azure Stream Analytics & AzureML functions méretezése feladat |} Microsoft Docs
description: Ismerje meg a megfelelő méretezése a Stream Analytics-feladatok (particionálás, SU mennyiség és egyebek) használata az Azure Machine Learning funkciók.
keywords: ''
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: 47ce7c5e-1de1-41ca-9a26-b5ecce814743
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: dd6effab3ba0b411131414bd757ffe8cc54e49d2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-functions"></a>A Stream Analytics-feladat az Azure Machine Learning functions méretezése
Legtöbbször könnyen állítson be egy Stream Analytics-feladat, és néhány adatot végigfuttatása. Mit kell végezzük, amikor nagyobb adatmennyiség futtassa ugyanazt a feladatot? Szükséges, hogy ismerje meg, hogyan konfigurálhatja a Stream Analytics-feladat, úgy, hogy a méretezés. Ebben a dokumentumban azt összpontosítani a Machine Learning függvényekkel Stream Analytics-feladatok méretezése különleges szempontjait is. A Stream Analytics-feladatok méretezése általános információ: a cikk [feladatok skálázás](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Mi az az Azure Machine Learning függvény a Stream Analytics?
A Stream Analytics a Machine Learning függvény használható például a Stream Analytics lekérdezési nyelv a rendszeres függvény hívásakor. Azonban a leképezni kívánt jelenetben mögött a függvényhívások ténylegesen Azure Machine Learning webszolgáltatás kérelmeket is. Machine Learning webszolgáltatások támogatja a "kötegelés" több sort minimális kötegelt, az azonos web service API-hívásban, teljes átviteli sebesség növelése érdekében nevezzük. A következő cikkekben talál további részleteit. [Azure Machine Learning funkciók a Stream Analytics](https://blogs.technet.microsoft.com/machinelearning/2015/12/10/azure-ml-now-available-as-a-function-in-azure-stream-analytics/) és [Azure Machine Learning webszolgáltatások](../machine-learning/studio/consume-web-services.md).

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>A Stream Analytics-feladat Machine Learning funkciók konfigurálása
A gépi tanulás funkció Stream Analytics-feladat konfigurálásakor nincsenek két paramétert kell figyelembe venni, a Köteg mérete, a Machine Learning függvényhívások, és a folyamatos átviteli egységeket (SUS-t), a Stream Analytics-feladat kiépítve. Ezek a megfelelő értékek meghatározásáról, először egy kell döntést közötti késleltetés és a teljesítményt, ez azt jelenti, hogy a Stream Analytics-feladat, és minden SU átviteli várakozási ideje. SUS-t mindig vehető fel egy feladatot, amely növelheti a teljesítményt is particionált Stream Analytics-lekérdezés, bár a feladatok futtatásának további SUS-t.

Ezért fontos, hogy a *tolerancia* késés a Stream Analytics-feladat futtatása. Az Azure Machine Learning szolgáltatáskérések futtatásával további késés természetes növeli a Köteg mérete, amely vegyületek a Tiltás késése a Stream Analytics-feladat. Másrészt a köteg méretének növelése lehetővé teszi, hogy a Stream Analytics-feladat feldolgozni * további események a *ugyanannyi* a Machine Learning webszolgáltatás a szolgáltatáskéréseket. Gyakran a Machine Learning web service késés növekedése a köteg méretének növekedésével sublinear, ezért fontos figyelembe venni a Machine Learning webszolgáltatásba bármely adott helyzetben a leginkább költséghatékony kötegméret. Az alapértelmezett kötegméret a webszolgáltatás-kérelmek 1000, ezért használatával módosítható a [Stream Analytics REST API](https://msdn.microsoft.com/library/mt653706.aspx "Stream Analytics REST API") vagy a [Stream Analytics-ügyfél PowerShell](stream-analytics-monitor-and-manage-jobs-use-powershell.md "Stream Analytics-ügyfél PowerShell").

A Köteg mérete meghatározása után az adatfolyam egységek (SUs) meghatározható, határozzák meg, amelyet a függvény az események száma másodpercenként feldolgozása. További információ az adatfolyam-egységek: [Stream Analytics feladatok méretezése](stream-analytics-scale-jobs.md).

Általában nincsenek 20 egyidejű kapcsolatok a Machine Learning webszolgáltatáshoz tartozó minden 6 SUS-t, azzal a különbséggel, hogy a feladatokat. SU 1. és 3 SU feladat első 20 egyidejű kapcsolatok is.  Például ha a bemeneti adatok sebessége 200 000 események másodpercenkénti és a Köteg mérete az alapértelmezett 1000 marad az eredményül kapott webes szolgáltatás várakozási ideje 1000 események minimális kötegelt 200 ms. Ez azt jelenti, hogy minden kapcsolat tehet öt kéréseket a Machine Learning webszolgáltatás egy másik. 20 kapcsolatot a Stream Analytics-feladat feldolgozhatja a 20 000 a 200 ms és ezért 100 000 események másodpercenként. 200 000 események másodpercenkénti feldolgozni, így a Stream Analytics-feladat 40 egyidejű kapcsolat, amely 12 SUs térjen kell. A következő ábra szemlélteti a Stream Analytics-feladat kérelmeit a Machine Learning webszolgáltatás végpontja – 6 SUS-t minden rendelkezik Machine Learning webszolgáltatáshoz 20 egyidejű kapcsolatok maximális száma.

![A Stream Analytics méretezése a Machine Learning funkciók két feladat példa](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "méretezési Stream Analytics a Machine Learning funkciók két feladat példa")

Általában ***B*** a Köteg mérete ***L*** web service késésének a Köteg mérete B ezredmásodpercben, az átviteli sebessége a Stream Analytics a feladat ***N*** SUs van:

![A Stream Analytics a Machine Learning funkciók képlet méretezni](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Stream Analytics a Machine Learning funkciók képlet méretezni")

Egy további figyelembe lehet, hogy a "max egyidejű hívások" a Machine Learning web service oldalon, a maximális értéket (jelenleg 200) beállítani a javasoljuk.

Ezzel a beállítással kapcsolatban további tájékoztatást a [méretezés a cikk a Machine Learning webszolgáltatások](../machine-learning/studio/scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Példa – véleményeket elemzés
A következő példában egy Stream Analytics-feladat véleményeket elemzése Machine Learning-függvény, lásd: a [Stream Analytics Machine Learning integrációs oktatóanyag](stream-analytics-machine-learning-integration-tutorial.md).

Ez egy egyszerű teljesen particionálva lekérdezés követ a **véleményeket** működhetnek a következő látható:

    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery

A következő példa; 10 000 Twitter-üzenetek / s átviteli sebességgel a Stream Analytics-feladat létre kell hozni véleményeket elemzést, a Twitter-üzeneteket (esemény). 1 SU használ, a Stream Analytics-feladat lehet tudja kezelni a forgalom? Az alapértelmezett kötegméret 1000 a feladat meg kell tudja tartani a bemenetet. További a hozzáadott Machine Learning-függvény létrehozzon egy második, a késés, amely az általános több alapértelmezett várakozási ideje a céggel kapcsolatos véleményeket elemzési Machine Learning webszolgáltatásba (az alapértelmezett kötegméret 1000). A Stream Analytics-feladat **általános** vagy végpontok közötti késés állna néhány másodpercig. További részletes tekintse meg azokat a Stream Analytics-feladat *különösen* a Machine Learning függvényhívásokat. 1000, amelynek a Köteg mérete, 10 000 események átviteli érvénybe körülbelül 10 kérelmek webszolgáltatáshoz. 1 SU használatát, még nincsenek elég egyidejű kapcsolatok, amelyek a bemeneti forgalmat.

De mi történik, ha a bemeneti események gyakorisága növekszik 100 x, és most a Stream Analytics-feladatban kell feldolgozni másodpercenként 1 000 000 Twitter-üzeneteket? Két lehetőség áll rendelkezésre:

1. A köteg méretének növeléséhez vagy
2. A bemeneti adatfolyam feldolgozni az eseményeket az párhuzamos partíció

Az első lehetőség, a feladat a **késés** növeli.

A második beállítás több SUS-t kell üzembe helyezve, és ezért a Machine Learning web service több egyidejű kérelmet létrehozni. Ez azt jelenti, hogy a feladat **költség** növeli.

Feltételezik, a várakozási a céggel kapcsolatos véleményeket elemzési Machine Learning webszolgáltatásba 200 ms 1000-esemény kötegek, vagy az alábbi 250 ms 5000-esemény kötegek, 300 ms 10 000-esemény kötegek vagy 25 000-esemény kötegek 500 ms.

1. Az első lehetőség használatával (**nem** több SUs kiépítése), a Köteg mérete sikerült növelni a **25 000**. Ez pedig lehetővé válik a feladat a Machine Learning webszolgáltatás 20 egyidejű kapcsolatok 1 000 000 események feldolgozásához (hívás / 500 ms késéssel). Így a Stream Analytics-feladat a céggel kapcsolatos véleményeket függvény kéréseket a meghatározott a Machine Learning webszolgáltatás-kérelmek miatt további késését a kell emelni **200 ms** való **500 ms**. Azonban kötegméret **nem** végtelenül a Machine Learning webszolgáltatások megköveteli a terhelés méretének egy kérelem 4 MB vagy kisebb növelni webszolgáltatás kérelmek időtúllépési művelet 100 másodperc múlva.
2. A második beállítás használatával, a kötegméret marad, 1000, és 200 ms web service késleltetés, minden 20 egyidejű kapcsolatok száma a webszolgáltatással tudná 1000 * 20 * 5 eseményeinek = 100 000 száma másodpercenként. 1 000 000 események másodpercenkénti feldolgozni, tehát a feladat kellene 60 SUS-t. Az első lehetőség képest, Stream Analytics-feladat tenné további web service kötegelt kérelmekben, egy nagyobb költséget viszont létrehozásakor.

Az alábbiakban a táblát, az átviteli sebessége a Stream Analytics-feladat van különböző SUS-t és a Köteg mérete (az események másodpercenkénti számát).

| Köteg mérete (a gépi tanulás összetevő várakozási ideje) | 500 (200 ms) | 1,000 (200 ms) | 5,000 (250 ms) | 10,000 (300 ms) | 25,000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **3 SUS-t** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **6 SUS-t** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **12 SUS-t** |5,000 |10,000 |40,000 |60,000 |100,000 |
| **18 SUS-t** |7,500 |15,000 |60,000 |90,000 |150,000 |
| **24 SUS-t** |10,000 |20,000 |80,000 |120,000 |200,000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25,000 |50,000 |200,000 |300,000 |500,000 |

Mostanra akkor már rendelkezik Stream Analytics a Machine Learning funkciók működése beható ismerete. Valószínűleg is tudomásul veszi, hogy a Stream Analytics-feladatok "pull" adatokat adatforrásokból és minden egyes "pull" egy események feldolgozását a Stream Analytics-feladat kötegelt adja vissza. Hogyan a lekéréses modell hatás a Machine Learning szolgáltatáskérések webes?

A Köteg mérete, a Machine Learning funkciók hivatott általában pontosan nem osztható minden Stream Analytics-feladat "pull" által visszaadott események száma. Ha ez történik, a Machine Learning webszolgáltatáshoz "részleges" kötegek nevezik. Ez annak lekéréses lekéréses további feldolgozás várakozási ideje terhelés összevonási eseményekben nem számítunk történik.

## <a name="new-function-related-monitoring-metrics"></a>Új, a függvény kapcsolatos figyelési metrikákat
A Stream Analytics-feladat figyelő területen három további függvény kapcsolatos metrikákat lettek hozzáadva. Az alábbi ábrán látható módon FÜGGVÉNY, FÜGGVÉNY események és sikertelen FÜGGVÉNY KÉRELMEKET.

![A Stream Analytics a Machine Learning funkciók metrikák méretezése](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Stream Analytics a Machine Learning funkciók metrikák méretezése")

Az elem az alábbiak szerint definiáltuk:

**FÜGGVÉNY kérelmek**: függvény kérelmek számát jelenti.

**FÜGGVÉNY események**: A szám a függvény kérelmek események.

**SIKERTELEN FÜGGVÉNY kérelmek**: sikertelen függvény kérelmek számát jelenti.

## <a name="key-takeaways"></a>Kulcs Takeaways
Összefoglalva a főbb pontjai ahhoz, hogy a Stream Analytics-feladat a Machine Learning funkciók méretezhető, a következő elemek figyelembe kell venni:

1. A bemeneti események száma
2. A megengedett várakozási futó Stream Analytics-feladat (és ezáltal a Köteg mérete, a Machine Learning web service kérelmek)
3. A kiépített Stream Analytics SUS-t és a gépi tanulás webszolgáltatás-kérelmek (a további függvény kapcsolatos költségek) száma

Egy teljesen particionált Stream Analytics lekérdezési példaként használt. Egy összetettebb lekérdezés van szüksége a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics) kiváló forrást biztosítanak a további kapcsolatos segítség kérése a Stream Analytics csapat van.

## <a name="next-steps"></a>További lépések
A Stream Analytics kapcsolatos további információkért lásd:

* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
