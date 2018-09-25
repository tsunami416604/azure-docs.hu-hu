---
title: Az Azure Machine Learning segítségével modell hiperparaméterek hangolása
description: Ismerje meg, az Azure Machine Learning szolgáltatás használatával a mély tanulás / gépi tanulási modellt az hiperparaméterek hangolását. Bemutatjuk, hogyan határozza meg a paraméter keresési terület, adjon meg egy elsődleges metrika optimalizálása és a korai a rosszul működő konfigurációk leáll.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 205a4d454be02d64058e3d0fcffda35df5b831a5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971673"
---
# <a name="tune-hyperparameters-for-your-model"></a>Hiperparaméterek a modell finomhangolása

Ebből a cikkből megtudhatja, hatékonyan a hiperparaméterek a modell finomhangolása. Bemutatjuk, hogyan határozza meg a paraméter keresési terület, adjon meg egy elsődleges metrika optimalizálása és a korai a rosszul működő konfigurációk leáll. Megjelenítheti a különböző a betanítási Futtatás és válassza ki a legjobb teljesítményt nyújtó configuration a modell is.

## <a name="what-are-hyperparameters"></a>Mik azok a hiperparaméterek?
Hiperparaméterek kiválasztása előtt a modell tanítása maga a betanítási folyamat szabályozó állítható paraméterek. Például egy Neurális hálózat betanítása, előtt szüksége lesz dönthet arról, hogy minden egyes rétegben lévő csomópontok számát és a hálózat rejtett réteget száma. Ezek az értékek általában marad állandó a betanítási folyamat során.

Mély tanulás / gépi tanulás forgatókönyveket, a modellek teljesítményének nagyban függ a kijelölt hiperparaméter értékek. A hiperparaméter feltárás célja, hogy kereshet különféle hiperparaméter konfigurációk megtalálja a megfelelő konfigurációt, amely a kívánt teljesítményt eredményez. A hiperparaméter feltárási folyamata általában megszokott manuális, tekintve, hogy a keresési lemezterület mérete nagy, és értékelése az egyes konfigurációkhoz költséges lehet.

Az Azure Machine Learning segítségével automatizálhatja a hiperparaméter feltárása és hatékonyan, így jelentős mennyiségű időt és erőforrásokat. Megadhatja a hiperparaméter értékek feltárása, és ez a feltárási futtatja képzési maximális száma. A rendszer ezután automatikusan elindítja paraméterek különböző konfigurációjú több egyidejű betanítási Futtatás és megkeresi a konfigurációt, amely szerint a felhasználó által kiválasztott metrika mért a lehető legjobb teljesítményt eredményez. Gyengén teljesítő betanítási futtatások automatikusan korai leállt, csökkenti a számítási erőforrások veszteség. Ezeket az erőforrásokat más hiperparaméter konfigurációk megismerése helyett használhatók.

A modell Azure Machine Learning szolgáltatás használatával hiperparaméterek hangolása, tegye a következőket - kell
* Adja meg a hiperparaméter keresési terület
* Adjon meg egy elsődleges metrika optimalizálása
* Adjon meg egy korai lemondási szabályzatot
* Erőforrások lefoglalása a hiperparaméter finomhangolása
* Indítsa el a fenti konfigurációval kísérlet

## <a name="define-the-hyperparameter-search-space"></a>Adja meg a hiperparaméter keresési terület
Az Azure Machine Learning szolgáltatás biztonság érdekében automatikusan hangolja hiperparaméterek által definiált minden egyes hiperparaméter értékek feltárása.

### <a name="types-of-hyperparameters"></a>Hiperparaméterek típusai
Minden egyes hiperparaméter diszkrét és folytonos lehet.

#### <a name="discrete-hyperparameters"></a>Különálló hiperparaméterek 
Különálló hiperparaméterek adhat meg egy `choice` diszkrét értékei között. `choice` is igénybe vehet egy vagy több vesszővel tagolt értékek, egy `range` objektumot, vagy bármely tetszőleges `list` objektum. Példa:  

```Python
    {    
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

Ebben az esetben batch_size [16 32, 64, 128] értékek egyikét hajthatja végre, és number_of_hidden_layers az [1, 2, 3, 4] érték egyikét hajthatja végre.

Speciális diszkrét hiperparaméterek is eloszlás használatával adható meg. A következő disztribúciók támogatottak-
* `quniform(low, high, q)` – Visszaad egy értéket, például a ciklikus (egységes (alacsony, magas) / q) * q
* `qloguniform(low, high, q)` – Visszaad egy értéket, például a ciklikus (exp (egységes (alacsony, magas)) / q) * q
* `qnormal(mu, sigma, q)` – Visszaad egy értéket, például a ciklikus (normál (mu, szigma) / q) * q
* `qlognormal(mu, sigma, q)` – Visszaad egy értéket, például a ciklikus (exp (normál (mu, szigma)) / q) * q

#### <a name="continuous-hyperparameters"></a>Folyamatos hiperparaméterek 
Folyamatos hiperparaméterek értékek folyamatos számos terjesztési adható meg. Támogatott disztribúciók tartalmazzák-
* `uniform(low, high)` -Értékét adja vissza egy egyenletesen alacsony és magas között elosztott
* `loguniform(low, high)` -Adja vissza egy merőleges exp (egységes (alacsony, magas)) alapján, hogy egyenletesen oszlik el a visszaadott érték a logaritmusa
* `normal(mu, sigma)` -Értéket ad vissza egy valódi általában a mean mu és a szórást szigma elosztott
* `lognormal(mu, sigma)` -Adja vissza egy merőleges exp (normál (mu, szigma)) alapján, hogy a visszaadott érték logaritmusát általában terjesztése

Íme egy példa egy hely paraméterdefiníció-

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Ebben a példában két paraméter – learning_rate és keep_probability a keresési tárhely határozza meg. learning_rate közepes értéke 10-es és a egy 3 szórását normál eloszláshoz fog rendelkezni. keep_probability 0,05 és a egy 0,1 maximális értéke egy egyenletes elosztása a minimális érték lesz.

### <a name="sampling-the-hyperparameter-space"></a>Mintavétel a hiperparaméter terület
A felhasználó is adja meg a paraméter-mintavételezési módszerét használja a megadott hiperparaméter definícióhoz-en keresztül. Az Azure Machine Learning szolgáltatás véletlenszerű mintavételi, rács mintavételi és Bayes mintavételi támogatja.

#### <a name="random-sampling"></a>Véletlenszerű mintavétel
A véletlenszerű mintavételezése esetén, a hiperparaméter értékek rendszer véletlenszerűen választja ki a megadott keresési területen. Véletlenszerű mintavételi lehetővé teszi, hogy a keresési terület is diszkrét és folytonos hiperparaméterek tartalmazza. Példa:

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
Rács mintavételi egy egyszerű rács keresés minden lehetséges értéket a megadott keresési terület végrehajtja. Csak használható hiperparaméterek megadni, használja a `choice`. Például a következő területen, a hat-minták – összesen

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Bayes-mintavétel
Bayes mintavételi a Bayes algoritmus alapján, és lehetővé teszi az intelligens döntések a következő példa a hiperparaméter értékekhez. Azt választja ki ezt a mintát, hogyan az előző példák hajtja végre, például, hogy az új mintát javítja a jelzett elsődleges metrika alapján.

Bayes-mintavétel használatakor az egyidejű futtatásainak számát hatással van a beállítási folyamat hatékonyságát. Általában egy kisebb mennyiségű egyidejű Futtatás jobb mintavételi convergence vezethet. Ennek oka az, a párhuzamosság kisebb mértékű növeli, amelyek korábban befejezett futtatások futtatások száma.

Bayes mintavételi támogatja a csak `choice` és `uniform` disztribúciók a keresési tér át. Példa: 

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> Bayes mintavételi jelenleg nem támogatja a korai lemondási házirendek (lásd: [adjon meg egy korai lemondási szabályzatot](#specify-an-early-termination-policy)). Ha Bayes paraméter mintavételt használ, először be kell állítania a házirend `None`. Nem adnak meg lezárást házirend Bayes mintavételi lesz ugyanezt a hatást.
>
> ```Python
> early_termination_policy = None
> ```

## <a name="specify-a-primary-metric-to-optimize"></a>Adjon meg egy elsődleges metrika optimalizálása
Hiperparaméterek beállítása, amikor szüksége adja meg az elsődleges metrikát a hiperparaméter finomhangolása kísérlet optimalizálása érdekében. Egyes betanítási futtatások kiértékelése történik az elsődleges metrika és rosszul működő futtatja (amelyen az elsődleges metrika nem felel meg a korai lemondási házirendek által beállított feltételek) befejeződik. Mellett az elsődleges metrika nevének megadását, is kell adja meg a cél az optimalizálási - e az elsődleges metrika vagy teljes méretűre.
* `primary_metric_name`: Az elsődleges metrika optimalizálása a neve. A metrika a tanítási szkriptet által naplózott neve pontosan egyeznie kell az elsődleges metrika neve. Lásd: [metrikáinak hiperparaméter finomhangolása jelentkezzen](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal`: Ez lehet `PrimaryMetricGoal.MAXIMIZE` vagy `PrimaryMetricGoal.MINIMIZE` és határozza meg, hogy az elsődleges metrika teljes méretű vagy kis méretben fut az fut kiértékelése során. 

Például:
```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```
Ez optimalizálja a futtatások "pontossága" maximalizálása érdekében.

### <a name="log-metrics-for-hyperparameter-tuning"></a>Napló metrikáinak hiperparaméter finomhangolása
Annak érdekében, hogy a hiperparaméter finomhangolása az Azure Machine Learning szolgáltatást használja, a modell a tanítási szkriptet kell jelentés releváns metrikákat, a modell végrehajtása közben. A felhasználó adja meg a elsődleges metrika szeretnének a szolgáltatás futtatási teljesítmény kiértékelésekor használandó, és a tanítási szkriptet be kell jelentkeznie, ez a metrika. Lásd: [adjon meg egy elsődleges metrika optimalizálása](#specify-a-primary-metric-to-optimize).

Ez a metrika bejelentkezni a tanítási szkriptet is frissítheti az alábbi kódrészlettel minta -

```Python
from azureml.core.run import Run
run_logger = Run.get_submitted_run()
run_logger.log("accuracy", float(val_accuracy))
```

Ebben a példában a tanítási szkriptet kiszámítja a `val_accuracy` és a "pontossággal", mint az elsődleges metrika használt naplózza. A modell fejlesztő határozza meg, hogy milyen gyakran kell jelentse, ez a metrika esetén.

## <a name="specify-an-early-termination-policy"></a>Adjon meg egy korai lemondási szabályzatot
Ha az Azure Machine Learning szolgáltatás használatával hiperparaméterek, rosszul működő futtatások finomhangolása automatikusan korai megszűnik. Ez csökkenti az erőforrások veszteség, és helyette használja ezeket az erőforrásokat más paraméter konfigurációs átvizsgálását.

Egy korai lemondási házirend használata esetén a felhasználó konfigurálhat-e az alábbi paramétereket, amelyek vezérlik a házirend alkalmazásakor-
* `evaluation_interval`: a házirend alkalmazására vonatkozó gyakoriságát. Minden egyes a tanítási szkriptet az elsődleges metrika számít egy időközt. Így egy `evaluation_interval` 1 alkalmazza a szabályzatot minden alkalommal, amikor a tanítási szkriptet jelenti az elsődleges metrikát. Egy `evaluation_interval` 2 alkalmazza a szabályzatot minden más, a tanítási szkriptet jelenti az elsődleges metrika alkalommal. Ez egy nem kötelező paraméter, és ha nincs megadva, `evaluation_interval` alapértelmezés szerint 1-re van állítva.
* `delay_evaluation`: késlelteti az intervallumok megadott számú első szabályzat-kiértékelés. Ez egy nem kötelező paraméter, amely lehetővé teszi az összes konfiguráció időközök, elkerülve a betanítási futtatások idő előtti befejezése kezdeti minimális számú futtatásához. Ha meg van adva, a szabályzat vonatkozik, amely nagyobb vagy egyenlő delay_evaluation evaluation_interval minden többszöröse.

Az Azure Machine Learning szolgáltatás támogatja a következő korai lemondási házirendek –

### <a name="bandit-policy"></a>Bandit házirend
Bandit csoportházirend egy olyan megszüntetése szabályzat, a slack tényező/slack-mennyiség és értékelés céljából használják időköz alapján. Ez a szabályzat korai bármely fut, ahol az elsődleges metrika nem esik a megadott slack tényező / garanciát a legjobb teljesítményt nyújtó képzés slack összeg futtatása ér véget. Az alábbi konfigurációs paramétereket - vesz igénybe
* `slack_factor` vagy `slack_amount`: a Slack-en garanciát a legjobb teljesítményt nyújtó képzés futtatása engedélyezett. `slack_factor` Adja meg a megengedett slack arány. `slack_amount` Adja meg a megengedett slack abszolút összeg, szélesség-magasság arányban helyett.

    Vegyük példaként egy Bandit házirend 10 időközönként alkalmazása folyamatban. Tegyük fel, hogy a legjobb végrehajtása 10 időközönként futtatása jelentett 0,8 elsődleges metrika az a célja, hogy az elsődleges metrika maximalizálása érdekében. Ha a házirend lett megadva a egy `slack_factor` képzést, fut, 0.2-es, amelynek ajánlott metrika időközönként 10-es nem éri el 0.66 (0,8 / (1 +`slack_factor`)) befejeződik. Ha ehelyett a házirendet az lett megadva egy `slack_amount` képzést, fut, 0.2-es, amelynek ajánlott metrika időközönként 10-es nem éri el a 0.6-os (0,8 - `slack_amount`) befejeződik.
* `evaluation_interval`: a házirend (nem kötelező paraméter) alkalmazása gyakoriságát.
* `delay_evaluation`: (nem kötelező paraméter) időközönként megadott számú első szabályzat-kiértékelés késlelteti.

Fontolja meg az ebben a példában-

```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

Ebben a példában a korai lemondási házirend esetén is alkalmazva lesz minden időközönként metrikák szerepelnek a jelentésben, kiértékelési időköze 5 ellenében. Bármely futtassa, amelynek ajánlott a metrika-nál kisebb (1/(1+0.1) 91 %-a legjobban teljesítő futtatási lesz állítható le.

### <a name="median-stopping-policy"></a>Középérték házirend leállítása
Középérték leállítása házirend egy korai lemondási házirend futó átlagok a futtatások által jelentett elsődleges mérőszámok alapján. Ez a szabályzat átlagokat kiszámítja az összes betanítási futtatás során, és amelyek teljesítményét az rosszabb, mint az átlagokat középértékének futtatások befejeződik. Ez a szabályzat az alábbi konfigurációs paramétereket - vesz igénybe.
* `evaluation_interval`: a házirend (nem kötelező paraméter) alkalmazása gyakoriságát.
* `delay_evaluation`: (nem kötelező paraméter) időközönként megadott számú első szabályzat-kiértékelés késlelteti.

Fontolja meg az ebben a példában-

```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

Ebben a példában a korai lemondási házirendet alkalmazza minden időközönként kiértékelési időköze 5 díjtól. Futtatás 5 időközönként megszűnik, ha a legjobb elsődleges metrika rosszabb, mint az átlagokat középértékének keresztül időközök 1:5 összes betanítási futtatás során.

### <a name="truncation-selection-policy"></a>Csonkolási kijelölés házirend
Csonkolása házirendjének megszakítja az értékelés időközönként fut legalacsonyabb hajt végre egy adott százaléka. Futtatások összehasonlítja az elsődleges metrikát a teljesítményük alapján, és a legalacsonyabb X % megszűnik. Az alábbi konfigurációs paramétereket - vesz igénybe
* `truncation_percentage`: a legalacsonyabb végrehajtása aránya fut, hogy minden egyes kiértékelési időköze fejeződik be. Ez egy egész számot 1 és 99 között kell lennie.
* `evaluation_interval`: a házirend (nem kötelező paraméter) alkalmazása gyakoriságát.
* `delay_evaluation`: (nem kötelező paraméter) időközönként megadott számú első szabályzat-kiértékelés késlelteti.

Fontolja meg az ebben a példában-

```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

Ebben a példában a korai lemondási házirendet alkalmazza minden időközönként kiértékelési időköze 5 díjtól. Futtatás befejeződik időköz 5, ha 5 időközönként teljesítménye minden Futtatás teljesítményét időköz 5 legkisebb 20 %-át.

### <a name="no-termination-policy"></a>Nincs lemondási házirend
Ha azt szeretné, hogy minden a betanítási Futtatás befejezését, NoTerminationPolicy használja. Ez lesz a nem a bármely korai lemondási-házirend alkalmazásának. Példa: 

```Python
from azureml.train.hyperdrive import NoTerminationPolicy
early_termination_policy = NoTerminationPolicy()
```

### <a name="default-policy"></a>Alapértelmezett szabályzat
Ha nincs szabályzat van megadva, a hiperparaméter finomhangolása szolgáltatás használja-e a középérték leállítása házirendet `evaluation_interval` 1 és `delay_evaluation` alapértelmezés szerint 5. Ezek a konzervatív beállításait, amely körülbelül 35 %-os 25 %-os megtakarítás adatvesztés nélkül képes biztosítani az elsődleges metrika (értékelési adatok alapján).

## <a name="allocate-resources-for-hyperparameter-tuning"></a>Erőforrások lefoglalása a hiperparaméter finomhangolása
A hiperparaméter finomhangolása kísérlet a betanítási Futtatás és szükség esetén a hiperparaméter finomhangolása kísérlet (percben) maximális időtartamának maximális száma megadásával az erőforrás-költségkeretét szabályozhatja. 
* `max_total_runs`: A betanítási Futtatás létrehozni maximális teljes száma. Ez a felső határt – előfordulhat, hogy rendelkezünk kevesebb fut le, például ha a hiperparaméter terület véges, és kevesebb mintákat. 1 és 1000 közötti számnak kell lennie.
* `max_duration_minutes`: A hiperparaméter finomhangolása kísérlet percek maximális időtartama. Ez egy nem kötelező paraméter, és ha van ilyen, az ezen időtartam után esetlegesen futó futtatásokat automatikusan törlődnek.

>[!NOTE] 
>Ha mindkét `max_total_runs` és `max_duration_minutes` meg van adva, az első e két küszöbértékek elérésekor a hiperparaméter finomhangolása kísérlet megszakadt.

Ezenkívül azt is megadhatja, legfeljebb hány betanítási fut egyidejűleg a hiperparaméter finomhangolása keresés során futtatandó.
* `max_concurrent_runs`: Ez a futtatások maximális száma egy adott időpontban egyidejűleg ipari környezetekben történő futtatását. Ha nincs megadva, az összes `max_total_runs` párhuzamosan elindul. Ha meg van adva, 1 és 100 közötti számnak kell lennie.

>[!NOTE] 
>Egyidejű futtatásainak számát a megadott számítási célkiszolgálón elérhető erőforrásokon engedi át. Ezért kell, hogy a számítási célnak legyen-e a kívánt egyidejűségi számára elérhető erőforrások.

Erőforrások tud lefoglalni az hiperparaméter finomhangolása, ahogyan az ebben a példában-
```Python
max_total_runs=20,
max_concurrent_runs=4
```
Ez konfigurálja a hiperparaméter finomhangolása a kísérletben legfeljebb 4 konfigurációk fut egyszerre 20 teljes futtatások használandó.

## <a name="configure-your-hyperparameter-tuning-experiment"></a>Konfigurálja a hiperparaméter finomhangolása kísérlet
Konfigurálhatja a hiperparaméter finomhangolása a meghatározott hyperpameter keresési terület, a korai lemondási házirend, az elsődleges metrikákkal és az erőforrás-elosztás alapján a fenti szakaszban kísérlet. Emellett meg kell adnia egy `estimator` , amely a mintavételezett hiperparaméterek hívja. A `estimator` ismerteti a tanítási szkriptet futtatja, az erőforrások száma (egyszeres vagy többszörös-gpu), a feladat és a számítási célnak használatára. Egyidejűségi óta esetében a hiperparaméter finomhangolása kísérlet a rendelkezésre álló erőforrásokon engedi át kell győződjön meg arról, hogy a számítási célnak megadott a `estimator` elegendő erőforrással rendelkezik a kívánt egyidejűségi. (Lásd: [hivatkozás](/how-to-train-ml-models.md) estimators bővebben).

Íme egy példa, hogy hogyan konfigurálhat a hiperparaméter hangolási kísérlet –

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

## <a name="submit-your-hyperparameter-tuning-experiment"></a>A hiperparaméter finomhangolása a kísérlet elküldése
Miután meghatározta a hiperparaméter finomhangolása konfigurációs, egy kísérlet elküldésére is ezzel a konfigurációval -

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

ahol `experiment_name` a hiperparaméter finomhangolása a kísérlethez hozzárendelni kívánt név és `workspace` , amelyben meg szeretné a kísérlet létrehozása a munkaterület (lásd: [hivatkozás](/concept-azure-machine-learning-architecture.md) kísérletek bővebben).

## <a name="visualize-your-hyperparameter-tuning-experiment"></a>A hiperparaméter finomhangolása kísérlet megjelenítése
Az Azure Machine Learning SDK biztosít egy jegyzetfüzetet widget, amelyek segítségével megjelenítheti a betanítási futtatások állapotát. A következő kódrészlet is használható minden a hiperparaméter hangolási Futtatás egy helyen - megjelenítése

```Python
from azureml.train.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Ez megjeleníti a betanítási futtatások részleteit tartalmazó tábla a hiperparaméter konfigurációk mindegyike esetében. Példa:

![hiperparaméter hangolási táblázat](media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

A teljesítmény a fut az egyes képzési előrehaladtával emellett jelenítheti meg. Példa:

![hiperparaméter hangolási diagram](media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Végül vizuálisan azonosíthatja a teljesítmény és az egyes hiperparaméterek használatával egy párhuzamos koordinálja a nyomtatandó értékek korrelációját. Példa: 

![a hiperparaméter finomhangolása párhuzamos koordináták](media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)

Másik lehetőségként jelenítheti meg az összes a hiperparaméter finomhangolása fut, valamint az Azure webes portálján. Lásd: [hivatkozás](/how-to-track-experiments.md/#view-the-experiment-in-the-web-portal) kísérlet megtekintése a webportálon további tájékoztatást. Ha például-

![hiperparaméter hangolási portál](media/how-to-tune-hyperparameters/HyperparameterTuningPortal.png)

## <a name="find-the-configuration-that-resulted-in-the-best-performance"></a>Keresse meg a konfigurációt a lehető legjobb teljesítményt eredményező
Miután elvégezte a hiperparaméter finomhangolása futtatások összes, azonosíthatja a legjobb teljesítményt nyújtó configuration és a megfelelő hiperparaméter értékeket az alábbi kódrészlet -

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
* `training/03.train-hyperparameter-tune-deploy-with-tensorflow/03.train-hyperparameter-tune-deploy-with-tensorflow.ipynb` hiperparaméterek Tensorflow modell finomhangolásával oktatóanyagot. 

Ez a jegyzetfüzet lekérése:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések
* [Egy kísérlet nyomon követése](/how-to-track-experiments.md)
* [A betanított modell üzembe helyezése](/how-to-deploy-and-where.md)
