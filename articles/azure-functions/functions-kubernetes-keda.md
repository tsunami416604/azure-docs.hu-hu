---
title: Azure Functions a Kubernetes és a KEDA
description: Megtudhatja, hogyan futtathat Azure Functions a felhőben vagy a helyszínen a Kubernetes-ben a KEDA, Kubernetes-alapú Event vezérelt automatikus skálázás használatával.
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 9978bd567b1b07e8dd0e22e1f02834626281a5dd
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2020
ms.locfileid: "75920664"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Azure Functions a Kubernetes és a KEDA

A Azure Functions futtatókörnyezet rugalmasságot biztosít a üzemeltetésben, ahol és így tovább.  A [KEDA](https://keda.sh) (Kubernetes-alapú eseményvezérelt automatikus skálázás) párok zökkenőmentesen használhatók a Azure functions futtatókörnyezettel és az Kubernetes-alapú eseményvezérelt méretezés biztosításához.

## <a name="how-kubernetes-based-functions-work"></a>A Kubernetes-alapú függvények működése

A Azure Functions szolgáltatás két kulcsfontosságú összetevőből áll: egy futtatókörnyezetből és egy méretezési vezérlőből.  A functions futtatókörnyezet futtatja és végrehajtja a kódot.  A futtatókörnyezet logikát tartalmaz a függvények végrehajtásának elindításához, naplózásához és kezeléséhez.  A Azure Functions futtatókörnyezet *bárhol*futhat.  A másik összetevő egy méretezési vezérlő.  A skálázási vezérlő figyeli a függvényt célzó események arányát, és proaktív módon méretezi az alkalmazást futtató példányok számát.  További információ: [Azure functions skálázás és üzemeltetés](functions-scale.md).

A Kubernetes-alapú függvények egy [Docker-tárolóban](functions-create-function-linux-custom-image.md) lévő functions futtatókörnyezetet biztosítanak eseményvezérelt skálázással az KEDA-on keresztül.  A KEDA 0 példányra (ha nincs esemény) és legfeljebb *n* példányra méretezhető. Ezt a Kubernetes autoscaleer (horizontális Pod autoskálázás) egyéni metrikáinak kiosztásával teszi meg.  A functions-tárolók és a KEDA lehetővé teszi a kiszolgáló nélküli függvények replikálását bármely Kubernetes-fürtön.  Ezek a függvények az [Azure Kubernetes Services (ak) virtuális csomópontok](../aks/virtual-nodes-cli.md) szolgáltatásával is üzembe helyezhetők a kiszolgáló nélküli infrastruktúra számára.

## <a name="managing-keda-and-functions-in-kubernetes"></a>KEDA és függvények kezelése a Kubernetes-ben

A függvények a Kubernetes-fürtön való futtatásához telepítenie kell a KEDA összetevőt. Ezt az összetevőt [Azure functions Core Tools](functions-run-local.md)használatával telepítheti.

### <a name="installing-with-the-azure-functions-core-tools"></a>Telepítés a Azure Functions Core Tools

Alapértelmezés szerint a Core Tools a KEDA és az Osiris összetevőket is telepíti, amelyek támogatják az eseményvezérelt és a HTTP-méretezést.  A telepítés `kubectl` az aktuális környezetben fut.

Telepítse a KEDA a fürtben a következő telepítési parancs futtatásával:

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Function-alkalmazás üzembe helyezése a Kubernetes

A KEDA-t futtató Kubernetes-fürtökön bármely Function alkalmazást üzembe helyezhet.  Mivel a függvények Docker-tárolóban futnak, a projektnek `Dockerfile`ra van szüksége.  Ha még nem rendelkezik ilyennel, hozzáadhat egy Docker az alábbi parancs futtatásával a functions projekt gyökerében:

```cli
func init --docker-only
```

A következő parancs futtatásával hozzon létre egy rendszerképet, és telepítse a függvényeket a Kubernetes:

> [!NOTE]
> A központi eszközök kihasználják a Docker CLI-t a lemezkép összeállításához és közzétételéhez. Győződjön meg arról, hogy a Docker már telepítve van, és `docker login`a fiókjához csatlakozik.

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Cserélje le a `<name-of-function-deployment>`t a Function alkalmazás nevére.

Ez létrehoz egy Kubernetes `Deployment` erőforrást, egy `ScaledObject` erőforrást és `Secrets`, amely tartalmazza az `local.settings.json` fájlból importált környezeti változókat.

### <a name="deploying-a-function-app-from-a-private-registry"></a>Function-alkalmazás üzembe helyezése privát beállításjegyzékből

A fenti folyamat privát kibocsátásiegység-forgalmi jegyzékekhez is használható.  Ha a tároló képét privát beállításjegyzékből húzza át, akkor adja meg a `--pull-secret` jelzőt, amely a titkos beállításjegyzék hitelesítő adatait tartalmazó Kubernetes hivatkozik `func kubernetes deploy`futtatásakor.

## <a name="removing-a-function-app-from-kubernetes"></a>Function-alkalmazás eltávolítása a Kubernetes-ből

A telepítés után eltávolíthatja a függvényt, ha eltávolítja a társított `Deployment`, `ScaledObject`, `Secrets` létrehozva.

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

A KEDA a következő Azure Function triggereket támogatja:

* [Azure Storage-üzenetsorok](functions-bindings-storage-queue.md)
* [Azure Service Bus várólisták](functions-bindings-service-bus.md)
* [Azure Event/IoT hubok](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [RabbitMQ-várólista](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>HTTP-trigger támogatása

Használhatja a HTTP-eseményindítókat közzétevő Azure Functionsokat, de a KEDA nem kezeli közvetlenül.  A Azure Functions Core Tools egy kapcsolódó projektet telepít, amely lehetővé teszi a HTTP-végpontok 0 és 1 közötti skálázását.  Az 1 és *n* közötti skálázás a hagyományos Kubernetes skálázási szabályzatokra támaszkodik.

## <a name="next-steps"></a>Következő lépések
További információkért lásd a következőket:

* [Függvény létrehozása egyéni rendszerkép használatával](functions-create-function-linux-custom-image.md)
* [Az Azure Functions helyi kódolása és tesztelése](functions-develop-local.md)
* [Az Azure Function fogyasztási terv működése](functions-scale.md)