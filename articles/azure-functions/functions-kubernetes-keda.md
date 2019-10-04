---
title: Azure Functions a Kubernetes és a KEDA
description: Megtudhatja, hogyan futtathat Azure Functions a felhőben vagy a helyszínen a Kubernetes-ben a KEDA, Kubernetes-alapú Event vezérelt automatikus skálázás használatával.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: Azure functions, functions, Event Processing, dinamikus számítás, kiszolgáló nélküli architektúra, kubernetes
ms.service: azure-functions
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: jehollan
ms.openlocfilehash: b581d7c9b5876813e36ebbf41be713b44dd97735
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096097"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Azure Functions a Kubernetes és a KEDA

A Azure Functions futtatókörnyezet rugalmasságot biztosít a üzemeltetésben, ahol és így tovább.  [KEDA](https://github.com/kedacore/kore) (A Kubernetes-alapú eseményvezérelt automatikus skálázás) párok zökkenőmentesen használhatók a Azure Functions futtatókörnyezettel és az Kubernetes-alapú esemény-vezérelt méretezés biztosításához.

## <a name="how-kubernetes-based-functions-work"></a>A Kubernetes-alapú függvények működése

A Azure Functions szolgáltatás két kulcsfontosságú összetevőből áll: egy futtatókörnyezetből és egy méretezési vezérlőből.  A functions futtatókörnyezet futtatja és végrehajtja a kódot.  A futtatókörnyezet logikát tartalmaz a függvények végrehajtásának elindításához, naplózásához és kezeléséhez.  A másik összetevő egy méretezési vezérlő.  A skálázási vezérlő figyeli a függvényt célzó események arányát, és proaktív módon méretezi az alkalmazást futtató példányok számát.  További információ: [Azure functions skálázás és üzemeltetés](functions-scale.md).

A Kubernetes-alapú függvények egy Docker- [tárolóban](functions-create-function-linux-custom-image.md) lévő functions futtatókörnyezetet biztosítanak eseményvezérelt skálázással az KEDA-on keresztül.  A KEDA 0 példányra (ha nincs esemény) és legfeljebb *n* példányra méretezhető. Ezt a Kubernetes autoscaleer (horizontális Pod autoskálázás) egyéni metrikáinak kiosztásával teszi meg.  A functions-tárolók és a KEDA lehetővé teszi a kiszolgáló nélküli függvények replikálását bármely Kubernetes-fürtön.  Ezek a függvények az [Azure Kubernetes Services (ak) virtuális csomópontok](../aks/virtual-nodes-cli.md) szolgáltatásával is üzembe helyezhetők a kiszolgáló nélküli infrastruktúra számára.

## <a name="managing-keda-and-functions-in-kubernetes"></a>KEDA és függvények kezelése a Kubernetes-ben

A függvények a Kubernetes-fürtön való futtatásához telepítenie kell a KEDA összetevőt. Ezt az összetevőt [Azure functions Core Tools](functions-run-local.md)használatával telepítheti.

### <a name="installing-with-the-azure-functions-core-tools"></a>Telepítés a Azure Functions Core Tools

Alapértelmezés szerint a Core Tools a KEDA és az Osiris összetevőket is telepíti, amelyek támogatják az eseményvezérelt és a HTTP-méretezést.  A telepítés `kubectl` az aktuális környezetben fut.

Telepítse a KEDA a fürtben a következő telepítési parancs futtatásával:

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Function-alkalmazás üzembe helyezése a Kubernetes

A KEDA-t futtató Kubernetes-fürtökön bármely Function alkalmazást üzembe helyezhet.  Mivel a függvények Docker-tárolóban futnak, a projekthez a `Dockerfile`szükséges.  Ha még nem rendelkezik ilyennel, hozzáadhat egy Docker az alábbi parancs futtatásával a functions projekt gyökerében:

```cli
func init --docker-only
```

A következő parancs futtatásával hozzon létre egy rendszerképet, és telepítse a függvényeket a Kubernetes:

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Cserélje `<name-of-function-deployment>` le a helyére a Function alkalmazás nevét.

Ez létrehoz egy Kubernetes `Deployment` -erőforrást `ScaledObject` , egy erőforrást, és `Secrets`a `local.settings.json` fájlból importált környezeti változókat is.

## <a name="removing-a-function-app-from-kubernetes"></a>Function-alkalmazás eltávolítása a Kubernetes-ből

A telepítés után eltávolíthatja a függvényt a társított `Deployment`, `ScaledObject` `Secrets` a létrehozott elem eltávolításával.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>A KEDA eltávolítása a Kubernetes-ből

A következő alapvető eszközök parancs futtatásával távolíthatja el a KEDA egy Kubernetes-fürtből:

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>Támogatott eseményindítók a KEDA-ben

A KEDA jelenleg béta verzióban érhető el a következő Azure Function triggerek támogatásával:

* [Azure Storage-várólisták](functions-bindings-storage-queue.md)
* [Azure Service Bus várólisták](functions-bindings-service-bus.md)
* [HTTP](functions-bindings-http-webhook.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)

## <a name="next-steps"></a>További lépések
További információkért lásd a következőket:

* [Függvény létrehozása egyéni rendszerkép használatával](functions-create-function-linux-custom-image.md)
* [Az Azure Functions helyi kódolása és tesztelése](functions-develop-local.md)
* [Az Azure Function fogyasztási terv működése](functions-scale.md)