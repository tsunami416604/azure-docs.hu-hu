---
title: ELAVULT Az Azure Kubernetes-fürt figyelése a Datadoggal
description: Kubernetes-fürt figyelése Azure Container Service a Datadoggal használatával
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 1f3f70c30ab397bd549a2f3305a738274ee4f64f
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371171"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-datadog"></a>ELAVULT Azure Container Service-fürt figyelése a Datadoggal

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez a bemutató azt feltételezi, hogy [Azure Container Service használatával hozott létre egy Kubernetes-fürtöt](container-service-kubernetes-walkthrough.md).

Azt is feltételezi, hogy telepítve van a `az` Azure CLI és `kubectl` eszközök.

A futtatásával tesztelheti, hogy telepítve van-e a `az` eszköz:

```azurecli
az --version
```

Ha nincs telepítve a `az` eszköz, [itt](https://github.com/azure/azure-cli#installation)talál útmutatást.

A futtatásával tesztelheti, hogy telepítve van-e a `kubectl` eszköz:

```console
kubectl version
```

Ha nincs `kubectl` telepítve, akkor a következőket futtathatja:

```azurecli
az acs kubernetes install-cli
```

## <a name="datadog"></a>DataDog
A datadoggal egy figyelési szolgáltatás, amely a Azure Container Service-fürtön belüli tárolók figyelési adatait gyűjti. A datadoggal rendelkezik egy Docker-integrációs irányítópulttal, ahol megtekintheti a tárolókban található konkrét mérőszámokat. A tárolók által gyűjtött mérőszámokat a CPU, a memória, a hálózat és az I/O rendezi. A datadoggal tárolók és képek számára osztja szét a metrikákat.

Először [létre kell hoznia egy fiókot](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>A Datadoggal-ügynök telepítése Daemonset elemet
A Kubernetes a DaemonSets használja a tároló egyetlen példányának futtatására a fürt minden gazdagépén.
Tökéletesek a figyelési ügynökök futtatásához.

Miután bejelentkezett a Datadoggal-be, kövesse a [datadoggal utasításokat](https://app.datadoghq.com/account/settings#agent/kubernetes) a datadoggal-ügynökök fürtön való telepítéséhez daemonset elemet használatával.

## <a name="conclusion"></a>Összegzés
Ennyi az egész! Az ügynökök üzembe helyezése után néhány percen belül meg kell jelennie az adatnak a-konzolon. Az integrált [kubernetes irányítópultján](https://app.datadoghq.com/screen/integration/kubernetes) megtekintheti a fürt összegzését.
