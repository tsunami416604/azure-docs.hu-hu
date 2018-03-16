---
title: "Az Azure Machine Learning-telepítés hibaelhárítási útmutatója |} Microsoft Docs"
description: "A központi telepítés és a szolgáltatások létrehozásakor hibaelhárítási útmutatója"
services: machine-learning
author: AashishB
ms.author: AashishB
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 614767840f8781c3c30c358dcf3ffc366aa3c0d6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="troubleshooting-service-deployment-and-environment-setup"></a>Hibaelhárítás a szolgáltatás telepítése és a környezet beállítása
A következő információ segíthet a hiba okának megállapításához a modell felügyeleti környezet beállításakor.

## <a name="model-management-environment"></a>Modell felügyeleti környezet
### <a name="contributor-permission-required"></a>A közreműködői engedély szükséges
Az előfizetés vagy az erőforráscsoportot a webes szolgáltatások telepítését a fürt beállítása közreműködői hozzáférés szükséges.

### <a name="resource-availability"></a>Erőforrás rendelkezésre állása
Az előfizetésben elérhető erőforrásokkal rendelkezik, ezért a környezet erőforrásokat oszthat kell.

### <a name="subscription-caps"></a>Előfizetés Caps
Előfordulhat, hogy az előfizetés egy kap a számlázást, ami megakadályozhatja a környezet erőforrások kiépítése. Távolítsa el a cap-kiépítés engedélyezése.

### <a name="enable-debug-and-verbose-options"></a>Hibakeresés és részletes beállítások engedélyezése
Használja a `--debug` és `--verbose` jelzők a hibakeresési és nyomkövetési információk megjelenítése, a környezet telepítése folyamatban van a setup parancs.

```
az ml env setup -l <location> -n <name> -c --debug --verbose 
```

## <a name="service-deployment"></a>Szolgáltatás központi telepítése
A következő információk segítségével határozható meg, hogy a hibák okait üzembe helyezése során, vagy a webes szolgáltatás hívása.

### <a name="service-logs"></a>Service naplóit
A `logs` lehetőséget a szolgáltatás CLI ez naplóadatok Docker és Kubernetes.

```
az ml service logs realtime -i <web service id>
```

További naplóbeállítások, használja a `--help` (vagy `-h`) lehetőséget.

```
az ml service logs realtime -h
```

### <a name="debug-and-verbose-options"></a>Hibakeresés és részletes beállítások
Használja a `--debug` jelzőjét, hogy hibakeresési naplók megjelenítése, mivel a szolgáltatás telepítése.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --debug
```

Használja a `--verbose` jelzőt, amely szerint a szolgáltatás telepítése További részletek megtekintéséhez.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --verbose
```

### <a name="enable-request-logging-in-app-insights"></a>Kérelem App Insights-naplózás engedélyezése
Állítsa be a `-l` jelzője true értékre, ha a kérelem webhelyszintű naplózás engedélyezéséhez webes szolgáltatás létrehozása. A kérelem naplók kerülnek a környezet az Azure App Insights példányát. Ez a példány használata esetén használt környezet neve keresése a `az ml env setup` parancsot.

- Állítsa be `-l` true értékre, ha a szolgáltatás létrehozása.
- Nyissa meg az App Insights az Azure-portálon. A környezet neve segítségével App Insights-példány keresése.
- Egyszer App Insights, kattintson a Keresés a felső menüben, az eredmények megtekintéséhez.
- Vagy `Analytics`  >  `Exceptions`  >  `exceptions take | 10`.


### <a name="add-error-handling-in-scoring-script"></a>Hiba történt a parancsfájl pontozási kezelése hozzáadása
A kivételkezelő használja a `scoring.py` parancsfájl **futtatása** függvény által visszaadott hibaüzenet a webes szolgáltatás kimeneti részeként.

Python példa:
```
    try:
        <code to load model and score>
   except Exception as e:
        return(str(e))
```

## <a name="other-common-problems"></a>Egyéb gyakori problémák
- Ha az azure cli-ml pip telepítési hibával meghiúsul `cannot find the path specified` Windows-gépen, hosszú elérési engedélyezni kell. Lásd: https://blogs.msdn.microsoft.com/jeremykuhne/2016/07/30/net-4-6-2-and-long-paths-on-windows-10/. 
- Ha a `env setup` parancs `LocationNotAvailableForResourceType`, valószínűleg használja a megfelelő helyre (régió) gépi tanulási erőforrásokat. Győződjön meg arról, hogy a meghatározott helyre a `-l` paraméter `eastus2`, `westcentralus`, vagy `australiaeast`.
- Ha a `env setup` parancs `Resource quota limit exceeded`, győződjön meg arról, hogy van elég az előfizetésben elérhető magok és, hogy az erőforrások nem használják fel egyéb folyamatok.
- Ha a `env setup` parancs `Invalid environment name. Name must only contain lowercase alphanumeric characters`, győződjön meg arról, hogy a szolgáltatás neve nem tartalmaz-nagybetűk, szimbólumok vagy az aláhúzás (_) (mint a *my_environment*).
- Ha a `service create` parancs `Service Name: [service_name] is invalid. The name of a service must consist of lower case alphanumeric characters (etc.)`, ellenőrizze, hogy a szolgáltatás neve 3 – 32 karakter hosszúságúnak; elindul és kisbetűs alfanumerikus karaktereket; végződik és nem tartalmaz nagybetűk, szimbólumok kötőjel (-) és időszakon kívül ( . ), vagy a aláhúzásjellel (_) (mint a *my_webservice*).
- Próbálja meg újra, ha egy `502 Bad Gateway` hiba, ha a webes szolgáltatás hívása. Ez általában azt jelenti, hogy a tároló még nem lett alkalmazva a fürt még.
- Ha `CrashLoopBackOff` hiba, ha a szolgáltatás létrehozása a naplókban. Általában a Hiányzó függőségek eredményét a **init** függvény.
