---
title: Azure-függvények a Kubernetes en a KEDA-val
description: Ismerje meg, hogyan futtathatja az Azure Functions-t a Kubernetes-ben a felhőben vagy a helyszíni KEDA, Kubernetes-alapú automatikus skálázás használatával.
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 2c06fdba8f60243acf4e0fabd23df8b832c210db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301675"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Azure-függvények a Kubernetes en a KEDA-val

Az Azure Functions futásidejű rugalmasságot biztosít a tárhely, ahol és hogyan szeretné.  [KEDA](https://keda.sh) (Kubernetes-alapú eseményvezérelt automatikus skálázás) zökkenőmentesen párosítja az Azure Functions futásidejű és eszköz, hogy eseményvezérelt skálát Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>A Kubernetes-alapú függvények működésének megmunkálata

Az Azure Functions szolgáltatás két fő összetevőből áll: egy futásidejű és egy méretezési vezérlőből.  A Függvények futásidejű fut, és végrehajtja a kódot.  A futásidejű logika, hogyan kiváltó, naplóés és a függvény-végrehajtások kezelése.  Az Azure Functions futásideje *bárhol*futtatható.  A másik összetevő egy méretezési vezérlő.  A méretezési vezérlő figyeli a függvényt célzó események sebességét, és proaktív módon méretezi az alkalmazást futtató példányok számát.  További információ: [Azure Functions scale and hosting](functions-scale.md).

Kubernetes-alapú függvények biztosítanak a Functions futásidejű egy [Docker-tároló](functions-create-function-linux-custom-image.md) eseményvezérelt skálázás a KEDA-n keresztül.  A KEDA 0 példányra skálázható (ha nem történik esemény), és *n* példányokra. Ezt úgy éri el, hogy a Kubernetes automatikus skálázó (Vízszintes pod automatikus skálázó) egyéni metrikákat tesz ki.  A Függvénytárolók KEDA-val való használata lehetővé teszi a kiszolgáló nélküli függvényképességek replikálását bármely Kubernetes-fürtben.  Ezek a függvények is [telepíthetők az Azure Kubernetes Services (AKS) virtuális csomópontok](../aks/virtual-nodes-cli.md) szolgáltatás kiszolgáló nélküli infrastruktúra használatával.

## <a name="managing-keda-and-functions-in-kubernetes"></a>A KEDA és a Kubernetes funkcióinak kezelése

A Kubernetes-fürt függvényeinek futtatásához telepítenie kell a KEDA összetevőt. Ezt az összetevőt az [Azure Functions Core Tools](functions-run-local.md)eszközzel telepítheti.

### <a name="installing-with-helm"></a>Telepítés a Helm-el

A KEDA telepítése számos kubernetes-fürtben, beleértve a Helm-et is, számos módon telepíthető.  A telepítési lehetőségek a [KEDA-webhelyen](https://keda.sh/deploy/)vannak dokumentálva.

## <a name="deploying-a-function-app-to-kubernetes"></a>Függvényalkalmazás telepítése a Kubernetes-re

Bármilyen függvényalkalmazást telepíthet egy KEDA-t futtató Kubernetes-fürtre.  Mivel a függvények egy Docker-tárolóban `Dockerfile`futnak, a projektnek szüksége van egy .  Ha még nem rendelkezik ilyentel, a Functions projekt gyökerében futtatva hozzáadhat egy Docker-fájlt:

```cli
func init --docker-only
```

Lemezkép létrehozásához és a függvények Kubernetes rendszerbe való telepítéséhez futtassa a következő parancsot:

> [!NOTE]
> A Core Tools a docker CLI-t használja a lemezkép létrehozásához és közzétételéhez. Győződjön meg arról, hogy a docker már telepítve van, és csatlakozik a fiókjához a segítségével. `docker login`

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Cserélje `<name-of-function-deployment>` le a függvényalkalmazás nevére.

Ez létrehoz egy `Deployment` Kubernetes-erőforrást, egy erőforrást `ScaledObject` és `Secrets` `local.settings.json` a , amely a fájlból importált környezeti változókat tartalmazza.

### <a name="deploying-a-function-app-from-a-private-registry"></a>Függvényalkalmazás telepítése privát beállításjegyzékből

A fenti folyamat működik a magán nyilvántartások is.  Ha a tárolólemezképet egy privát beállításjegyzékből húzza ki, adja meg a `--pull-secret` Kubernetes `func kubernetes deploy`titkos tárolójára hivatkozó jelzőt, amely a privát rendszerleíró hitelesítő adatokat tárolja futás közben.

## <a name="removing-a-function-app-from-kubernetes"></a>Függvényalkalmazás eltávolítása a Kubernetes-ből

A telepítés után eltávolíthatja a függvényt `Deployment` `ScaledObject`a `Secrets` társított , a létrehozott lehetőség eltávolításával.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>A KEDA eltávolítása a Kubernetes-ből

A KEDA eltávolításának lépéseit [a KEDA webhelydokumentálja.](https://keda.sh/deploy/)

## <a name="supported-triggers-in-keda"></a>Támogatott eseményindítók a KEDA-ban

A KEDA támogatja a következő Azure-függvényesemény-indítókat:

* [Azure Storage-üzenetsorok](functions-bindings-storage-queue.md)
* [Az Azure Service Bus várólistái](functions-bindings-service-bus.md)
* [Azure-esemény / IoT Hubs](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [RabbitMQ-várólista](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>HTTP Trigger támogatása

Használhatja az Azure Functions, amely elérhetővé teszi a HTTP-eseményindítók, de a KEDA nem közvetlenül kezeli őket.  A KEDA prometheus eseményindítóval [1-től *n-es* példányig skálázhatja a HTTP Azure-függvényeket.](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42)

## <a name="next-steps"></a>Következő lépések
További információkért lásd a következőket:

* [Függvény létrehozása egyéni képpel](functions-create-function-linux-custom-image.md)
* [Az Azure Functions helyi kódolása és tesztelése](functions-develop-local.md)
* [Az Azure-függvényfelhasználási csomag működése](functions-scale.md)