---
title: Az Azure Machine Learning segítségével modell hiperparaméterek hangolása
description: Hatékonyan finomhangolja az Azure Machine Learning szolgáltatás használatával a mély tanulás / gépi tanulási modell hiperparaméterek. Megtudhatja, hogyan határozza meg a paraméter keresési terület, adjon meg egy elsődleges metrika optimalizálása és a korai a rosszul működő fut le.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 13820dd511d31217b79385e893edbb55a3a57693
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430019"
---
# <a name="tune-hyperparameters-for-your-model"></a>Hiperparaméterek a modell finomhangolása

Hatékonyan finomhangolja hiperparaméterek a modell Azure Machine Learning szolgáltatás használatával.  Hiperparaméter finomhangolása a következő lépésekből áll:

* Adja meg a paraméter keresési terület
* Adjon meg egy elsődleges metrika optimalizálása  
* Adja meg a korai lemondási feltételeit rosszul működő futtatások
* Erőforrások lefoglalása a hiperparaméter finomhangolása
* Indítsa el a fenti konfigurációval kísérlet
* A betanítási futtatások megjelenítése
* Válassza ki a legjobb teljesítményt nyújtó configuration a modell

## <a name="what-are-hyperparameters"></a>Mik azok a hiperparaméterek?

Hiperparaméterek úgy dönt, hogy a modell betanítását, maga a betanítási folyamat szabályozó állítható paraméterek. Ha például egy Neurális hálózat betanítása, úgy dönt, előtt a modell tanítása minden egyes rétegben lévő csomópontok számát és a hálózat rejtett réteget száma. Ezek az értékek általában marad állandó a betanítási folyamat során.

Mély tanulás / gépi tanulás forgatókönyveket, a modellek teljesítményének nagyban függ a kijelölt hiperparaméter értékek. A hiperparaméter feltárás célja, hogy kereshet különféle hiperparaméter konfigurációk megtalálja a megfelelő konfigurációt, amely a lehető legjobb teljesítményt eredményez. A hiperparaméter feltárási folyamata általában megszokott manuális, tekintve, hogy a keresési lemezterület mérete nagy, és értékelése az egyes konfigurációkhoz költséges lehet.

Az Azure Machine Learning lehetővé teszi, hogy automatizálja a hiperparaméter feltárása és hatékonyan, így jelentős mennyiségű időt és erőforrásokat. Adjon meg hiperparaméter értékek, és a futtatások képzési maximális száma. A rendszer ezután automatikusan elindítja a paraméterek különböző konfigurációjú több egyidejű futtatás, és megkeresi a konfigurációt, amely a legjobb teljesítmény érdekében a mérőszám úgy dönt, mérjük eredményez. Gyengén teljesítő betanítási futtatások automatikusan korai leállt, csökkenti a számítási erőforrások veszteség. Ezeket az erőforrásokat más hiperparaméter konfigurációk megismerése helyett használhatók.

>[!NOTE]
> Ebben a cikkben kód teszteltük az Azure Machine Learning SDK verziója 0.168 

## <a name="define-search-space"></a>Keresési hely megadása

Hiperparaméterek hangolni által definiált minden egyes hiperparaméter értékek feltárása.

### <a name="types-of-hyperparameters"></a>Hiperparaméterek típusai

Minden egyes hiperparaméter diszkrét és folytonos lehet.

#### <a name="discrete-hyperparameters"></a>Különálló hiperparaméterek 

Különálló hiperparaméterek vannak megadva a `choice` diszkrét értékei között. `choice` lehetnek:

* egy vagy több vesszővel tagolt értékek
* egy `range` objektum
* bármilyen tetszőleges `list` objektum


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

Ebben az esetben `batch_size` egy [16 32, 64, 128] értéket veszi fel, és `number_of_hidden_layers` egy [1, 2, 3, 4] értéket veszi fel.

Speciális diszkrét hiperparaméterek is eloszlás használatával adható meg. A következő disztribúciók támogatottak:

* `quniform(low, high, q)` – Visszaad egy értéket, például a ciklikus (egységes (alacsony, magas) / q) * q
* `qloguniform(low, high, q)` – Visszaad egy értéket, például a ciklikus (exp (egységes (alacsony, magas)) / q) * q
* `qnormal(mu, sigma, q)` – Visszaad egy értéket, például a ciklikus (normál (mu, szigma) / q) * q
* `qlognormal(mu, sigma, q)` – Visszaad egy értéket, például a ciklikus (exp (normál (mu, szigma)) / q) * q

#### <a name="continuous-hyperparameters"></a>Folyamatos hiperparaméterek 

Folyamatos hiperparaméterek értékek folyamatos számos terjesztési vannak megadva. Támogatott disztribúciók a következők:

* `uniform(low, high)` -Értékét adja vissza egy egyenletesen alacsony és magas között elosztott
* `loguniform(low, high)` -Adja vissza egy merőleges exp (egységes (alacsony, magas)) alapján, hogy egyenletesen oszlik el a visszaadott érték a logaritmusa
* `normal(mu, sigma)` -Értéket ad vissza egy valódi általában a mean mu és a szórást szigma elosztott
* `lognormal(mu, sigma)` -Adja vissza egy merőleges exp (normál (mu, szigma)) alapján, hogy a visszaadott érték logaritmusát általában terjesztése

A példában egy paraméterdefinícióhoz terület:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Ez a kód határozza meg a keresési tárhely két paraméter – `learning_rate` és `keep_probability`. `learning_rate` közepes értéke 10-es és a egy 3 szórását normál eloszláshoz rendelkezik. `keep_probability` 0,05 és a egy 0,1 maximális értéke egy egyenletes elosztása a minimális értékkel rendelkezik.

### <a name="sampling-the-hyperparameter-space"></a>Mintavétel a hiperparaméter terület

A paraméter-mintavételezési módszerét használja a hiperparaméter definícióhoz-en keresztül is megadhatja. Az Azure Machine Learning szolgáltatás a szúrópróbaszerű, rács mintavételi és Bayes mintavételi támogatja.

#### <a name="random-sampling"></a>Véletlenszerű mintavétel

A véletlenszerű mintavételezése esetén, a hiperparaméter értékek rendszer véletlenszerűen választja ki a megadott keresési területen. Véletlenszerű mintavételi lehetővé teszi, hogy a keresési terület is diszkrét és folytonos hiperparaméterek tartalmazza.

```Python
from azureml.train.hyperdrive import RandomParameterSampling
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Rács mintavétel

Rács mintavételi egy egyszerű rács keresés minden lehetséges értéket a megadott keresési terület végrehajtja. Csak használható hiperparaméterek megadni, használja a `choice`. Például az azt követő szóközt, összesen hat minták:

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Bayes-mintavétel

Bayes mintavételi a Bayes algoritmus alapján, és lehetővé teszi az intelligens döntések a következő példa a hiperparaméter értékekhez. Azt választja, a minta, hogyan az előző példák hajtja végre, például, hogy az új mintát javítja a jelzett elsődleges metrika alapján.

Bayes-mintavétel használatakor az egyidejű futtatásainak számát hatással van a beállítási folyamat hatékonyságát. Általában kisebb mennyiségű egyidejű Futtatás vezethet jobb mintavételi convergence, mivel a kisebb párhuzamossági fokot növeli, amelyek korábban befejezett futtatások futtatások száma.

Bayes mintavételi támogatja a csak `choice` és `uniform` disztribúciók a keresési tér át. 

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> Bayes mintavételi nem támogatja a korai lemondási házirendek (lásd: [adjon meg egy korai lemondási szabályzatot](#specify-early-termination-policy)). Bayes paraméter-mintavétel használatakor beállítása `early_termination_policy = None`, vagy hagyja a `early_termination_policy` paraméter.

<a name='specify-primary-metric-to-optimize'/>

## <a name="specify-primary-metric"></a>Adja meg az elsődleges metrika

Adja meg a elsődleges mérőszám azt szeretné, hogy a hiperparaméter finomhangolása kísérlet optimalizálása érdekében. Egyes betanítási futtatások abban az esetben a elsődleges metrika. Rosszul működő futtatja (amelyen az elsődleges metrika nem felel meg a korai lemondási házirendek által beállított feltételek) befejeződik. Az elsődleges metrika neve mellett is megadhatja a cél az optimalizálási - e az elsődleges metrika vagy teljes méretűre.

* `primary_metric_name`: Az elsődleges metrika optimalizálása a neve. A metrika a tanítási szkriptet által naplózott neve pontosan egyeznie kell az elsődleges metrika neve. Lásd: [metrikáinak hiperparaméter finomhangolása jelentkezzen](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal`: Ez lehet `PrimaryMetricGoal.MAXIMIZE` vagy `PrimaryMetricGoal.MINIMIZE` és határozza meg, hogy az elsődleges metrika teljes méretű vagy kis méretben fut az fut kiértékelése során. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Optimalizálhatja a futtatások "pontossága" maximalizálása érdekében.  Ellenőrizze, hogy ez az érték jelentkezzen be a tanítási szkriptet.

<a name='log-metrics-for-hyperparameter-tuning'/>

### <a name="log-metrics-for-hyperparameter-tuning"></a>Napló metrikáinak hiperparaméter finomhangolása

A modell a tanítási szkriptet kell jelentkeznie a releváns metrikákat modell betanítása közben. A hiperparaméter finomhangolása konfigurálásakor adja meg a elsődleges metrika futtatási teljesítmény kiértékelésekor használandó. (Lásd: [adjon meg egy elsődleges metrika optimalizálása](#specify-primary-metric-to-optimize).)  A tanítási szkriptet Ez a metrika kell jelentkeznie, így a hiperparaméter finomhangolása a folyamat a rendelkezésére áll.

Ez a metrika jelentkezzen be az alábbi minta kódrészlettel a tanítási szkriptet:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Kiszámítja a tanítási szkriptet a `val_accuracy` és naplózza, mint "pontossággal", mint az elsődleges metrika használt. A mérőszám a rendszer naplózza minden alkalommal, amikor megkapta a hiperparaméter finomhangolása a szolgáltatás. A modell fejlesztő határozza meg, hogy milyen gyakran kell jelentse, ez a metrika esetén.

<a name='specify-early-termination-policy'/>

## <a name="specify-early-termination-policy"></a>Korai lemondási házirend megadása

Állítsa le a rosszul működő automatikusan fut egy korai lemondási szabályzatot. Lemondási csökkenti az erőforrások veszteség, és helyette használja ezeket az erőforrásokat más paraméter konfigurációs átvizsgálását.

Egy korai lemondási szabályzat használatakor konfigurálhatja az alábbi paramétereket, amelyek vezérlik a házirend alkalmazásakor:

* `evaluation_interval`: a házirend alkalmazására vonatkozó gyakoriságát. Minden egyes a tanítási szkriptet az elsődleges metrika számít egy időközt. Így egy `evaluation_interval` 1 alkalmazza a szabályzatot minden alkalommal, amikor a tanítási szkriptet jelenti az elsődleges metrikát. Egy `evaluation_interval` 2 alkalmazza a szabályzatot minden más, a tanítási szkriptet jelenti az elsődleges metrika alkalommal. Ha nincs megadva, `evaluation_interval` alapértelmezés szerint 1-re van állítva.
* `delay_evaluation`: késlelteti az intervallumok megadott számú első szabályzat-kiértékelés. Egy nem kötelező paraméter, amely lehetővé teszi az összes konfiguráció futtatása egy kezdeti minimális számú időközök, elkerülve a képzés korai lemondási futtatja. Ha meg van adva, a szabályzat vonatkozik, amely nagyobb vagy egyenlő delay_evaluation evaluation_interval minden többszöröse.

Az Azure Machine Learning szolgáltatás a következő korai lemondási házirendeket támogatja.

### <a name="bandit-policy"></a>Bandit házirend

Bandit egy olyan lezárást szabályzat, a slack tényező/slack-mennyiség és értékelés céljából használják időköz alapján. A szabályzat korai bármely fut, ahol az elsődleges metrika nem esik a megadott slack tényező / garanciát a legjobb teljesítményt nyújtó képzés slack összeg futtatása leáll. Az alábbi konfigurációs paramétereket tart:

* `slack_factor` vagy `slack_amount`: a Slack-en garanciát a legjobb teljesítményt nyújtó képzés futtatása engedélyezett. `slack_factor` Adja meg a megengedett slack arány. `slack_amount` Adja meg a megengedett slack abszolút összeg, szélesség-magasság arányban helyett.

    Vegyük példaként egy Bandit házirend 10 időközönként alkalmazása folyamatban. Tegyük fel, hogy a legjobb végrehajtása 10 időközönként futtatása jelentett 0,8 elsődleges metrika az a célja, hogy az elsődleges metrika maximalizálása érdekében. Ha a házirend lett megadva a egy `slack_factor` képzést, fut, 0.2-es, amelynek ajánlott metrika időközönként 10-es nem éri el 0.66 (0,8 / (1 +`slack_factor`)) befejeződik. Ha ehelyett a házirendet az lett megadva egy `slack_amount` képzést, fut, 0.2-es, amelynek ajánlott metrika időközönként 10-es nem éri el a 0.6-os (0,8 - `slack_amount`) befejeződik.
* `evaluation_interval`: a házirend (nem kötelező paraméter) alkalmazása gyakoriságát.
* `delay_evaluation`: (nem kötelező paraméter) időközönként megadott számú első szabályzat-kiértékelés késlelteti.


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

Ebben a példában a korai lemondási házirend esetén is alkalmazva lesz minden időközönként metrikák szerepelnek a jelentésben, kiértékelési időköze 5 ellenében. Bármely futtassa, amelynek ajánlott a metrika-nál kisebb (1/(1+0.1) 91 %-a legjobban teljesítő futtatási lesz állítható le.

### <a name="median-stopping-policy"></a>Közepes leállításával házirend

Közepes leállításával egy korai lemondási házirend futó átlagok a futtatások által jelentett elsődleges mérőszámok alapján. Ez a szabályzat átlagokat kiszámítja az összes betanítási futtatás során, és amelyek teljesítményét az rosszabb, mint az átlagokat középértékének futtatások befejeződik. Ez a szabályzat az alábbi konfigurációs paramétereket fogadja:
* `evaluation_interval`: a házirend (nem kötelező paraméter) alkalmazása gyakoriságát.
* `delay_evaluation`: (nem kötelező paraméter) időközönként megadott számú első szabályzat-kiértékelés késlelteti.


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

Ebben a példában a korai lemondási házirendet alkalmazza minden időközönként kiértékelési időköze 5 díjtól. Futtatás 5 időközönként megszűnik, ha a legjobb elsődleges metrika rosszabb, mint az átlagokat középértékének keresztül időközök 1:5 összes betanítási futtatás során.

### <a name="truncation-selection-policy"></a>Csonkolási kijelölés házirend

Csonkolási kijelölés megszakítja az értékelési időközönként fut legalacsonyabb hajt végre egy adott százaléka. Futtatások összehasonlítja az elsődleges metrikát a teljesítményük alapján, és a legalacsonyabb X % megszűnik. Az alábbi konfigurációs paramétereket tart:

* `truncation_percentage`: a legalacsonyabb végrehajtása aránya fut, hogy minden egyes kiértékelési időköze fejeződik be. Adjon meg egy egész számot 1 és 99 között.
* `evaluation_interval`: a házirend (nem kötelező paraméter) alkalmazása gyakoriságát.
* `delay_evaluation`: (nem kötelező paraméter) időközönként megadott számú első szabályzat-kiértékelés késlelteti.


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

Ebben a példában a korai lemondási házirendet alkalmazza minden időközönként kiértékelési időköze 5 díjtól. Futtatás befejeződik időköz 5, ha 5 időközönként teljesítménye minden Futtatás teljesítményét időköz 5 legkisebb 20 %-át.

### <a name="no-termination-policy"></a>Nincs lemondási házirend

Ha azt szeretné, hogy minden a betanítási Futtatás befejezését, NoTerminationPolicy használja. Ez lesz a nem a bármely korai lemondási-házirend alkalmazásának.

```Python
from azureml.train.hyperdrive import NoTerminationPolicy
early_termination_policy = NoTerminationPolicy()
```

### <a name="default-policy"></a>Alapértelmezett szabályzat

Ha nincs szabályzat van megadva, a hiperparaméter finomhangolása szolgáltatás használja-e a középérték leállítása házirendet `evaluation_interval` 1 és `delay_evaluation` alapértelmezés szerint 5. Ezek a konzervatív beállításait, amely körülbelül 35 %-os 25 %-os megtakarítás adatvesztés nélkül képes biztosítani az elsődleges metrika (értékelési adatok alapján).

## <a name="allocate-resources"></a>Erőforrásokat

Szabályozhatja a hiperparaméter finomhangolása kísérlet megadásával a betanítási futtatások maximális száma erőforrás költségkeretét.  Nem kötelezően megadhatja a hiperparaméter finomhangolása kísérlet az engedélyezett maximális időtartam.

* `max_total_runs`: A betanítási Futtatás létrehozni maximális teljes száma. Felső határérték - lehet kevesebb fut le, például ha a hiperparaméter terület véges, és kevesebb mintákat. 1 és 1000 közötti számnak kell lennie.
* `max_duration_minutes`: A maximális időtartam percben kifejezve a hiperparaméter finomhangolása kísérlet. A paraméter nem kötelező, és ha van ilyen, az ezen időtartam után szeretné futó futtatásokat automatikusan törlődnek.

>[!NOTE] 
>Ha mindkét `max_total_runs` és `max_duration_minutes` meg van adva, a hiperparaméter finomhangolása kísérlet megszakítása, ha az első e két küszöbértékek elérésekor.

Emellett adja meg legfeljebb hány betanítási fut egyidejűleg a hiperparaméter finomhangolása keresési idejére.

* `max_concurrent_runs`: Futtatások maximális száma egy adott időpontban egyidejűleg ipari környezetekben történő futtatását. Ha nincs megadva, az összes `max_total_runs` párhuzamosan elindul. Ha meg van adva, 1 és 100 közötti számnak kell lennie.

>[!NOTE] 
>Egyidejű futtatásainak számát a megadott számítási célkiszolgálón elérhető erőforrásokon engedi át. Ezért kell, hogy a számítási célnak legyen-e a kívánt egyidejűségi számára elérhető erőforrások.

A hiperparaméter finomhangolása erőforrásokat:

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Ez a kód a hiperparaméter finomhangolása a kísérletben legfeljebb 4 konfigurációk fut egyszerre 20 teljes futtatások használandó konfigurálja.

## <a name="configure-experiment"></a>Kísérlet konfigurálása

Konfigurálja a hiperparaméter finomhangolása a meghatározott hiperparaméter keresési terület, a korai lemondási házirend, elsődleges metrika és erőforrás-elosztás alapján a fenti szakaszban kísérlet. Továbbá adja meg egy `estimator` , amely a mintavételezett hiperparaméterek hívja. A `estimator` ismerteti a tanítási szkriptet futtatja, az erőforrások száma (egyszeres vagy többszörös-gpu), a feladat és a számítási célnak használatára. Mivel egyidejűségi esetében a hiperparaméter finomhangolása kísérlet engedi át az erőforrások, győződjön meg arról, hogy a számítási célnak megadott a `estimator` elegendő erőforrással rendelkezik a kívánt egyidejűségi számára. (Estimators további információkért lásd: [modelleket taníthat be hogyan](how-to-train-ml-models.md).)

Adja meg a hiperparaméter finomhangolása kísérlet:

```Python
from azureml.train.hyperdrive import HyperDriveRunConfig
hyperdrive_run_config = HyperDriveRunConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="submit-experiment"></a>Kísérlet elküldése

Miután a hiperparaméter finomhangolása konfigurációs meghatározott, egy kísérlet elküldésére:

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

`experiment_name` a neve, hozzárendeli a hiperparaméter finomhangolása a kísérletet, és `workspace` a munkaterülete, amelyben meg szeretné a kísérlet létrehozása (kísérletek további információkért lásd: [Azure Machine Learning szolgáltatás működése?](concept-azure-machine-learning-architecture.md))

## <a name="visualize-experiment"></a>Kísérlet megjelenítése

Az Azure Machine Learning SDK-t biztosít, amely megjeleníti a tanítási előrehaladását Notebook widget futtatja. Az alábbi kódrészlet megjeleníti az összes a hiperparaméter finomhangolása egy Jupyter Notebookban egy helyen fut:

```Python
from azureml.train.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Ez a kód a hiperparaméter konfigurációk mindegyike jeleníti meg a betanítási futtatások részleteit tartalmazó tábla.

![hiperparaméter hangolási táblázat](media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

A teljesítmény a fut az egyes képzési előrehaladtával emellett jelenítheti meg. 

![hiperparaméter hangolási diagram](media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Ezenkívül vizuálisan azonosíthatja a teljesítmény és az egyes hiperparaméterek használatával egy párhuzamos koordinálja a nyomtatandó értékek korrelációját. 

![a hiperparaméter finomhangolása párhuzamos koordináták](media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)

Az összes a hiperparaméter finomhangolása fut, valamint az Azure webes portálján jelenítheti meg. A kísérlet megtekintése a webportálon további információkért lásd: [nyomon követését kísérletek](how-to-track-experiments.md#view-the-experiment-in-the-web-portal).

![hiperparaméter hangolási portál](media/how-to-tune-hyperparameters/HyperparameterTuningPortal.png)

## <a name="find-the-best-model"></a>A legjobb modellt keresése

Miután elvégezte a hiperparaméter finomhangolása futtatások összes, azonosítsa a legjobb teljesítményt nyújtó configuration és a hiperparaméter tartozó értékek:

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['Arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## <a name="sample-notebook"></a>Minta notebook
Tekintse meg 
* [Training/03.train-hyperparameter-Tune-Deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/blob/master/training/03.train-hyperparameter-tune-deploy-with-tensorflow) hiperparaméterek Tensorflow modell finomhangolásával oktatóanyagot. 

Ez a jegyzetfüzet lekérése:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések
* [Egy kísérlet nyomon követése](how-to-track-experiments.md)
* [A betanított modell üzembe helyezése](how-to-deploy-and-where.md)
