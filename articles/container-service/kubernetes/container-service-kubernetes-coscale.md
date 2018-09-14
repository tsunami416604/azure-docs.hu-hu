---
title: Coscale-Azure-beli Kubernetes-fürt megfigyelése
description: Az Azure Container Service használatával CoScale Kubernetes-fürt monitorozása
services: container-service
author: fryckbos
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: e9896a34e835646e17328482c07d8031c624e858
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2018
ms.locfileid: "45544030"
---
# <a name="monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>Coscale-Azure Container Service Kubernetes-fürt megfigyelése

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Ebben a cikkben bemutatjuk, hogyan helyezhet üzembe a [CoScale](https://www.coscale.com/) ügynök minden csomópontján és tárolók az Azure Container Service-ben a Kubernetes-fürt monitorozásához. Ez a konfiguráció coscale-fiók szükséges. 


## <a name="about-coscale"></a>CoScale kapcsolatban 

CoScale a megfigyelő platformnak, metrikákkal és eseményekkel gyűjt több vezénylési platformokon az összes tárolót. CoScale kínál a teljes veremre kiterjedő figyelést Kubernetes-környezetekből. A veremben lévő összes rétegének adatmegjelenítési és elemzési biztosítja: az operációs rendszer, Kubernetes, Docker és a tárolókban futó alkalmazások. CoScale kínál számos beépített figyelési irányítópultokat, és rendelkezik beépített anomáliadetektálást, hogy a kezelők és fejlesztők infrastruktúráját és alkalmazási problémák gyors keresése.

![Felhasználói felület coScale](./media/container-service-kubernetes-coscale/coscale.png)

Ebben a cikkben látható, a Kubernetes-fürtön történő futtatásra a Szolgáltatottszoftver-megoldás CoScale ügynökök is telepítheti. Ha meg szeretné tartani az adatokat a helyszíni, CoScale érhető el a helyszíni telepítéshez.


## <a name="prerequisites"></a>Előfeltételek

Először létre kell [CoScale-fiók létrehozása](https://www.coscale.com/free-trial).

Az útmutató feltételezi, hogy [egy Kubernetes-fürtöt az Azure Container Service használatával létrehozott](container-service-kubernetes-walkthrough.md).

Azt is feltételezi, hogy a `az` Azure CLI-vel és `kubectl` telepített eszközök.

Ha rendelkezik tesztelheti a `az` futtatásával telepített eszköz:

```azurecli
az --version
```

Ha nem rendelkezik a `az` eszközt telepítette, az e-mail utasításokat is [Itt](/cli/azure/install-azure-cli).

Ha rendelkezik tesztelheti a `kubectl` futtatásával telepített eszköz:

```bash
kubectl version
```

Ha nem rendelkezik `kubectl` telepítve, futtatható:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>A DaemonSet a az CoScale-ügynök telepítése
[DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) egy tárolót egyetlen példányát futtatni a fürt minden gazdagépen Kubernetes használják.
Azok a futó monitorozási ügynökök, például a CoScale-ügynök tökéletes.

CoScale való bejelentkezés után nyissa meg a [ügynök lapján](https://app.coscale.com/) CoScale-ügynök telepítéséhez a saját fürtjében, egy DaemonSet. A CoScale felhasználói felületén hozzon létre egy ügynököt, és megkezdheti a figyelést a teljes Kubernetes-fürt részletes konfigurációs lépéseit ismerteti.

![Az ügynök konfigurációs coScale](./media/container-service-kubernetes-coscale/installation.png)

Az ügynök a fürt elindításához futtassa a megadott parancsot:

![Indítsa el a CoScale-ügynök](./media/container-service-kubernetes-coscale/agent_script.png)

Ennyi az egész! Miután az ügynökök üzembe helyezéséig, megtekintheti a konzol adatainak néhány perc múlva. Látogasson el a [ügynök lapján](https://app.coscale.com/) tekintse meg a fürt összegzését, további konfigurációs lépésekre, és tekintse meg például az irányítópultok a **Kubernetes-fürt áttekintés**.

![Kubernetes-fürt – áttekintés](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

A CoScale-ügynök automatikusan telepíti a fürtöt az új gépeken. Az ügynök frissítések automatikusan, amikor egy új verziója.


## <a name="next-steps"></a>További lépések

Tekintse meg a [dokumentáció CoScale](http://docs.coscale.com/) és [blog](https://www.coscale.com/blog) CoScale figyelési megoldások további információt. 

