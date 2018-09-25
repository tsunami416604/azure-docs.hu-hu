---
title: Hibaelhárítási útmutató az Azure Machine Learning üzembe helyezése |} A Microsoft Docs
description: Hibaelhárítási útmutató az üzembe helyezés és a szolgáltatás létrehozása
services: machine-learning
author: aashishb
ms.author: aashishb
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/16/2017
ROBOTS: NOINDEX
ms.openlocfilehash: a2867545e454e2b13360d87f5282e684753d6476
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994496"
---
# <a name="troubleshooting-service-deployment-and-environment-setup"></a>Hibaelhárítási szolgáltatás üzembe helyezésének és a környezet beállítása

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

A következő információ segíthet a hiba okának megállapításához a modell felügyeleti környezetének beállítása során.

## <a name="model-management-environment"></a>Modell felügyeleti környezetének
### <a name="contributor-permission-required"></a>Szükséges közreműködői engedélyt
Közreműködői hozzáférés szükséges az előfizetéshez vagy az erőforráscsoport, a webszolgáltatások üzembe helyezéséhez egy fürt beállításához van szüksége.

### <a name="resource-availability"></a>Erőforrás rendelkezésre állása
Az előfizetésben elérhető erőforrásokkal rendelkezik, így kioszthatja a környezet erőforrásait kell.

### <a name="subscription-caps"></a>Előfizetés Caps
Előfordulhat, hogy az előfizetés-korlát a számlázási, amely megakadályozhatja az erőforrásoknak a környezetben. Távolítsa el a Cap-hoz, a kiépítés engedélyezéséhez.

### <a name="enable-debug-and-verbose-options"></a>Hibakeresési és beállításokat verbose engedélyezése
Használja a `--debug` és `--verbose` jelzőt a setup parancs hibakeresési és a nyomkövetési információk megjelenítéséhez, mivel a környezet kiépítése folyamatban van.

```
az ml env setup -l <location> -n <name> -c --debug --verbose 
```

## <a name="service-deployment"></a>Szolgáltatástelepítés
A következő információk segítségével határozza meg, a hibák okait üzembe helyezés során vagy a webszolgáltatás hívásakor.

### <a name="service-logs"></a>Szolgáltatás naplói
A `logs` lehetőséget a szolgáltatás CLI naplóadatok biztosít a Docker és a Kubernetes.

```
az ml service logs realtime -i <web service id>
```

A további naplózási beállításai, használja a `--help` (vagy `-h`) lehetőséget.

```
az ml service logs realtime -h
```

### <a name="debug-and-verbose-options"></a>Hibakeresési és a részletes beállítások
Használja a `--debug` jelző, hibakeresési naplók megjelenítése, mivel a szolgáltatás telepítése.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --debug
```

Használja a `--verbose` jelző, a szolgáltatás telepítése a további részletek megtekintéséhez.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --verbose
```

### <a name="enable-request-logging-in-app-insights"></a>Az App Insights-naplózás kérelem engedélyezéséhez
Állítsa be a `-l` jelzőt true értékre, ha a kérés webhelyszintű naplózás engedélyezése webes szolgáltatás létrehozásához. A kérelem naplók az App Insights-példány a környezet az Azure-ban írt. Keresse meg a használata esetén használt környezet neve használatával ezt a példányt a `az ml env setup` parancsot.

- Állítsa be `-l` true értékre, ha a szolgáltatás létrehozása.
- Nyissa meg az App Insights az Azure Portalon. Használja a környezet nevét az App Insights-példány található.
- Egyszer az App Insights, kattintson a Keresés az eredmények megtekintése a felső menüben.
- Vagy az `Analytics`  >  `Exceptions`  >  `exceptions take | 10`.


### <a name="add-error-handling-in-scoring-script"></a>A pontozó szkript hibakezelés
Kivételkezelés használja a `scoring.py` szkript **futtatása** függvény által visszaadott a hibaüzenet a web service kimeneti részeként.

Python-példát:
```
    try:
        <code to load model and score>
   except Exception as e:
        return(str(e))
```

## <a name="other-common-problems"></a>Egyéb gyakori problémák
- Ha az azure-cli-ml pip telepítése sikertelen, és a `cannot find the path specified` egy Windows-gépen, engedélyeznie kell a hosszú elérési támogatása. Lásd: https://blogs.msdn.microsoft.com/jeremykuhne/2016/07/30/net-4-6-2-and-long-paths-on-windows-10/. 
- Ha a `env setup` parancs `LocationNotAvailableForResourceType`, a machine learning-erőforrások valószínűleg használja a nem megfelelő helyhez (régiót). Győződjön meg arról, hogy a helyre a `-l` paraméter `eastus2`, `westcentralus`, vagy `australiaeast`.
- Ha a `env setup` parancs `Resource quota limit exceeded`, ellenőrizze, hogy elegendő az előfizetésében elérhető maggal rendelkezik, és, hogy az erőforrások nem használják fel más folyamatokban.
- Ha a `env setup` parancs `Invalid environment name. Name must only contain lowercase alphanumeric characters`, győződjön meg arról, hogy a szolgáltatás neve nem tartalmaz, nagybetűk, szimbólumok vagy a aláhúzásjelet (_) (mint a *my_environment*).
- Ha a `service create` parancs `Service Name: [service_name] is invalid. The name of a service must consist of lower case alphanumeric characters (etc.)`, győződjön meg arról, hogy a szolgáltatás neve 3 – 32 karakter közötti hosszúságú; kezdődik és végződik a kisbetűs alfanumerikus karaktereket:; és nem tartalmaz a nagybetűk, szimbólumok kötőjel (-) és az időszak kivételével ( . ), vagy a aláhúzásjelet (_) (mint a *my_webservice*).
- Próbálkozzon újra, ha egy `502 Bad Gateway` hiba a webszolgáltatás hívásakor. Azt általában azt jelenti, hogy a tároló még nem vezette a fürthöz.
- Ha a `CrashLoopBackOff` egy szolgáltatás létrehozásakor hiba történt a naplókban. Általában a hiányzó függőségeit, az eredmény a **init** függvény.
