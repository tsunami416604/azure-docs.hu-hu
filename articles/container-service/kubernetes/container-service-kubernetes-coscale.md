---
title: ELAVULT Azure Kubernetes-fürt figyelése együttesen
description: Kubernetes-fürt figyelése Azure Container Service a méretarányos használatával
author: fryckbos
ms.service: container-service
ms.topic: conceptual
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: f1d0ca1ffc2e7a3d645ac5acbaafdf45f85550be
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271099"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>ELAVULT Azure Container Service Kubernetes-fürt figyelése együttesen

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Ebben a cikkben bemutatjuk, hogyan helyezheti üzembe [az Kubernetes](https://web.archive.org/web/20180317071550/https://www.coscale.com/) -fürtben lévő összes csomópontot és tárolót a Azure Container Serviceban. Ehhez a konfigurációhoz szükség van egy olyan fiókra, amely együttesen méretezhető. 


## <a name="about-coscale"></a>A méretarány 

A méretarány egy figyelési platform, amely az összes tárolóból származó mérőszámokat és eseményeket gyűjti össze számos összehangoló platformon. A Kubernetes-környezetek teljes körű figyelését a skála biztosítja. Vizualizációkat és elemzéseket biztosít a veremben lévő összes réteghez: az operációs rendszer, a Kubernetes, a Docker és a tárolókban futó alkalmazások számára. A skála számos beépített monitorozási irányítópultot kínál, és beépített anomáliák észlelése lehetővé teszi, hogy a kezelők és a fejlesztők gyorsan megtalálják az infrastruktúrát és az alkalmazások problémáit.

![Többméretű felhasználói felület](./media/container-service-kubernetes-coscale/coscale.png)

Ahogy az ebben a cikkben is látható, az ügynököket telepítheti egy Kubernetes-fürtre, ha SaaS-megoldásként szeretné futtatni a méretezést. Ha a helyszínen szeretné megőrizni az adatait, a helyszíni telepítéshez is rendelkezésre áll a skálázás.


## <a name="prerequisites"></a>Előfeltételek

Először [létre kell hoznia egy méretarányos fiókot](https://web.archive.org/web/20170507123133/https://www.coscale.com/free-trial).

Ez a bemutató azt feltételezi, hogy [Azure Container Service használatával hozott létre egy Kubernetes-fürtöt](container-service-kubernetes-walkthrough.md).

Azt is feltételezi, hogy telepítve van a `az` Azure CLI és `kubectl` eszközök.

A futtatásával tesztelheti, hogy telepítve van-e a `az` eszköz:

```azurecli
az --version
```

Ha nincs telepítve a `az` eszköz, [itt](/cli/azure/install-azure-cli)talál útmutatást.

A futtatásával tesztelheti, hogy telepítve van-e a `kubectl` eszköz:

```bash
kubectl version
```

Ha nincs `kubectl` telepítve, akkor a következőket futtathatja:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>A méretarányos ügynök telepítése Daemonset elemet
A Kubernetes a [DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) használja a tároló egyetlen példányának futtatására a fürt minden gazdagépén.
Tökéletesek a figyelési ügynökök, például az egyméretű ügynök futtatásához.

Miután bejelentkezett a Méretarányba, lépjen az [ügynök lapra](https://app.coscale.com/) , és telepítse a daemonset elemet-t használó, a fürthöz tartozó méretarányos ügynököket. A méretarányos felhasználói felület interaktív konfigurációs lépéseket biztosít az ügynökök létrehozásához és a teljes Kubernetes-fürt figyelésének megkezdéséhez.

![A méretezési ügynök konfigurációja](./media/container-service-kubernetes-coscale/installation.png)

Az ügynök a fürtön való indításához futtassa a megadott parancsot:

![A méretarányos ügynök elindítása](./media/container-service-kubernetes-coscale/agent_script.png)

Ennyi az egész! Az ügynökök üzembe helyezése után néhány percen belül meg kell jelennie az adatnak a-konzolon. Az [ügynök lapon](https://app.coscale.com/) megtekintheti a fürt összegzését, további konfigurációs lépéseket hajthat végre, és megtekintheti az irányítópultok, például a **Kubernetes-fürt áttekintését**.

![A Kubernetes-fürt áttekintése](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

A rendszer a fürtben lévő új gépekre automatikusan telepíti a méretezési ügynököt. Az ügynök automatikusan frissül, amikor megjelent egy új verzió.


## <a name="next-steps"></a>Következő lépések

A többméretű figyelési megoldásokkal kapcsolatos további információkért tekintse meg a többoldalas [dokumentációt](https://web.archive.org/web/20180415164304/http://docs.coscale.com:80/) és a [blogot](https://web.archive.org/web/20170501021344/http://www.coscale.com:80/blog) . 

