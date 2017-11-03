---
title: "A figyelő Azure Kubernetes fürt - Sysdig |} Microsoft Docs"
description: "Figyelési Kubernetes Sysdig használata az Azure Tárolószolgáltatás-fürt"
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 283cae7031fd275fc1f355e25f01206d143756fe
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2017
---
# <a name="monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>Egy Azure-tároló szolgáltatás Kubernetes fürtjéhez Sysdig figyelése

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató feltételezi, hogy rendelkezik [a Kubernetes Azure Tárolószolgáltatási fürt létrehozott](container-service-kubernetes-walkthrough.md).

Azt is feltételezi, hogy rendelkezik-e az azure cli és kubectl eszközök vannak telepítve.

Ha tesztelheti a `az` eszköz futtatásával telepítve:

```console
$ az --version
```

Ha nem rendelkezik a `az` eszköz telepítve, az e-mail utasításokat is [Itt](https://github.com/azure/azure-cli#installation).

Ha tesztelheti a `kubectl` eszköz futtatásával telepítve:

```console
$ kubectl version
```

Ha nem rendelkezik `kubectl` telepítve, futtatható:

```console
$ az acs kubernetes install-cli
```

## <a name="sysdig"></a>Sysdig
Sysdig egy külső "figyelés" a szolgáltatás vállalat, amely képes figyelni az Azure-beli Kubernetes fürt tárolók. Aktív Sysdig fiók Sysdig a használatához.
Iratkozzon fel a fiókot saját [hely](https://app.sysdigcloud.com).

Miután bejelentkezett a Sysdig felhő webhelyére, kattintson a felhasználónevére, és az oldalon meg kell jelennie a hívóbetűjének („Access Key”). 

![Sysdig API-kulcs](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>A Kubernetes a Sysdig ügynökök telepítése
A tárolók figyeléséhez Sysdig minden gép, egy Kubernetes használatával futtat egy folyamatot `DaemonSet`.
DaemonSets olyan gépenként tárolója egyetlen példányát futtató Kubernetes API-objektumok.
Fontosságúak tökéletes megoldás az eszközök, például a Sysdig figyelési ügynök telepítése.

A Sysdig daemonset telepítéséhez kell előbb az [a sablon](https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml) a sysdig. Mentse a fájlt `sysdig-daemonset.yaml`.

Linux-és OS X futtathatja:

```console
$ curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

A PowerShell:

```console
$ Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

A hozzáférési kulcsot, Sysdig fiókjából beszerzett fájl mellett szerkesztése.

Végezetül hozza létre a DaemonSet:

```console
$ kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>A figyelést megtekintése
Miután telepített és futó, az ügynökök adatokat vissza Sysdig kell szivattyú.  Lépjen vissza a [sysdig irányítópult](https://app.sysdigcloud.com) és megjelenítheti a tárolók kapcsolatos információkat.

Kubernetes-specifikus irányítópultok keresztül is telepíthet a [új irányítópult varázsló](https://app.sysdigcloud.com/#/dashboards/new).
