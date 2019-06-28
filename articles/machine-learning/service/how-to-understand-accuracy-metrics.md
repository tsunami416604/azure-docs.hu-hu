---
title: Pontosság metrikák az automatikus ML képzés
titleSuffix: Azure Machine Learning service
description: További információ az automatikus a machine learning pontossága metrikák minden, a futtatások.
author: j-martens
ms.author: jmartens
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 06/20/2019
ms.openlocfilehash: 44dfa387b289afe4dc5f030cca0b13325c04e811
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313300"
---
# <a name="evaluate-training-accuracy-in-automated-ml-with-metrics"></a>Metrikák az automatikus ml képzési pontossága kiértékelése

Többféle módon futtatási törzsének képzési pontossága metrikákat tekinthet meg.

* Használat [Jupyter widget](how-to-track-experiments.md#view-run-details)
* Használat [a `get_metrics()` függvény](how-to-track-experiments.md#query-run-metrics) bármely `Run` objektum
* Nézet [a kísérlet mérőszámok az Azure Portalon](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal)

## <a name="classification-metrics"></a>Besorolási metrikák

A következő metrikák besorolási feladat futtatási törzsének lesznek mentve.

|Metrika|Leírás|Számítás|További paraméterek
--|--|--|--|
AUC_Macro| AUC az a terület, a fogadó működő jellemző görbe alatt. Makró minden egyes osztály a AUC számtani középértékét.  | [Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | átlagos = "makra."|
AUC_Micro| AUC az a terület, a fogadó működő jellemző görbe alatt. Micro globálisan számított a valódi pozitívok és az egyes osztályok vakriasztások kombinálásával| [Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | átlagos = "micro"|
AUC_Weighted  | AUC az a terület, a fogadó működő jellemző görbe alatt. Az eredmény az egyes osztályok az egyes osztályok igaz példányok száma szerint számtani súlyozott van.| [Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|átlagos = "súlyozott"
accuracy|Pontosság pontosan egyezik a valódi címkéket előre jelzett címkékhez százaléka. |[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |None|
average_precision_score_macro|Átlagos pontosság pontosság-visszahívási görbe elért minden egyes küszöbértéket, a korábbi küszöbértéket, a súly használt fogyasztóktól növekedés az szükséges, a súlyozott átlag foglalja össze. Makró az egyes osztályok átlagos pontosság pontszám középértéke|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|átlagos = "makra."|
average_precision_score_micro|Átlagos pontosság pontosság-visszahívási görbe elért minden egyes küszöbértéket, a korábbi küszöbértéket, a súly használt fogyasztóktól növekedés az szükséges, a súlyozott átlag foglalja össze. Micro globálisan számított weboldalak százmilliárdjainak biztosítják a valódi pozitívok és a téves, minden egyes megszakítási szerint|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|átlagos = "micro"|
average_precision_score_weighted|Átlagos pontosság pontosság-visszahívási görbe elért minden egyes küszöbértéket, a korábbi küszöbértéket, a súly használt fogyasztóktól növekedés az szükséges, a súlyozott átlag foglalja össze. Súlyozott van számtani átlagos pontosság az eredmény az egyes osztályok az egyes osztályok igaz példányok száma szerint|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|átlagos = "súlyozott"|
balanced_accuracy|Elosztott terhelésű pontosságát az egyes osztályok visszaírási számtani középértékét.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlagos = "makra."|
f1_score_macro|F1 pontszám: közepének pontosság és a visszahívás. Makró az egyes osztályok F1 pontszám középértéke|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|átlagos = "makra."|
f1_score_micro|F1 pontszám: közepének pontosság és a visszahívás. Micro globálisan számított alapján a teljes valódi pozitívok, a téves negatív és a hamis pozitív jelzések elkerülése|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|átlagos = "micro"|
f1_score_weighted|F1 pontszám: közepének pontosság és a visszahívás. Súlyozott átlag által az egyes osztályok F1 pontszám osztály gyakorisága|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|átlagos = "súlyozott"|
log_loss|Ez az a veszteség függvény a logisztikai regressziós (multinomial) és -bővítmények, például a Neurális hálózatokat definiálva a valószínűségi besorolás előrejelzéseket megadott igaz címkék negatív log valószínűségét, használt. Egyetlen minta igaz a címke a yt {0,1} és becsült yp valószínűsége, hogy yt = 1, a napló adatvesztés a - P jelentkezzen (yt&#124;yp) =-(yt log(yp) + (1 – yt) log (1 – yp))|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|None|
norm_macro_recall|Normalizált makró visszahívása makró ne felejtse el, hogy a véletlenszerű teljesítmény 0 pontszámot pedig tökéletes teljesítmény 1 pontszámot normalized. Ez úgy érhető el, norm_macro_recall: (recall_score_macro - R) = /(1-R), ahol az R véletlenszerű ismeretekkel (azaz a R = 0,5 bináris osztályozási) és a C osztályú osztályozási problémák R=(1/C) recall_score_macro várt értéke|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlagos = "makró", majd (recall_score_macro - R) /(1-R), ahol az R véletlenszerű ismeretekkel (azaz a R = 0,5 bináris osztályozási) és a C osztályú osztályozási problémák R=(1/C) recall_score_macro várt értéke|
precision_score_macro|Pontosság a százalékos aránya a következő címkét: egy adott osztály elemeit tartalmazza ténylegesen az adott osztály. Makró az egyes osztályok pontosság számtani középértékét|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|átlagos = "makra."|
precision_score_micro|Pontosság a százalékos aránya a következő címkét: egy adott osztály elemeit tartalmazza ténylegesen az adott osztály. Micro globálisan számított alapján a teljes valódi pozitívok és a hamis pozitív jelzések elkerülése|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|átlagos = "micro"|
precision_score_weighted|Pontosság a százalékos aránya a következő címkét: egy adott osztály elemeit tartalmazza ténylegesen az adott osztály. Minden osztály az egyes osztályok igaz példányok száma szerint a pontossági számtani súlyozott van.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|átlagos = "súlyozott"|
recall_score_macro|Visszaírási valójában egy bizonyos osztály elemeinek megfelelően van-e jelölve, hogy százaléka. Makró az egyes osztályok visszaírási középértéke|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlagos = "makra."|
recall_score_micro|Visszaírási valójában egy bizonyos osztály elemeinek megfelelően van-e jelölve, hogy százaléka. Micro globálisan számított a teljes valódi pozitívok, a téves negatív leltár szerint|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlagos = "micro"|
recall_score_weighted|Visszaírási valójában egy bizonyos osztály elemeinek megfelelően van-e jelölve, hogy százaléka. Minden osztály az egyes osztályok igaz példányok száma szerint visszaírási számtani középértékét súlyozott van.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|átlagos = "súlyozott"|
weighted_accuracy|Ahol a súlyt kapnak valamennyi példa megegyezik az időarány, amíg igaz példányok adott példa igaz osztályban súlyozott pontossága pontossága|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight egy adott osztály az egyes elemekhez az időarány, amíg a célzott egyenlő vektor|

## <a name="regression-and-forecasting-metrics"></a>Regresszió és előrejelzési mérőszámok

A következő metrikák regressziós vagy előrejelzési feladat futtatási törzsének lesznek mentve.

|Metrika|Leírás|Számítás|További paraméterek
--|--|--|--|
explained_variance|MAGYARÁZAT eltérés az időarány, amíg, amelyhez a modell matematikai fiókok számára egy adott adatkészlet változata. A százalékos varianciát a varianciát a hibák az eredeti adatok csökkenése. Az átlag, a hibák értéke 0, esetén egyenlő magyarázat eltérés.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|None|
r2_score|R2 meghatározása vagy a képest egy alapkonfiguráció modellt, amely a mean squared hibák százalékos csökkenését a relatív. Az átlag, a hibák értéke 0, esetén egyenlő magyarázat eltérés.|[Számítás](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|None|
spearman_correlation|Valamennyi korrelációs a két adatkészlet között fennálló monotonicity nonparametric mértékegysége. A Pearson-korrelációs eltérően a valamennyi korrelációs nem feltételezi azt, hogy mindkét olyan adatkészlettel általában oszlanak meg. Más korrelációs együttható, például az egyik platformjától függően -1 és + 1 nincs korreláció úgy 0-val. -1 és + 1 összefüggéseket egy pontos monoton kapcsolatot jelenti. Pozitív összefüggéseket jelenti azt, hogy x növekszik, hogy mit y. Negatív összefüggéseket jelenti azt, hogy növeli az x y csökken.|[Számítás](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|None|
mean_absolute_error|Jelenti azt, hogy abszolút hiba a várt értékkel, az abszolút értékét a cél- és az előrejelzési közötti különbség|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|None|
normalized_mean_absolute_error|Normalizált átlagos abszolút hiba osztva az adatok tartományán mean Absolute Error|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Az adatok számos nullával|
median_absolute_error|Közepes abszolút hiba a cél- és az előrejelzési közötti összes abszolút eltérések középértékének. Ezen adatveszteség robusztus a kiugró értékeket.|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|None|
normalized_median_absolute_error|Normalizált medián abszolút hiba osztva az adatok tartományán medián abszolút hiba|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Az adatok számos nullával|
root_mean_squared_error|Root mean squared hiba a cél- és az előrejelzési várt eltéréseinek négyzetgyökét|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|None|
normalized_root_mean_squared_error|Normalizált legfelső szintű mean squared hiba root mean squared hiba osztva az adatok tartományán:|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Az adatok számos nullával|
root_mean_squared_log_error|Legfelső szintű mean squared log hiba a várt squared logaritmikus hiba négyzetgyökét|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|None|
normalized_root_mean_squared_log_error|Root mean squared log normalizált hiba elosztja az adatokat számos root mean squared log hiba|[Számítás](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Az adatok számos nullával|
