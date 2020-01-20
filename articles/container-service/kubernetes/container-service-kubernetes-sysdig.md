---
title: ELAVULT Azure Kubernetes-fürt figyelése – Sysdig
description: Kubernetes-fürt figyelése Azure Container Service a Sysdig használatával
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 3cb9c628993201553b8da1d1bd37b4705e0f23dc
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271640"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>ELAVULT Azure Container Service Kubernetes-fürt figyelése a Sysdig használatával

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez a bemutató azt feltételezi, hogy [Azure Container Service használatával hozott létre egy Kubernetes-fürtöt](container-service-kubernetes-walkthrough.md).

Azt is feltételezi, hogy telepítette az Azure CLI-t és a kubectl-eszközöket.

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

## <a name="sysdig"></a>Sysdig
A Sysdig egy külső figyelési szolgáltatásként működő cég, amely az Azure-ban futó Kubernetes-fürtben lévő tárolók figyelésére képes. A Sysdig használatához aktív Sysdig-fiók szükséges.
Regisztrálhat egy fiókot a [webhelyén](https://app.sysdigcloud.com).

Miután bejelentkezett a Sysdig felhő webhelyére, kattintson a felhasználónevére, és az oldalon meg kell jelennie a hívóbetűjének („Access Key”). 

![Sysdig API-kulcs](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>A Sysdig-ügynökök telepítése a Kubernetes-be
A tárolók figyeléséhez a Sysdig egy Kubernetes-`DaemonSet`használó folyamatot futtat minden gépen.
A DaemonSets olyan Kubernetes API-objektumok, amelyek gépenként egy tároló egyetlen példányát futtatják.
Tökéletesek az olyan eszközök telepítéséhez, mint például a Sysdig figyelési ügynöke.

A Sysdig-daemonset elemet telepítéséhez először le kell töltenie [a sablont](https://github.com/draios/sysdig-cloud-scripts/tree/master/agent_deploy/kubernetes) a Sysdig-ből. Mentse a fájlt `sysdig-daemonset.yaml`ként.

Linux és OS X rendszeren futtathatja a következőt:

```console
$ curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

A PowerShellben:

```console
$ Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Ezután szerkessze a fájlt a Sysdig-fiókjából beszerzett hozzáférési kulcs beszúrásához.

Végül hozza létre a Daemonset elemet:

```console
$ kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>Figyelés megtekintése
A telepítése és futtatása után az ügynököknek vissza kell Sysdig az adatgyűjtést.  Lépjen vissza a [sysdig-irányítópultra](https://app.sysdigcloud.com) , és tekintse meg a tárolókkal kapcsolatos információkat.

A Kubernetes-specifikus irányítópultokat az [új irányítópult varázsló](https://app.sysdigcloud.com/#/dashboards/new)segítségével is telepítheti.
