---
title: A modell hiperparamétereinek finomhangolása
titleSuffix: Azure Machine Learning
description: Az Azure Machine Learning használatával hatékonyan hangolhatja be a deep learning/ machine learning modell hiperparamétereit. Megtudhatja, hogyan határozhatja meg a paraméter keresési terület, adja meg az elsődleges metrika optimalizálásához, és a korai leállítása rosszul teljesítő fut.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 4ca9cac00b9d00dcdbbd27f2fe769de2983c13ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75536641"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>Az Azure Machine Learning segítségével finomhangolhatja a modell hiperparamétereit
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Az Azure Machine Learning használatával hatékonyan hangolhatja be a modell hiperparamétereit.  A hiperparaméter-hangolás a következő lépéseket tartalmazza:

* A paraméterkeresési terület meghatározása
* Az optimalizáláshoz szolgáló elsődleges mérőszám megadása  
* A rosszul teljesítő futtatások korai megszüntetésének feltételeinek megadása
* Erőforrások lefoglalása a hiperparaméter-hangoláshoz
* Kísérlet indítása a fenti konfigurációval
* Az edzésfuttatások megjelenítése
* Válassza ki a modell legjobban teljesítő konfigurációját

## <a name="what-are-hyperparameters"></a>Mik azok a hiperparaméterek?

A hiperparaméterek olyan állítható paraméterek, amelyeket a betanítási folyamatot szabályozó modell betanításához választ. Például egy mély neurális hálózat betanításához a modell betanítása előtt dönti el a hálózat rejtett rétegeinek számát és az egyes rétegek csomópontjainak számát. Ezek az értékek általában állandóak maradnak a betanítási folyamat során.

A deep learning / machine learning forgatókönyvek, modell teljesítménye nagymértékben függ a kiválasztott hiperparaméter-értékek. A hiperparaméter feltárása célja, hogy keressen a különböző hiperparaméter-konfigurációk között, hogy megtalálja a konfigurációt, amely a legjobb teljesítményt eredményezi. A hiperparaméter-feltárási folyamat általában gondosan manuális, mivel a keresési terület hatalmas, és az egyes konfigurációk kiértékelése költséges lehet.

Az Azure Machine Learning lehetővé teszi a hyperparameter exploration hatékony automatizálását, jelentős időt és erőforrásokat takarítva meg. Megadhatja a hiperparaméter-értékek tartományát és a betanítási futtatások maximális számát. A rendszer ezután automatikusan elindítja a különböző paraméterkonfigurációkkal rendelkező egyidejű futtatásokat, és megkeresi a legjobb teljesítményt eredményező konfigurációt, amelyet a kiválasztott metrika mér. A rosszul teljesítő betanítási futtatások automatikusan leállnak, csökkentve a számítási erőforrások pazarlását. Ezek az erőforrások ehelyett más hiperparaméter-konfigurációk feltárására szolgálnak.


## <a name="define-search-space"></a>Keresési tér definiálása

A hiperparaméterek automatikus finomhangolása az egyes hiperparaméterekhez definiált értéktartomány feltárásával.

### <a name="types-of-hyperparameters"></a>A hiperparaméterek típusai

Minden hiperparaméter lehet diszkrét vagy folytonos, és egy [paraméterkifejezés](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?view=azure-ml-py)által leírt értékek eloszlásával rendelkezik.

#### <a name="discrete-hyperparameters"></a>Különálló hiperparaméterek 

A különálló hiperparaméterek különálló értékek `choice` között vannak megadva. `choice`lehet:

* egy vagy több vesszővel tagolt érték
* objektum `range`
* tetszőleges `list` objektum


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

Ebben az `batch_size` esetben átveszi az egyik értéket [16, 32, 64, 128], és `number_of_hidden_layers` felveszi az egyik értéket [1, 2, 3, 4].

A speciális különálló hiperparaméterek eloszlással is megadhatók. A következő disztribúciók támogatottak:

* `quniform(low, high, q)`- Kerek(egyenruha(alacsony, magas) / q) * q értéket ad eredményül.
* `qloguniform(low, high, q)`- Kerek(exp(uniform(alacsony, magas)) / q) * q értéket ad eredményül.
* `qnormal(mu, sigma, q)`- Kerek(normál(mu, szigma) / q) * q értéket ad eredményül.
* `qlognormal(mu, sigma, q)`- Kerek(exp(normal(mu, sigma)) / q) * q értéket ad eredményül.

#### <a name="continuous-hyperparameters"></a>Folyamatos hiperparaméterek 

A folyamatos hiperparaméterek folytonos értéktartományon belüli eloszlásként vannak megadva. A támogatott disztribúciók a következők:

* `uniform(low, high)`- Az alacsony és a magas között egyenletesen elosztott értéket számítja ki.
* `loguniform(low, high)`- Exp(uniform(low, high)) szerint megrajzolt értéket ad eredményül, hogy a visszatérési érték logaritmusa egyenletesen oszlik el.
* `normal(mu, sigma)`- Valós értéket ad eredményül, amely általában átlagos mu és szórásszigigmaszigmával van elosztva.
* `lognormal(mu, sigma)`- Exp(normal(mu, sigma)) szerint megrajzolt értéket ad eredményül, hogy a visszatérési érték logaritmusa általában ellegyen osztva.

Példa paramétertér-definícióra:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Ez a kód két paraméterrel rendelkező `learning_rate` `keep_probability`keresési területet határoz meg - és . `learning_rate`szokásos eloszlása 10 középértékkel és 3 szórással rendelkezik. `keep_probability`egyenletes eloszlása 0,05, maximális értéke 0,1.

### <a name="sampling-the-hyperparameter-space"></a>A hiperparaméter-tér mintavételezése

Megadhatja a hiperparaméter-térdefiníción keresztül használandó paramétermintavételi módszert is. Az Azure Machine Learning támogatja a véletlenszerű mintavételezést, a hálózati mintavételezést és a Bayes mintavételezést.

#### <a name="picking-a-sampling-method"></a>Mintavételi módszer kiválasztása

* A rácsmintavételezés akkor használható, ha a hiperparaméter-terület különálló értékek közötti választásként definiálható, és ha elegendő költségvetéssel rendelkezik ahhoz, hogy a megadott keresési térben minden értékteljes körű kereséséhez elegendő legyen. Ezenkívül a rosszul teljesítő futtatások automatikus korai megszüntetését is használhatja, ami csökkenti az erőforrások pazarlását.
* A véletlenszerű mintavételezés lehetővé teszi, hogy a hiperparaméter tér ben mind a diszkrét és a folyamatos hiperparaméterek. A gyakorlatban ez jó eredményeket a legtöbb alkalommal, és azt is lehetővé teszi az automatikus korai megszüntetését rosszul teljesítő fut. Egyes felhasználók véletlenszerű mintavétellel végeznek kezdeti keresést, majd iteratív módon finomítják a keresési területet az eredmények javítása érdekében.
* A bayes-mintavételi mintavétel a korábbi minták tudását használja ki a hiperparaméter-értékek kiválasztásakor, hatékonyan törekedve a jelentett elsődleges metrika javítására. Bayes-mintavételajánlott akkor ajánlott, ha elegendő költségvetéssel rendelkezik a hiperparaméter-terület feltárásához - a bayes-mintavételezés legjobb eredménye érdekében javasoljuk, hogy a behangolt hiperparaméterek számának 20-szorosát nagyobb vagy azzal egyenlő maximális számú futtatást használjon. Vegye figyelembe, hogy a Bayes-mintavételjelenleg nem támogatja a korai megszüntetési szabályzatot.

#### <a name="random-sampling"></a>Véletlenszerű mintavétel

Véletlenszerű mintavételezés esetén a hiperparaméter-értékek véletlenszerűen kerülnek kiválasztásra a megadott keresési területről. [A véletlenszerű mintavételezés](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?view=azure-ml-py) lehetővé teszi, hogy a keresési terület különálló és folyamatos hiperparamétereket is tartalmazzon.

```Python
from azureml.train.hyperdrive import RandomParameterSampling
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Hálózati mintavételezés

[A rácsmintavétel](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?view=azure-ml-py) egyszerű rácsos keresést hajt végre a megadott keresési térben az összes lehetséges értéken. Csak a használatával `choice`megadott hiperparaméterekkel használható. A következő terület például összesen hat mintát tartalmaz:

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Bayes-féle mintavételezés

[A bayes-mintavétela bayes-i](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?view=azure-ml-py) optimalizálási algoritmuson alapul, és intelligens döntéseket hoz a következő mintavételhez szükséges hiperparaméter-értékeken. A minta az előző minták által végzett, úgy, hogy az új minta javítja a jelentett elsődleges metrika alapján.

Bayes-mintavételhasználatakor az egyidejű futtatások száma hatással van a hangolási folyamat hatékonyságára. Általában kisebb számú párhuzamos futtatások vezethet jobb mintavételi konvergencia, mivel a kisebb fokú párhuzamosság növeli a futtatások száma, amelyek részesülnek a korábban befejezett fut.

Bayes mintavétel csak támogatja `choice` `uniform`, `quniform` és a disztribúciók a keresési területen.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> A bayes-i mintavételezés nem támogatja a korai megszüntetési házirendet [(lásd: Korai felmondási szabályzat megadása](#specify-early-termination-policy)). Bayes paramétermintavétel használatakor állítsa `early_termination_policy = None`be a `early_termination_policy` paramétert, vagy hagyja ki.

<a name='specify-primary-metric-to-optimize'/>

## <a name="specify-primary-metric"></a>Elsődleges metrika megadása

Adja meg azt az [elsődleges metrikát,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?view=azure-ml-py) amelyet a hiperparaméter-hangolási kísérletoptimalizálni szeretne. Minden egyes betanítási futtatás kiértékelése az elsődleges metrika. A gyengén teljesítő futtatások (ahol az elsődleges metrika nem felel meg a korai megszüntetési szabályzatáltal meghatározott feltételeknek) leállnak. Az elsődleges metrika neve mellett megadhatja az optimalizálás célját is – hogy maximalizálja vagy minimalizálja az elsődleges metrikát.

* `primary_metric_name`: Az optimalizálandó elsődleges metrika neve. Az elsődleges metrika nevének pontosan meg kell egyeznie a betanítási parancsfájl által naplózott metrika nevével. Lásd: [Metrikák naplózása a hiperparaméter-hangoláshoz.](#log-metrics-for-hyperparameter-tuning)
* `primary_metric_goal`: Lehet vagy `PrimaryMetricGoal.MAXIMIZE` `PrimaryMetricGoal.MINIMIZE` vagy, és meghatározza, hogy az elsődleges metrika lesz maximalizálva vagy minimalizálva a futtatások kiértékelésekor. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Optimalizálja a fut, hogy maximalizálja a "pontosság".  Győződjön meg róla, hogy ezt az értéket a betanítási parancsfájlban.

<a name='log-metrics-for-hyperparameter-tuning'/>

### <a name="log-metrics-for-hyperparameter-tuning"></a>A hiperparaméter-hangolás mérőszámai

A modell betanítási parancsfájljának naplóznia kell a megfelelő metrikákat a modell betanítása során. A hiperparaméter-hangolás konfigurálásakor megadhatja az elsődleges metrikát a futtatási teljesítmény kiértékeléséhez. (Lásd: [Elsődleges mérőszám megadása az optimalizáláshoz.)](#specify-primary-metric-to-optimize)  A betanítási parancsfájlban naplóznia kell ezt a metrikát, hogy elérhető legyen a hiperparaméter-hangolási folyamat számára.

Ezt a mérőszámot a betanítási parancsfájlban a következő mintakódrészlettel naplózza:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

A betanítási `val_accuracy` parancsfájl kiszámítja a és naplózza azt a "pontosság", amely elsődleges metrikaként használatos. Minden alkalommal, amikor a metrika naplózza azt a hyperparameter tuning szolgáltatás fogadása. A modellfejlesztő feladata annak meghatározása, hogy milyen gyakran jelentse ezt a metrikát.

<a name='specify-early-termination-policy'/>

## <a name="specify-early-termination-policy"></a>Korai megszüntetési házirend megadása

A gyengén teljesítő konklissza automatikusan, korai megszüntetési szabályzattal végződik. A megszüntetés csökkenti az erőforrások pazarlását, és ehelyett ezeket az erőforrásokat más paraméterkonfigurációk feltárására használja.

Korai befejezési házirend használataesetén a következő paramétereket konfigurálhatja, amelyek a házirend alkalmazásakor szabályozhatók:

* `evaluation_interval`: a szabályzat alkalmazásának gyakorisága. Minden alkalommal, amikor a betanítási parancsfájl naplózza az elsődleges metrika számít egy intervallum. Így `evaluation_interval` egy 1-es alkalmazásával a szabályzat minden alkalommal, amikor a betanítási parancsfájl jelenti az elsődleges metrika. Egy `evaluation_interval` 2-es a szabályzat ot minden második alkalommal alkalmazza, amikor a betanítási parancsfájl jelenti az elsődleges metrikát. Ha nincs megadva, `evaluation_interval` alapértelmezés szerint 1-re van állítva.
* `delay_evaluation`: elhalasztja az első házirend-kiértékelést egy meghatározott számú intervallumra. Ez egy választható paraméter, amely lehetővé teszi, hogy minden konfiguráció fut egy kezdeti minimális időközönként, elkerülve a képzési futtatások idő előtti befejezése. Ha meg van adva, a házirend a evaluation_interval minden olyan többszörösét alkalmazza, amely delay_evaluation nagyobb vagy egyenlő.

Az Azure Machine Learning a következő korai megszüntetési szabályzatokat támogatja.

### <a name="bandit-policy"></a>Bandita házirend

[A bandit](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?view=azure-ml-py#definition) a tartalékidő/tartalékidő és a kiértékelési időköz alapján leálltatási házirend. A szabályzat korai leállítja a futtatások, ahol az elsődleges metrika nem a megadott tartalékidő tényező / tartalékidő összeg a legjobban teljesítő betanítási futtatás tekintetében. A következő konfigurációs paramétereket veszi igénybe:

* `slack_factor`vagy `slack_amount`: a legjobban teljesítő edzésfutáshoz megengedett lazaság. `slack_factor`a megengedett tartalékidőt adja meg arányként. `slack_amount`a megengedett tartalékidőt abszolút összegként adja meg az arány helyett.

    Vegyük például a 10-es intervallumban alkalmazott Bandit házirendet. Tegyük fel, hogy a legjobban teljesítő futás a 10-es intervallumban egy 0,8-as elsődleges metrikát jelentett azzal a céllal, hogy maximalizálja az elsődleges metrikát. Ha a házirend `slack_factor` 0.2-es értékkel lett megadva, minden betanítási folyamat, amelynek a 10-es`slack_factor`intervallumban a legjobb metrikája kevesebb, mint 0,66 (0,8/(1+ )) megszűnik. Ha ehelyett a házirend 0.2-es értékkel `slack_amount` lett megadva, minden betanítási futtatás, amelynek a `slack_amount`10-es intervallumban a legjobb metrikája kevesebb, mint 0,6 (0,8 - ) megszűnik.
* `evaluation_interval`: a szabályzat alkalmazásának gyakorisága (választható paraméter).
* `delay_evaluation`: késlelteti az első házirend-kiértékelést egy megadott számú intervallumra (nem kötelező paraméter).


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

Ebben a példában a korai megszüntetési szabályzat minden időközönként, amikor metrikák at jelentenek, 5 kiértékelési időköztől kezdődően. Minden olyan futtatás, amelynek legjobb metrikája kisebb, mint (1/(1+0.1) vagy a legjobban teljesítő sorozat 91%-a, leáll.

### <a name="median-stopping-policy"></a>Medián leállítási politika

[A medián leállítás](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?view=azure-ml-py) egy korai befejezési szabályzat, amely a futtatások által jelentett elsődleges metrikák futási átlagain alapul. Ez a szabályzat kiszámítja az összes betanítási futtatások és megszakítások fut, amelyek teljesítménye rosszabb, mint a futási átlagok mediánja. Ez a házirend a következő konfigurációs paramétereket veszi figyelembe:
* `evaluation_interval`: a szabályzat alkalmazásának gyakorisága (választható paraméter).
* `delay_evaluation`: késlelteti az első házirend-kiértékelést egy megadott számú intervallumra (nem kötelező paraméter).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

Ebben a példában a korai megszüntetési szabályzat minden időközönként, 5 kiértékelési időköztől kezdődően kerül alkalmazásra. A futtatás 5-ös intervallumban megszakad, ha a legjobb elsődleges metrikája rosszabb, mint a futási átlagok mediánja az összes betanítási futtatássorán 1:5 időközönként.

### <a name="truncation-selection-policy"></a>Csonkolási kijelölési házirend

[A csonkolás kiválasztása](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?view=azure-ml-py) megszakítja a legalacsonyabb teljesítményt végző futtatások megadott százalékát az egyes kiértékelési időközökben. A futtatások összehasonlítása az elsődleges metrika teljesítményén alapul, és a legalacsonyabb X% leáll. A következő konfigurációs paramétereket veszi igénybe:

* `truncation_percentage`: az egyes kiértékelési időközökben leállítandó legalacsonyabb teljesítményű futtatások százalékos aránya. Adjon meg egy 1 és 99 közötti egész értéket.
* `evaluation_interval`: a szabályzat alkalmazásának gyakorisága (választható paraméter).
* `delay_evaluation`: késlelteti az első házirend-kiértékelést egy megadott számú intervallumra (nem kötelező paraméter).


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

Ebben a példában a korai megszüntetési szabályzat minden időközönként, 5 kiértékelési időköztől kezdődően kerül alkalmazásra. A futtatás az 5-ös intervallumban fejeződik be, ha az 5-ös intervallumban a teljesítménye az 5-ös intervallumban az összes futtatás teljesítményének legalacsonyabb 20%-ában van.

### <a name="no-termination-policy"></a>Nincs felmondási szabályzat

Ha azt szeretné, hogy az összes betanítási futtatás a befejezésig fusson, állítsa a házirendet Nincs. Ennek az a hatása, hogy nem alkalmaz nak korai megszüntetési politikát.

```Python
policy=None
```

### <a name="default-policy"></a>Alapértelmezett házirend

Ha nincs megadva házirend, a hiperparaméter-hangolási szolgáltatás lehetővé teszi, hogy az összes betanítási futtatások végrehajtása befejezéséig.

### <a name="picking-an-early-termination-policy"></a>Korai megszüntetési szabályzat kiválasztása

* Ha olyan konzervatív politikát keres, amely megtakarítást biztosít az ígéretes munkahelyek megszüntetésével, `evaluation_interval` használhatja az 1-es és `delay_evaluation` 5-ös medián megállási politikát. Ezek konzervatív beállítások, amelyek körülbelül 25%35%megtakarítást biztosítanak az elsődleges metrika vesztesége nélkül (értékelési adataink alapján).
* Ha a korai megszüntetésből származó agresszívebb megtakarításokat keres, használhatja a Bandit Policy-t egy szigorúbb (kisebb) megengedett tartalékidővel vagy csonkolási beállítási házirenddel, nagyobb csonkolási százalékkal.

## <a name="allocate-resources"></a>Erőforrások felosztása

Szabályozhatja az erőforrás-költségvetést a hiperparaméter-hangolási kísérlethez a betanítási futtatások maximális teljes számának megadásával.  Adja meg a hiperparaméter-hangolási kísérlet maximális időtartamát.

* `max_total_runs`: A létrehozandó képzési futtatások maximális száma. Felső határ – előfordulhat, hogy kevesebb fut, például, ha a hiperparaméter tér véges, és kevesebb mintát tartalmaz. 1 és 1000 közötti számnak kell lennie.
* `max_duration_minutes`: A hiperparaméter-hangolási kísérlet maximális időtartama percben. A paraméter megadása nem kötelező, és ha van ilyen, akkor az időtartam után futó futtatások automatikusan törlődnek.

>[!NOTE] 
>Ha `max_total_runs` mindkettő, és `max_duration_minutes` meg van adva, a hiperparaméter-hangolási kísérlet befejeződik, amikor az első a két küszöbértékek elérésekor.

Ezenkívül adja meg a hiperparaméter-hangolási keresés során egyidejűleg futtatandó betanítási futtatások maximális számát.

* `max_concurrent_runs`: Az adott pillanatban egyidejűleg futtatható futtatások maximális száma. Ha nincs megadva, az összes `max_total_runs` párhuzamosan indul el. Ha meg van adva, 1 és 100 közötti számnak kell lennie.

>[!NOTE] 
>Az egyidejű futtatások száma a megadott számítási célban rendelkezésre álló erőforrásokon van gated. Ezért meg kell győződnie arról, hogy a számítási cél rendelkezik a rendelkezésre álló erőforrásokkal a kívánt egyidejűséghez.

Erőforrások lefoglalása a hiperparaméter-hangoláshoz:

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Ez a kód úgy konfigurálja a hiperparaméter-hangolási kísérletet, hogy egyszerre legfeljebb 20 összes futtatást használjon, és egyszerre négy konfigurációt fut.

## <a name="configure-experiment"></a>Kísérlet konfigurálása

[Konfigurálja a hiperparaméter-hangolási](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?view=azure-ml-py) kísérletet a megadott hiperparaméter-keresési terület, a korai befejezési házirend, az elsődleges metrika és a fenti szakaszok erőforrás-elosztás használatával. Emellett adjon meg `estimator` egy olyan, amely a mintavételezett hiperparaméterekkel lesz hívva. A `estimator` ismerteti a betanítási parancsfájl futtatja, a feladatonkénti erőforrások (egy vagy több gpu), és a számítási cél használata. Mivel a hiperparaméter-hangolási kísérlet egyidejűsége a rendelkezésre álló erőforrásokon van `estimator` bevezetve, győződjön meg arról, hogy a számítási cél a megadott elegendő erőforrással rendelkezik a kívánt egyidejűséghez. (A becslésekről további információt a [modellek betanításáról](how-to-train-ml-models.md)talál.)

Konfigurálja a hiperparaméter-hangolási kísérletet:

```Python
from azureml.train.hyperdrive import HyperDriveConfig
hyperdrive_run_config = HyperDriveConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="submit-experiment"></a>Kísérlet beküldése

Miután definiálta a hiperparaméter-hangolási konfigurációt, [küldjön be egy kísérletet:](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py#submit-config--tags-none----kwargs-)

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

`experiment_name`a hiperparaméter-hangolási kísérlethez rendelt `workspace` név, és az a munkaterület, amelyben létre szeretné hozni a kísérletet (További információ a kísérletekről: ["Hogyan működik az Azure Machine Learning?"](concept-azure-machine-learning-architecture.md))

## <a name="warm-start-your-hyperparameter-tuning-experiment-optional"></a>A hiperparaméter-hangolási kísérlet bemelegítése (nem kötelező)

Gyakran a modell legjobb hiperparaméter-értékeinek megtalálása iteratív folyamat lehet, amelynek több hangolási futtatásra van szüksége, amelyek a korábbi hiperparaméter-hangolási futtatásokból tanulnak. Az előző futtatások ból származó ismeretek újrafelhasználása felgyorsítja a hiperparaméter-hangolási folyamatot, ezáltal csökkentve a modell finomhangolásának költségét, és potenciálisan javítja az eredményül kapott modell elsődleges metrikáját. Amikor meleg indítása hiperparaméter-hangolási kísérlet Bayes mintavételezés, kísérletek az előző futtatás i előzetes tudásként intelligensen új mintákat, az elsődleges metrika javítása érdekében. Emellett véletlenszerű vagy grid mintavételezés használatakor a korai megszüntetési döntések az előző futtatások metrikáit használja a rosszul teljesítő betanítási futtatások meghatározásához. 

Az Azure Machine Learning lehetővé teszi, hogy indítsa el a hiperparaméter-hangolási futtatását a tudás akár 5 korábban befejezett / törölt hyperparameter tuning szülő fut. Megadhatja a szülőfuttatások listáját, amelyeket a kódrészlet használatával szeretne felmelegíteni:

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Emellett előfordulhatnak olyan esetek, amikor egy hiperparaméter-hangolási kísérlet egyéni betanítási futtatása idotartama költségvetési megszorítások miatt megszakad, vagy más okok miatt sikertelen. Most már lehetséges, hogy folytassa az ilyen egyéni betanítási fut az utolsó ellenőrzőpont (feltételezve, hogy a betanítási parancsfájl kezeli ellenőrzőpontok). Az egyéni betanítási futtatás folytatása ugyanazt a hiperparaméter-konfigurációt fogja használni, és csatlakoztatja a futtatáshoz használt kimeneti mappát. A betanítási `resume-from` parancsfájlnak el kell fogadnia az argumentumot, amely tartalmazza az ellenőrzőpont- vagy modellfájlokat, amelyekből folytathatja a betanítási futtatást. Az egyes képzési műveleteket a következő kódrészlethasználatával folytathatja:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

A hiperparaméter-hangolási kísérletet beállíthatja úgy, hogy egy korábbi kísérlet `resume_from` `resume_child_runs` kezdete, vagy folytassa az egyéni betanítási futtatásokat a választható paraméterek kel és a konfigurációban:

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hyperdrive_run_config = HyperDriveConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          resume_from=warmstart_parents_to_resume_from, 
                          resume_child_runs=child_runs_to_resume,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="visualize-experiment"></a>Kísérlet megjelenítése

Az Azure Machine Learning SDK egy [jegyzetfüzet-widgetet](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py) biztosít, amely vizualizálja a betanítási futtatások előrehaladását. A következő kódrészlet vizualizálja az összes hiperparaméter-hangolási fut egy helyen egy Jupyter notebook:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Ez a kód egy táblázatot jelenít meg az egyes hiperparaméter-konfigurációk betanítási futtatásainak részleteivel.

![hyperparameter tuning tábla](./media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

Azt is vizualizálhatja a teljesítményét az egyes fut a képzés előrehaladtával. 

![hiperparaméter-hangolási telek](./media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Ezenkívül vizuálisan azonosíthatja a teljesítmény és az egyes hiperparaméterek értékei közötti korrelációt egy párhuzamos koordináta-ábrázolási telek használatával. 

[![hyperparameter tuning párhuzamos koordináták](./media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

Az Azure-webportálon is vizualizálhatja az összes hiperparaméter-hangolási futtatást. A kísérletek internetes portálon való megtekintéséről a [kísérletek nyomon követéséről](how-to-track-experiments.md#view-the-experiment-in-the-web-portal)talál további információt.

## <a name="find-the-best-model"></a>Találja meg a legjobb modellt

Miután az összes hiperparaméter-hangolási futtatás befejeződött, [azonosítsa a legjobban teljesítő konfigurációt](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverun?view=azure-ml-py#get-best-run-by-primary-metric-include-failed-true--include-canceled-true--include-resume-from-runs-true-----typing-union-azureml-core-run-run--nonetype-) és a megfelelő hiperparaméter-értékeket:

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

## <a name="sample-notebook"></a>Mintajegyzetfüzet
Tekintse meg a mappában található betanítási-hiperparaméter-* jegyzetfüzeteket:
* [how-to-use-azureml/training-with-deep-learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések
* [Kísérlet nyomon követése](how-to-track-experiments.md)
* [Betanított modell telepítése](how-to-deploy-and-where.md)
