---
title: "A figyelő Azure Kubernetes fürt Datadog"
description: "Figyelési Kubernetes Datadog használata az Azure Tárolószolgáltatás-fürt"
services: container-service
author: bburns
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 96bbd88f163939b58263f2f3a94b7b4d90f85736
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
---
# <a name="monitor-an-azure-container-service-cluster-with-datadog"></a>A figyelő egy DataDog az Azure Tárolószolgáltatás-fürt

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató feltételezi, hogy rendelkezik [a Kubernetes Azure Tárolószolgáltatási fürt létrehozott](container-service-kubernetes-walkthrough.md).

Azt is feltételezi, hogy a `az` Azure cli és `kubectl` eszközök vannak telepítve.

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

## <a name="datadog"></a>DataDog
Datadog egy figyelési szolgáltatás, amely a figyelési adatokat gyűjt a tárolók skálázása az Azure Tárolószolgáltatás-fürt belül. Datadog rendelkezik egy Docker integrációs irányítópultot, ahol láthatja adott mérőszámok belül a tárolók. A tárolók gyűjtött metrikák Processzor, memória, hálózati és i/o szerint vannak rendszerezve. Datadog metrikák felosztja a tárolók és a képeket.

Először [-fiók létrehozása](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>Egy DaemonSet a Datadog ügynök telepítése
A tároló egyetlen példány futhat az a fürt minden egyes állomás DaemonSets Kubernetes használják.
Fontosságúak tökéletes figyelőügynökök futtatásához.

Miután a Datadog jelentkezett, a [Datadog utasításokat](https://app.datadoghq.com/account/settings#agent/kubernetes) Datadog ügynökök telepítése a fürt egy DaemonSet használatával.

## <a name="conclusion"></a>Összegzés
Ennyi az egész! Miután az ügynököket működik, és meg kell jelennie a konzol adatainak néhány perc múlva. Az integrált ellátogathat [kubernetes irányítópult](https://app.datadoghq.com/screen/integration/kubernetes) a fürt összegzését.
