---
title: "Fut a legjobb pontosság és a legalacsonyabb időtartama az Azure Machine Learning-munkaterület található |} Microsoft Docs"
description: "Egy végpont használati eset legpontosabb parancssori felületen keresztül keresése az Azure Machine Learning-munkaterület segítségével"
services: machine-learning
author: totekp
ms.author: kefzhou
manager: akannava
ms.reviewer: akannava, haining, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: aaadf526577b9b6c254204aae90200661d40f325
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2017
---
# <a name="find-runs-with-the-best-accuracy-and-lowest-duration"></a>Keresés fut, a legjobb pontosság és a legalacsonyabb időtartama
Megadott több fut, a egy használati eset futtatása a legjobb pontossággal kereséséhez. A parancssori felület (CLI) használatával egyik módszer egy [JMESPath](http://jmespath.org/) lekérdezés. Az Azure parancssori felületen JMESPath használatával további információkért lásd: [JMESPath használjon lekérdezések az Azure CLI 2.0](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest). A következő példában négy futtatása 0, 0,98., 1 és 1 pontossága értékekkel jönnek létre. Futtatja a rendszer a tartományon lévő `[MaxAccuracy-Threshold, MaxAccuracy]` ahol `Threshold = .03`.

## <a name="sample-data"></a>Mintaadatok
Ha még nem rendelkezik a meglévő fut egy `Accuracy` érték, a következő lépéseket készítése fut a lekérdezésre.

Először hozzon létre egy Python-fájl az Azure Machine Learning-munkaterület, adjon neki nevet `log_accuracy.py`, és illessze be a következő kódot:
```python
from azureml.logging import get_azureml_logger

logger = get_azureml_logger()

accuracy_value = 0.5

if len(sys.argv) > 1:
     accuracy_value = float(sys.argv[1])

logger.log("Accuracy", accuracy_value)
```

Következő lépésként hozzon létre egy fájlt `run.py`, és illessze be a következő kódot:
```python
import os

accuracy_values = [0, 0.98, 1.0, 1.0]
for value in accuracy_values:
    os.system('az ml experiment submit -c local ./log_accuracy.py {}'.format(value))
```

Végül nyissa meg a parancssori felületen keresztül munkaterület és a parancs `python run.py` négy kísérletek elküldeni. A parancsfájl befejezése után megjelenik a négy további fut a `Run History` fülre.

## <a name="query-the-run-history"></a>A lekérdezés a futtatási előzményei
Az első parancs maximális pontosság talál.
```powershell
az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
```

A maximális pontossága értékének felhasználásával `1` és egy küszöbértéket `0.03`, a második parancs szűrők használatával futtat `Accuracy` majd rendezi futtatja által `duration` növekvő.
```powershell
az ml history list --query '@[?Accuracy >= sum(`[1, -0.03]`)] | sort_by(@, &duration)'
```
> [!NOTE]
> Ha azt szeretné, hogy egy szigorú felsőé ellenőrzést, a lekérdezés formátuma``@[?Accuracy >= sum(`[$max_accuracy_value, -$threshold]`) && Accuracy <= `$max_accuracy_value`]``

PowerShell használatakor a következő kódot helyi változók segítségével tárolja a küszöbérték és a maximális pontosság:
```powershell
$threshold = 0.03
$max_accuracy_value = az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
$find_runs_query = '@[?Accuracy >= sum(`[{0}, -{1}]`)] | sort_by(@, &duration)' -f $max_accuracy_value, $threshold
az ml history list --query $find_runs_query
```

## <a name="next-steps"></a>Következő lépések
További információt a naplózást, [futtatási előzményei és modell metrikák használata az Azure Machine Learning-munkaterület](how-to-use-run-history-model-metrics.md).    
