---
title: Egy Azure Kubernetes fürt CoScale figyelése
description: Az Azure Tárolószolgáltatásban CoScale használatával Kubernetes fürt figyelése
services: container-service
author: fryckbos
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 16580307193bbb7eb9b401eb1b14356e8589d6e2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32162789"
---
# <a name="monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>Egy Azure-tároló szolgáltatás Kubernetes fürt CoScale figyelése

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Ebben a cikkben azt megmutatja, hogyan telepítheti a [CoScale](https://www.coscale.com/) agent figyelje az összes csomópontot és az Azure Tárolószolgáltatásban Kubernetes fürt a tárolók. Ez a konfiguráció CoScale rendelkező fiók szükséges. 


## <a name="about-coscale"></a>CoScale kapcsolatos 

CoScale egy felügyeleti platform, metrikákkal és eseményekkel gyűjt az összes tároló több vezénylési platformokon. CoScale kínál a teljes verem Kubernetes környezetek figyelése. Képi megjelenítések és az elemzések biztosít az összes rétegének a veremben: az operációs rendszer, Kubernetes, Docker és a tárolók belül futó alkalmazások. CoScale kínál számos beépített figyelési irányítópultok, és nem rendelkezik, ahhoz, hogy operátorok és a fejlesztők gyors található infrastruktúra és az alkalmazás számos beépített anomáliadetektálás.

![Felhasználói felület coScale](./media/container-service-kubernetes-coscale/coscale.png)

Amint ez a cikk, a ügynökök való futtatásra a Szolgáltatottszoftver-megoldás CoScale Kubernetes fürtön is telepítheti. Ha meg szeretné tartani az adatokat a helyszíni, CoScale is rendelkezésre áll a helyszíni telepítéshez.


## <a name="prerequisites"></a>Előfeltételek

Először [CoScale-fiók létrehozása](https://www.coscale.com/free-trial).

Ez az útmutató feltételezi, hogy rendelkezik [a Kubernetes Azure Tárolószolgáltatási fürt létrehozott](container-service-kubernetes-walkthrough.md).

Azt is feltételezi, hogy a `az` Azure CLI és `kubectl` eszközök vannak telepítve.

Ha tesztelheti a `az` eszköz futtatásával telepítve:

```azurecli
az --version
```

Ha nem rendelkezik a `az` eszköz telepítve, az e-mail utasításokat is [Itt](/cli/azure/install-azure-cli).

Ha tesztelheti a `kubectl` eszköz futtatásával telepítve:

```bash
kubectl version
```

Ha nem rendelkezik `kubectl` telepítve, futtatható:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>Egy DaemonSet a CoScale ügynök telepítése
[DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) a tároló egyetlen példány futhat az a fürt minden egyes állomás Kubernetes használják.
Fontosságúak tökéletes választás, például a CoScale ügynök figyelőügynökök futtatásához.

Miután CoScale jelentkezik be, lépjen a [lap](https://app.coscale.com/) CoScale ügynökök telepítése a fürt egy DaemonSet használatával. A CoScale felhasználói felület lépéseit az interaktív konfigurációs hozzon létre egy ügynök és a teljes Kubernetes fürt figyelni.

![CoScale ügynök konfigurálása](./media/container-service-kubernetes-coscale/installation.png)

A megadott parancsot az ügynököt a fürt elindításához:

![Indítsa el a CoScale ügynök](./media/container-service-kubernetes-coscale/agent_script.png)

Ennyi az egész! Ha az ügynök működik és elérhető, meg kell jelennie a konzol adatainak néhány perc múlva. Látogasson el a [lap](https://app.coscale.com/) tekintse meg a fürt összegzését, további konfigurálásra, és tekintse meg az irányítópultok, mint a **Kubernetes fürt áttekintése**.

![Kubernetes fürtök – áttekintés](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

A CoScale ügynök automatikusan új gépek a fürt központi telepítése. Az ügynök a frissítések automatikus verziójának kiadásakor.


## <a name="next-steps"></a>További lépések

Tekintse meg a [dokumentáció CoScale](http://docs.coscale.com/) és [blog](https://www.coscale.com/blog) figyelési megoldásoknak CoScale további információt. 

