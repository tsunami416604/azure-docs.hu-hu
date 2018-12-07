---
title: (ELAVULT) Azure-beli Kubernetes-fürt – Sysdig figyelése
description: Az Azure Container Service a Sysdig segítségével Kubernetes-fürt figyelése
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 4aef241e2c86e4016c3c468fcdcfdfc620fc7aa9
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998733"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>(ELAVULT) Egy Azure Container Service Kubernetes-fürtön Sysdig figyelése

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Előfeltételek
Az útmutató feltételezi, hogy [egy Kubernetes-fürtöt az Azure Container Service használatával létrehozott](container-service-kubernetes-walkthrough.md).

Azt is feltételezi, hogy a telepített azure CLI-t és a kubectl eszközök.

Ha rendelkezik tesztelheti a `az` futtatásával telepített eszköz:

```console
$ az --version
```

Ha nem rendelkezik a `az` eszközt telepítette, az e-mail utasításokat is [Itt](https://github.com/azure/azure-cli#installation).

Ha rendelkezik tesztelheti a `kubectl` futtatásával telepített eszköz:

```console
$ kubectl version
```

Ha nem rendelkezik `kubectl` telepítve, futtatható:

```console
$ az acs kubernetes install-cli
```

## <a name="sysdig"></a>Sysdig
A Sysdig egy-egy szolgáltatás vállalat, amely képes figyelni a tárolók az Azure-ban futtatott Kubernetes-fürt külső monitorozásra. A Sysdig segítségével egy aktív Sysdig-fiókra van szükség.
Iratkozzon fel a fiók saját [hely](https://app.sysdigcloud.com).

Miután bejelentkezett a Sysdig felhő webhelyére, kattintson a felhasználónevére, és az oldalon meg kell jelennie a hívóbetűjének („Access Key”). 

![Sysdig API-kulcs](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>A Sysdig-ügynökök telepítése kubernetes
Tárolók monitorozásához Sysdig egy folyamat fut az összes olyan számítógépen, a Kubernetes használatával `DaemonSet`.
DaemonSets objektumai Kubernetes API-t, amely egy tároló gépenként egyetlen példánya.
Azok a tökéletes olyan eszközökkel, mint például a Sysdig monitorozási ügynök telepítése.

A Sysdig daemonset telepítéséhez meg kell töltenie [a sablon](https://github.com/draios/sysdig-cloud-scripts/tree/master/agent_deploy/kubernetes) a sysdig segítségével. Mentse a fájlt az `sysdig-daemonset.yaml`.

Linux- és OS X rendszeren futtathatja:

```console
$ curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

A PowerShellben:

```console
$ Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Ezután módosítsa ezt a fájlt a hozzáférési kulcsot, a Sysdig-fiókjából beszerzett.

Végül hozza létre a DaemonSet:

```console
$ kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>A figyelés megtekintése
Miután telepített és futó, az ügynökök adatokat küld vissza a sysdig segítségével kell pump.  Lépjen vissza a [sysdig irányítópult](https://app.sysdigcloud.com) és a tárolókkal kapcsolatos információkat kell látnunk.

Kubernetes-specifikus irányítópultokat is telepítheti a [új irányítópult varázsló](https://app.sysdigcloud.com/#/dashboards/new).
