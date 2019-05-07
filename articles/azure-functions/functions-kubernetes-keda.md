---
title: Az Azure Functions KEDA a kubernetesszel
description: Megismerheti az Azure Functions futtatása a Kubernetes felhőbeli vagy helyszíni KEDA, Kubernetes-alapú eseményvezérelt automatikus skálázás használatával.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: az Azure functions, függvények, eseményfeldolgozás, dinamikus számítás, kiszolgáló nélküli architektúra, kubernetes
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: jehollan
ms.openlocfilehash: c82ed7aa841f53f5c81f3281ed1b09926e565e75
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077620"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Az Azure Functions KEDA a kubernetesszel

Az Azure Functions runtime rugalmasságot kínál üzemeltetési, hogyan szeretné.  [KEDA](https://github.com/kedacore/kore) (Kubernetes-alapú esemény alapuló automatikus skálázás) zökkenőmentesen az Azure Functions runtime és azokat az eszközöket biztosít a Kubernetesben eseményvezérelt méretezési párokkal.

## <a name="how-kubernetes-based-functions-work"></a>Hogyan Kubernetes-alapú működnek

Az Azure Functions szolgáltatást két fő összetevőből épül fel: egy modult és a egy méretezési csoport vezérlő.  A Functions-futtatókörnyezetben fut, és végrehajtja a kódot.  A futásidejű trigger, log, és a függvénykivételek kezelése a logikát tartalmaz.  Az összetevővel a méretezési csoport vezérlő.  A méretezési csoport vezérlő az eseményeket, amelyek céloz meg a függvény arányát figyeli, és proaktív módon méretezi az alkalmazást futtató példányok száma.  További tudnivalókért lásd: [Azure Functions méretezése és üzemeltetése](functions-scale.md).

Kubernetes-alapú funkciókat biztosít a Functions futtatókörnyezete az egy [Docker-tároló](functions-create-function-linux-custom-image.md) keresztül KEDA eseményvezérelt méretezéssel.  0-példányok (ha nincsenek események történnek), és akár KEDA vertikális *n* példányok. Ezt nem teszi elérhetővé egyéni metrikák az automatikus méretező Kubernetes (vízszintes Pod méretező).  KEDA funkciók tárolók használata lehetővé teszi bármely Kubernetes-fürtben lévő kiszolgáló nélküli függvény képességek replikálni.  Ezek a függvények használatával is telepíthető [Azure Kubernetes-szolgáltatások (AKS) virtuális csomópontok](../aks/virtual-nodes-cli.md) kiszolgáló nélküli infrastruktúra szolgáltatást.

## <a name="managing-keda-and-functions-in-kubernetes"></a>KEDA és a Kubernetesben függvények kezelése

Függvények futtatni a Kubernetes-fürthöz, telepítenie kell a KEDA összetevő. Ezen összetevő használatával telepítheti [Azure Functions Core Tools](functions-run-local.md).

### <a name="installing-with-the-azure-functions-core-tools"></a>Az Azure Functions Core Tools telepítése

Alapértelmezés szerint Core Tools telepítése KEDA és Osiris összetevők, amelyek támogatják az eseményvezérelt és a HTTP-méretezés, illetve.  A telepítés használja `kubectl` fut az aktuális környezetben.

A fürt KEDA telepítse a következő telepítési parancs futtatásával:

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Függvényalkalmazás üzembe helyezése Kubernetes

Telepítheti a függvényalkalmazások KEDA futó Kubernetes-fürthöz.  A függvények futtatása Docker-tárolóban, mivel a projekthez szükséges egy `Dockerfile`.  Ha ezt még nem rendelkezik egy, a Functions-projekt gyökérkönyvtárában található a következő parancs futtatásával adhat hozzá egy docker-fájlban:

```cli
func init --docker-only
```

Állítson össze egy rendszerképet, és a Kubernetes üzembe helyezni a függvények, futtassa a következő parancsot:

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Cserélje le `<name-of-function-deployment>` a függvényalkalmazás nevére.

Ez létrehoz egy Kubernetes `Deployment` erőforrás, egy `ScaledObject` erőforrás, és `Secrets`, amely tartalmazza a környezeti változók importálására a `local.settings.json` fájlt.

## <a name="removing-a-function-app-from-kubernetes"></a>Függvényalkalmazás eltávolítása a Kubernetes

Miután üzembe helyezése, távolíthatja el egy függvényt a társított eltávolításával `Deployment`, `ScaledObject`, egy `Secrets` létrehozott.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Kubernetes KEDA eltávolítása

KEDA eltávolítása egy Kubernetes-fürtöt a következő alapvető eszközök parancsot futtathatja:

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>Támogatott KEDA eseményindítók

KEDA jelenleg bétaverzióban támogatásával, az alábbi Azure-függvény eseményindítók:

* [Az Azure Storage-üzenetsorok](functions-bindings-storage-queue.md)
* [Az Azure Service Bus-üzenetsorok](functions-bindings-service-bus.md)
* [HTTP](functions-bindings-http-webhook.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)

## <a name="next-steps"></a>További lépések
További információkért lásd a következőket:

* [Egyéni rendszerkép használatával függvény létrehozása](functions-create-function-linux-custom-image.md)
* [Az Azure Functions helyi kódolása és tesztelése](functions-develop-local.md)
* [Az Azure Functions használatalapú tarifacsomagjának működése](functions-scale.md)