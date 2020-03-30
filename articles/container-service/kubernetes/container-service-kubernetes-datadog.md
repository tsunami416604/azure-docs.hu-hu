---
title: (ELAVULT) Az Azure Kubernetes-fürt figyelése datadogmal
description: Kubernetes-fürt figyelése az Azure Container Service szolgáltatásban a Datadog használatával
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 1f3f70c30ab397bd549a2f3305a738274ee4f64f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371171"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-datadog"></a>(ELAVULT) Azure Container Service-fürt figyelése a DataDog segítségével

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez a forgatókönyv feltételezi, hogy [létrehozott egy Kubernetes-fürtöt az Azure Container Service használatával.](container-service-kubernetes-walkthrough.md)

Azt is feltételezi, hogy `az` az Azure `kubectl` cli és eszközök telepítve van.

Tesztelheti, hogy telepítve `az` van-e az eszköz a következő futtatásával:

```azurecli
az --version
```

Ha nincs telepítve az `az` eszköz, [itt](https://github.com/azure/azure-cli#installation)utasítások találhatók.

Tesztelheti, hogy telepítve `kubectl` van-e az eszköz a következő futtatásával:

```console
kubectl version
```

Ha nincs `kubectl` telepítve, futtathatja a következőket:

```azurecli
az acs kubernetes install-cli
```

## <a name="datadog"></a>DataDog (Adatkutya)
A Datadog egy figyelési szolgáltatás, amely az Azure Container Service-fürtön belüli tárolókból gyűjti a figyelési adatokat. Datadog rendelkezik egy Docker-integrációs irányítópult, ahol megtekintheti a tárolókon belül adott metrikák. A tárolókból összegyűjtött mérőszámok cpu, memória, hálózat és i/o szerint vannak rendezve. Datadog osztja metrikák tárolók és képek.

Először létre kell [hoznia egy fiókot](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>A Datadog Agent telepítése egy DaemonSet
A DaemonSets-et a Kubernetes arra használja, hogy a fürt minden állomásán egyetlen példányt futtasson.
Tökéletesek a megfigyelő ügynökök működtetéséhez.

Miután bejelentkezett datadog, kövesse a [Datadog utasításokat](https://app.datadoghq.com/account/settings#agent/kubernetes) telepíteni Datadog ügynökök a fürtön egy DaemonSet használatával.

## <a name="conclusion"></a>Összegzés
Ennyi az egész! Miután az ügynökök már működik, látnia kell az adatokat a konzolon néhány percen belül. Látogasson el az integrált [kubernetes irányítópultra](https://app.datadoghq.com/screen/integration/kubernetes) a fürt összegzésének megtekintéséhez.
