---
title: Gépi tanulási függvények méretezése az Azure Stream Analytics szolgáltatásban
description: Ez a cikk ismerteti, hogyan skálázható stream analytics-feladatok, amelyek machine learning-függvényeket használó, particionálási és streamegységek konfigurálásával.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 5b08625d055063b3804a35a3344ff01c7edb79de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067007"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Stream Analytics-feladat méretezése az Azure Machine Learning Studio (klasszikus) funkcióival

> [!TIP]
> Erősen ajánlott [azure Machine Learning UDF-ek](machine-learning-udf.md) használata az Azure Machine Learning Studio (klasszikus) UDF helyett a jobb teljesítmény és megbízhatóság érdekében.

Ez a cikk bemutatja, hogyan lehet hatékonyan skálázható Azure Stream Analytics-feladatok, amelyek az Azure Machine Learning-függvények. A Stream Analytics-feladatok általános méretezésével kapcsolatos információkért tekintse meg a [Feladatok méretezése](stream-analytics-scale-jobs.md)című témakört.

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Mi az Azure Machine Learning-függvény a Stream Analytics ben?

A Machine Learning-függvény a Stream Analytics-ben a Stream Analytics lekérdezési nyelvén is használható, mint egy rendszeres függvényhívás. A színfalak mögött azonban ezek a függvényhívások valójában Azure Machine Learning webszolgáltatás-kérelmek.

Javíthatja a Machine Learning webszolgáltatás-kérelmek átviteli mertékét, ha ugyanabban a webszolgáltatás API-hívásában több sort "kötegel" együtt. Ezt a csoportosítást minikötegnek nevezzük. További információ: [Azure Machine Learning Studio (classic) Web Services](../machine-learning/studio/consume-web-services.md). Az Azure Machine Learning Studio (klasszikus) támogatása a Stream Analytics előzetes verzióban érhető el.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Stream Analytics-feladat konfigurálása Machine Learning-függvényekkel

A Stream Analytics-feladat által használt Machine Learning-függvény konfigurálásához két paraméter van:

* A Machine Learning függvényhívások kötegmérete.
* A Stream Analytics-feladathoz kiépített streamelési egységek (SUs-ok) száma.

A megfelelő értékek meghatározásához sus, döntse el, hogy szeretné-e optimalizálni a Stream Analytics-feladat késése vagy az egyes SU átviteli. SUs mindig hozzáadható egy feladathoz, hogy növelje a jól particionált Stream Analytics-lekérdezés átviteli. További SUs nem növeli a feladat futtatásának költségeit.

Határozza meg a Stream Analytics-feladat *késéstűréshatárát.* A kötegelt méret növelése növeli az Azure Machine Learning-kérelmek késését és a Stream Analytics-feladat késését.

A kötegelt méret növelése lehetővé teszi, hogy a Stream Analytics-feladat **több eseményt** dolgozzon fel **azonos számú** Machine Learning-webszolgáltatás-kérelemmel. A Machine Learning webszolgáltatás késésének növekedése általában allineáris a kötegméret növekedéséhez. 

Fontos, hogy fontolja meg a leginkább költséghatékony kötegméretet a Machine Learning webszolgáltatás egy adott helyzetben. A webszolgáltatás-kérelmek alapértelmezett kötegmérete 1000. Ezt az alapértelmezett méretet módosíthatja a [Stream Analytics REST API](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "Stream Analytics – REST API") vagy a Stream Analytics [PowerShell-ügyfélszolgáltatásával.](stream-analytics-monitor-and-manage-jobs-use-powershell.md)

Miután eldöntötte a kötegméretet, beállíthatja a streamelési egységek (SUs) számát a függvény által másodpercenként feldolgozandó események száma alapján. A streamelési egységekről a [Stream Analytics méretezési feladatok című](stream-analytics-scale-jobs.md)témakörben talál további információt.

Minden 6 SUs 20 egyidejű kapcsolatot kap a Machine Learning webszolgáltatáshoz. Azonban 1 SU-feladat és 3 SU-feladat 20 egyidejű kapcsolatot kap.  

Ha az alkalmazás másodpercenként 200 000 eseményt hoz létre, és a kötegmérete 1000, akkor az eredményül kapott webszolgáltatás-késés 200 ms. Ez a sebesség azt jelenti, hogy minden kapcsolat másodpercenként öt kérelmet tehet fel a Machine Learning webszolgáltatásnak. 20 kapcsolattal a Stream Analytics-feladat 20 000 eseményt képes feldolgozni 200 ms-ban és 100 000 eseményt egy másodperc alatt.

200 000 esemény másodpercenkénti feldolgozásához a Stream Analytics-feladatnak 40 egyidejű kapcsolatra van szüksége, amelyek 12 SUs-t hoznak létre. Az alábbi ábra bemutatja a Stream Analytics-feladat tól a Machine Learning webszolgáltatás végpont – minden 6 SUs rendelkezik 20 egyidejű kapcsolatot a Machine Learning webszolgáltatás max.

![Stream Analytics méretezése a Machine Learning Functions segítségével két feladat például](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Stream Analytics méretezése a Machine Learning Functions segítségével két feladat például")

Általában ***B*** a kötegméret, ***L*** a webszolgáltatás késése a kötegelt mérete B ezredmásodpercben, az átviteli egy Stream Analytics feladat ***N*** SUs-szal:

![Stream Analytics méretezése a Machine Learning Functions képletével](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Stream Analytics méretezése a Machine Learning Functions képletével")

A "maximális egyidejű hívások" a Machine Learning webszolgáltatás konfigurálható. Javasoljuk, hogy ezt a paramétert a maximális értékre állítsa be (jelenleg 200).

A beállításról további információt a [Machine Learning Web Services méretezési cikkében talál.](../machine-learning/studio/scaling-webservice.md)

## <a name="example--sentiment-analysis"></a>Példa – Hangulatelemzés
A következő példa egy Stream Analytics-feladatot tartalmaz a hangulatelemzési Machine Learning funkcióval, a [Stream Analytics Machine Learning integrációs oktatóanyagban leírtak szerint.](stream-analytics-machine-learning-integration-tutorial.md)

A lekérdezés egy egyszerű, teljesen particionált lekérdezés, amelyet a **hangulatfüggvény** követ, ahogy az a következő példában látható:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

Vizsgáljuk meg a Stream Analytics-feladat létrehozásához szükséges konfigurációt, amely másodpercenként 10 000 tweetes sebességgel elemzi a tweetek hangulatelemzését.

Az 1 SU használatával ez a Stream Analytics-feladat kezelni tudja a forgalmat? A feladat lépést tarthat a bemeneti az alapértelmezett 1000 kötegméretet. A hangulatelemzés imázselemzési gépi tanulási webszolgáltatás (1000 alapértelmezett kötegmérete) alapértelmezett késése legfeljebb egy másodpercnyi késést hoz létre.

A Stream Analytics-feladat **általános** vagy teljes körű késése általában néhány másodperc. Részletesebben tekintse meg ezt a Stream Analytics-feladatot, *különösen* a Machine Learning függvényhívásokat. 1000-es kötegmérettel 10 000 esemény átviteli célja körülbelül 10 kérelem a webszolgáltatás. Még egy SU esetén is elegendő egyidejű kapcsolat áll fenn a bemeneti forgalom befogadásához.

Ha a bemeneti esemény aránya 100x-rel nő, akkor a Stream Analytics-feladatnak másodpercenként 1 000 000 tweetet kell feldolgoznia. A nagyobb skála megvalósításának két lehetősége van:

1. Növelje a köteg méretét.
2. Particionálja a bemeneti adatfolyamot az események párhuzamos feldolgozásához.

Az első lehetőség, a feladat **késése** növekszik.

A második lehetőség, akkor ki kell építenie több SUs több egyidejű Machine Learning webszolgáltatás-kérelmek. Ez a nagyobb számú SUs növeli a projekt **költségét.**

Tekintsük meg a skálázást a következő késésmérésekkel az egyes kötegek méretéhez:

| Késés | Köteg mérete |
| --- | --- |
| 200 ms | 1000-esemény tételek vagy az alatt |
| 250 ms | 5000 eseményből álló köteg |
| 300 ms | 10 000 eseményköteg |
| 500 ms | 25 000 eseménysorozat |

1. Az első lehetőség használata (**nem** több SUs kiépítése). A köteg mérete **25 000-re**növelhető . A kötegméret ily módon való növelése lehetővé teszi, hogy a feladat 1 000 000 eseményt dolgozzon fel 20 egyidejű kapcsolattal a Machine Learning webszolgáltatással (hívásonként 500 ms késéssel). Így a Stream Analytics-feladat további késése a Machine Learning webszolgáltatás-kérelmekkel szembeni hangulatfüggvény-kérelmek miatt **200 ms-ról** **500 ms-ra**nő. A kötegméret azonban **nem** növelhető végtelenül, mivel a Machine Learning webszolgáltatások megköveteli, hogy a kérelem hasznos terhelési mérete 4 MB vagy kisebb legyen, és a webszolgáltatás 100 másodpercnyi működés után időtúlterhelést kér.
1. A második lehetőség használatával a köteg mérete marad 1000, 200 ms-os webszolgáltatás késés, minden 20 egyidejű kapcsolatot a webszolgáltatás képes lenne feldolgozni 1000 * 20 * 5 események = 100 000 másodpercenként. Tehát 1 000 000 esemény másodpercenkénti feldolgozásához a feladatnak 60 SUs-ra van szüksége. Az első lehetőséghez képest a Stream Analytics-feladat több webszolgáltatás-kötegkérelmet eredményezne, ami viszont megnövekedett költséget eredményezne.

Az alábbiakban egy táblázatot a Stream Analytics-feladat átviteli a különböző SUs és kötegméretek (események száma másodpercenként).

| kötegméret (ML késés) | 500 (200 ms) | 1000 (200 ms) | 5000 (250 ms) | 10 000 (300 ms) | 25 000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2500 |5000 |20000 |30,000 |50 000 |
| **3 Sus** |2500 |5000 |20000 |30,000 |50 000 |
| **6 Sus (6 Sus)** |2500 |5000 |20000 |30,000 |50 000 |
| **12 Sus** |5000 |10,000 |40,000 |60.000 |100 000 |
| **18 Sus** |7500 |15 000 |60.000 |90,000 |150 000 |
| **24 Sus** |10,000 |20000 |80,000 |120,000 |200,000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25,000 |50 000 |200,000 |300,000 |500 000 |

Mostantól már jól meg kell értenie, hogyan működik a Machine Learning a Stream Analytics működésében. Valószínűleg azt is megérti, hogy a Stream Analytics-feladatok "lekérik" az adatokat adatforrásokból, és minden egyes "lekéréses" események egy kötegét adja vissza a Stream Analytics feladat feldolgozásához. Hogyan befolyásolja ez a lekéréses modell a Machine Learning webszolgáltatás-kérelmeket?

Általában a Machine Learning-függvényekhez beállított kötegméret nem lesz pontosan osztható az egyes Stream Analytics-feladatok "lekérése" által visszaadott események számával. Ha ez bekövetkezik, a Machine Learning webszolgáltatás neve "részleges" kötegek. Részleges kötegek használatával elkerülhető, hogy további feladat késés imigázos ság iösszehangzó események lekéréses.

## <a name="new-function-related-monitoring-metrics"></a>Új, funkcióval kapcsolatos figyelési mutatók
A Stream Analytics-feladat Figyelő területén három további funkcióval kapcsolatos mérőszám került hozzáadásra. Ezek **függvénykérelmek,** **függvényesemények** és **sikertelen függvénykérelmek,** ahogy az alábbi ábrán látható.

![Stream Analytics méretezése a Machine Learning Functions metrikákkal](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Stream Analytics méretezése a Machine Learning Functions metrikákkal")

A meghatározás a következő:

**FÜGGVÉNYKÉRÉSEK**: A függvénykérések száma.

**FÜGGVÉNY ESEMÉNYEK**: A függvénykérelmekben lévő események száma.

**FAILED FÜGGVÉNYKÉRÉSEK**: A sikertelen függvénykérelmek száma.

## <a name="key-takeaways"></a>Kulcs elvihető ételek

A Stream Analytics-feladatok Machine Learning-függvényekkel való méretezéséhez vegye figyelembe a következő tényezőket:

1. A bemeneti esemény aránya.
2. A tűrésűrésa a futó Stream Analytics-feladat (és így a batch mérete a Machine Learning webszolgáltatás-kérelmek).
3. A kiépített Stream Analytics SUs és a Machine Learning webszolgáltatás-kérelmek száma (a további funkcióval kapcsolatos költségek).

Példaként egy teljesen particionált Stream Analytics-lekérdezést használtak. Ha összetettebb lekérdezésre van szükség, az [Azure Stream Analytics-fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) nagyszerű erőforrás a Stream Analytics csapat további segítségének beszerzéséhez.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a Stream Analytics szolgáltatásról, olvassa el a következő témakört:

* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
