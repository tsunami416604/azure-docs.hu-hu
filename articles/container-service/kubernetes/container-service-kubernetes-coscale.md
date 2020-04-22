---
title: (ELAVULT) Azure Kubernetes-fürt figyelése a CoScale-tel
description: Kubernetes-fürt figyelése az Azure Container Service szolgáltatásban a CoScale használatával
author: fryckbos
ms.service: container-service
ms.topic: conceptual
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: f195a5c05c6c95dac898b2d471747952a3446d52
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681718"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>(ELAVULT) Azure Container Service Kubernetes-fürt figyelése a CoScale-tel

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Ebben a cikkben bemutatjuk, hogyan telepítheti a [CoScale-ügynököt](https://web.archive.org/web/20180317071550/https://www.coscale.com/) a Kubernetes-fürtben lévő összes csomópont és tároló figyeléséhez az Azure Container Service-ben. Ehhez a konfigurációhoz a CoScale-fiókra van szükség. 


## <a name="about-coscale"></a>A CoScale-ről 

A CoScale egy figyelési platform, amely több vezénylési platform összes tárolójából gyűjti össze a metrikákat és eseményeket. A CoScale teljes veremfigyelést biztosít a Kubernetes-környezetekben. Vizualizációkat és elemzéseket biztosít a verem összes rétegéhez: az operációs rendszerhez, a Kuberneteshez, a Dockerhez és a tárolókban futó alkalmazásokhoz. A CoScale számos beépített figyelési irányítópultot kínál, és beépített anomáliaészleléssel rendelkezik, hogy az operátorok és a fejlesztők gyorsan megtalálják az infrastruktúrával és az alkalmazásokkal kapcsolatos problémákat.

![Közös skálázású felhasználói felület](./media/container-service-kubernetes-coscale/coscale.png)

Ahogy ebben a cikkben látható, ügynököket telepíthet egy Kubernetes-fürtre a CoScale SaaS-megoldásként való futtatásához. Ha az adatokat a helyszínen szeretné tartani, a CoScale helyszíni telepítéshez is elérhető.


## <a name="prerequisites"></a>Előfeltételek

Először létre kell [hoznia egy CoScale-fiókot.](https://web.archive.org/web/20170507123133/https://www.coscale.com/free-trial)

Ez a forgatókönyv feltételezi, hogy [létrehozott egy Kubernetes-fürtöt az Azure Container Service használatával.](container-service-kubernetes-walkthrough.md)

Azt is feltételezi, hogy `az` az Azure `kubectl` CLI és az eszközök telepítve van.

Tesztelheti, hogy telepítve `az` van-e az eszköz a következő futtatásával:

```azurecli
az --version
```

Ha nincs telepítve az `az` eszköz, [itt](/cli/azure/install-azure-cli)utasítások találhatók.

Tesztelheti, hogy telepítve `kubectl` van-e az eszköz a következő futtatásával:

```bash
kubectl version
```

Ha nincs `kubectl` telepítve, futtathatja a következőket:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>A CoScale-ügynök telepítése DaemonSet segítségével
[A DaemonSets-et](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) a Kubernetes arra használja, hogy a fürt minden állomásán egyetlen példányt futtasson.
Tökéletesek a felügyeleti ügynökök, például a CoScale-ügynök futtatásához.

Miután bejelentkezett a CoScale-be, lépjen az [ügynök lapra,](https://developer.newrelic.com/) és telepítse a CoScale-ügynököket a fürtön egy DaemonSet használatával. A CoScale felhasználói felület e vezérelt konfigurációs lépéseket biztosít egy ügynök létrehozásához és a teljes Kubernetes-fürt figyeléséhez.

![CoScale-ügynök konfigurációja](./media/container-service-kubernetes-coscale/installation.png)

Az ügynök fürtön való indításához futtassa a mellékelt parancsot:

![A CoScale-ügynök indítása](./media/container-service-kubernetes-coscale/agent_script.png)

Ennyi az egész! Miután az ügynökök már működik, meg kell jelennie az adatokat a konzolon néhány percen belül. Látogasson el az [ügynök lapra](https://developer.newrelic.com/) a fürt összegzésének megtekintéséhez, hajtson végre további konfigurációs lépéseket, és tekintse meg az irányítópultokat, például a **Kubernetes-fürt áttekintését.**

![Kubernetes-fürt – áttekintés](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

A CoScale-ügynök automatikusan üzembe helyezése a fürt új gépeken. Az ügynök automatikusan frissül, amikor új verzió jelenik meg.


## <a name="next-steps"></a>További lépések

A CoScale figyelési megoldásairól a [CoScale dokumentációjában](https://web.archive.org/web/20180415164304/http://docs.coscale.com:80/) és [blogjában](https://web.archive.org/web/20170501021344/http://www.coscale.com:80/blog) olvashat bővebben. 

