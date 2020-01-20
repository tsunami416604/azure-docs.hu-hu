---
title: ELAVULT Az Azure Kubernetes-fürt figyelése a Datadoggal
description: Kubernetes-fürt figyelése Azure Container Service a Datadoggal használatával
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: c8ed146a224ec4225a7a0e85c76227fb1dc71b0b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271052"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-datadog"></a>ELAVULT Azure Container Service-fürt figyelése a Datadoggal

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez a bemutató azt feltételezi, hogy [Azure Container Service használatával hozott létre egy Kubernetes-fürtöt](container-service-kubernetes-walkthrough.md).

Azt is feltételezi, hogy telepítve van a `az` Azure CLI és `kubectl` eszközök.

A futtatásával tesztelheti, hogy telepítve van-e a `az` eszköz:

```console
$ az --version
```

Ha nincs telepítve a `az` eszköz, [itt](https://github.com/azure/azure-cli#installation)talál útmutatást.

A futtatásával tesztelheti, hogy telepítve van-e a `kubectl` eszköz:

```console
$ kubectl version
```

Ha nincs `kubectl` telepítve, akkor a következőket futtathatja:

```console
$ az acs kubernetes install-cli
```

## <a name="datadog"></a>Datadoggal
A datadoggal egy figyelési szolgáltatás, amely a Azure Container Service-fürtön belüli tárolók figyelési adatait gyűjti. A datadoggal rendelkezik egy Docker-integrációs irányítópulttal, ahol megtekintheti a tárolókban található konkrét mérőszámokat. A tárolók által gyűjtött mérőszámokat a CPU, a memória, a hálózat és az I/O rendezi. A datadoggal tárolók és képek számára osztja szét a metrikákat.

Először [létre kell hoznia egy fiókot](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>A Datadoggal-ügynök telepítése Daemonset elemet
A Kubernetes a DaemonSets használja a tároló egyetlen példányának futtatására a fürt minden gazdagépén.
Tökéletesek a figyelési ügynökök futtatásához.

Miután bejelentkezett a Datadoggal-be, kövesse a [datadoggal utasításokat](https://app.datadoghq.com/account/settings#agent/kubernetes) a datadoggal-ügynökök fürtön való telepítéséhez daemonset elemet használatával.

## <a name="conclusion"></a>Összegzés
Ennyi az egész! Az ügynökök üzembe helyezése után néhány percen belül meg kell jelennie az adatnak a-konzolon. Az integrált [kubernetes irányítópultján](https://app.datadoghq.com/screen/integration/kubernetes) megtekintheti a fürt összegzését.
