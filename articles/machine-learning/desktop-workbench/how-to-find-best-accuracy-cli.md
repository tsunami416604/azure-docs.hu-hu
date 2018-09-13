---
title: A legpontosabb és legrövidebb az Azure Machine Learning Workbenchben futtatások keresése |} A Microsoft Docs
description: Egy teljes körű használati eset keresése legpontosabb CLI-n keresztül az Azure Machine Learning Workbench használatával
services: machine-learning
author: totekp
ms.author: kefzhou
manager: akannava
ms.reviewer: akannava, haining, mldocs, jmartens, jasonwhowell
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: d2fe951a97b18c95e647b45d799843a982100367
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35926164"
---
# <a name="find-runs-with-the-best-accuracy-and-lowest-duration"></a>A legpontosabb és legrövidebb futtatások megkeresése
Adja meg a több futtatás, egy funkcióban a legpontosabb futtatás keresése. Az egyik módszere, a parancssori felület (CLI) használata egy [JMESPath](http://jmespath.org/) lekérdezés. Az Azure CLI JMESPath használatáról további információkért lásd: [használata JMESPath-lekérdezések az Azure CLI-vel](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest). A következő példában négy futtatások pontosság 0, 0,98., 1 és 1 értékeket jönnek létre. Futtatások szűr a rendszer, ha azok a tartomány `[MaxAccuracy-Threshold, MaxAccuracy]` ahol `Threshold = .03`.

## <a name="sample-data"></a>Mintaadatok
Ha nem rendelkezik meglévő fut egy `Accuracy` értékét, az alábbi lépések futtatásának lekérdezése készítése.

Először is hozzon létre egy Python-fájlt az Azure Machine Learning Workbench, nevezze el `log_accuracy.py`, és illessze be az alábbi kódot:
```python
from azureml.logging import get_azureml_logger

logger = get_azureml_logger()

accuracy_value = 0.5

if len(sys.argv) > 1:
     accuracy_value = float(sys.argv[1])

logger.log("Accuracy", accuracy_value)
```

Következő lépésként hozzon létre egy fájlt `run.py`, és illessze be az alábbi kódot:
```python
import os

accuracy_values = [0, 0.98, 1.0, 1.0]
for value in accuracy_values:
    os.system('az ml experiment submit -c local ./log_accuracy.py {}'.format(value))
```

Végül, a Workbench parancssori megnyitásához, és futtassa a parancsot `python run.py` négy kísérletek elküldése. A szkript befejezése után megjelenik a négy további futtatási a `Run History` fülre.

## <a name="query-the-run-history"></a>Lekérdezés a futtatási előzmények
Az első parancs megkeresi a maximális pontossága értéket.
```powershell
az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
```

Használja a következő maximális pontosság értéke `1` és a egy küszöbértéket `0.03`, a második parancs szűrők használatával futtat `Accuracy` és szerint rendezi futtat `duration` növekvő.
```powershell
az ml history list --query '@[?Accuracy >= sum(`[1, -0.03]`)] | sort_by(@, &duration)'
```
> [!NOTE]
> Ha azt szeretné, hogy egy szigorú felső ellenőrzést, a lekérdezés formátuma ``@[?Accuracy >= sum(`[$max_accuracy_value, -$threshold]`) && Accuracy <= `$max_accuracy_value`]``

Ha a PowerShell segítségével, az alábbi kód helyi változók segítségével tárolja a küszöbérték és a maximális pontosság:
```powershell
$threshold = 0.03
$max_accuracy_value = az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
$find_runs_query = '@[?Accuracy >= sum(`[{0}, -{1}]`)] | sort_by(@, &duration)' -f $max_accuracy_value, $threshold
az ml history list --query $find_runs_query
```

## <a name="next-steps"></a>További lépések
A naplózás további információkért lásd: [futtatási előzmények és modell metrikák használata az Azure Machine Learning Workbenchben](how-to-use-run-history-model-metrics.md).    
