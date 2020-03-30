---
title: (ELAVULT) Az Azure Kubernetes-fürt monitorozása – Sysdig
description: Kubernetes-fürt figyelése az Azure Container Service szolgáltatásban a Sysdig használatával
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 68136d5b9ec16c822cb62e4fee85b8ace9b1899a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371099"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>(ELAVULT) Azure Container Service Kubernetes-fürt figyelése a Sysdig használatával

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez a forgatókönyv feltételezi, hogy [létrehozott egy Kubernetes-fürtöt az Azure Container Service használatával.](container-service-kubernetes-walkthrough.md)

Azt is feltételezi, hogy az azure cli és kubectl eszközök telepítve.

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

## <a name="sysdig"></a>Sysdig
A Sysdig egy külső figyelés, amely szolgáltató vállalatként figyelheti az Azure-ban futó Kubernetes-fürt tárolóit. A Sysdig használatához aktív Sysdig-fiók szükséges.
Akkor iratkozzon fel egy fiókot a [saját honlapján](https://app.sysdigcloud.com).

Miután bejelentkezett a Sysdig felhő webhelyére, kattintson a felhasználónevére, és az oldalon meg kell jelennie a hívóbetűjének („Access Key”). 

![Sysdig API-kulcs](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>A Sysdig ügynökök telepítése a Kubernetes-re
A tárolók figyeléséhez a Sysdig minden gépen futtat `DaemonSet`egy folyamatot egy Kubernetes használatával.
A DaemonSets olyan Kubernetes API-objektumok, amelyek gépenként egy tároló egyetlen példányát futtatják.
Tökéletesek olyan eszközök telepítéséhez, mint a Sysdig felügyeleti ügynöke.

A Sysdig démon beállítás ának telepítéséhez először töltse le [a sablont a](https://github.com/draios/sysdig-cloud-scripts/tree/master/agent_deploy/kubernetes) sysdig-ből. Mentse a `sysdig-daemonset.yaml`fájlt .

Linux on és OS X futtatható:

```console
curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

A PowerShellben:

```powershell
Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Ezután szerkesztheti azt a fájlt, hogy beszúrja a Sysdig-fiókjából beszerzett hozzáférési kulcsot.

Végül hozza létre a DaemonSet:

```console
kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>A figyelés megtekintése
A telepítés és a működés után az ügynököknek vissza kell szivattyúzniuk az adatokat a Sysdig-be.  Menjen vissza a [sysdig irányítópultra,](https://app.sysdigcloud.com) és a tárolókkal kapcsolatos információkat kell látnia.

A Kubernetes-specifikus irányítópultokat az [új irányítópult](https://app.sysdigcloud.com/#/dashboards/new)varázslóval is telepítheti.
