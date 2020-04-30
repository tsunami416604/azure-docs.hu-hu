---
title: ELAVULT Azure Kubernetes-fürt figyelése együttesen
description: Kubernetes-fürt figyelése Azure Container Service a méretarányos használatával
author: fryckbos
ms.service: container-service
ms.topic: conceptual
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: f195a5c05c6c95dac898b2d471747952a3446d52
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681718"
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

Azt is feltételezi, hogy az Azure `az` CLI és `kubectl` az eszközök telepítve vannak.

A futtatásával ellenőrizheti, hogy telepítve `az` van-e az eszköz:

```azurecli
az --version
```

Ha nincs telepítve az `az` eszköz, [itt](/cli/azure/install-azure-cli)talál útmutatást.

A futtatásával ellenőrizheti, hogy telepítve `kubectl` van-e az eszköz:

```bash
kubectl version
```

Ha nincs `kubectl` telepítve, a következőket futtathatja:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>A méretarányos ügynök telepítése Daemonset elemet
A Kubernetes a [DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) használja a tároló egyetlen példányának futtatására a fürt minden gazdagépén.
Tökéletesek a figyelési ügynökök, például az egyméretű ügynök futtatásához.

Miután bejelentkezett a Méretarányba, lépjen az [ügynök lapra](https://developer.newrelic.com/) , és telepítse a daemonset elemet-t használó, a fürthöz tartozó méretarányos ügynököket. A méretarányos felhasználói felület interaktív konfigurációs lépéseket biztosít az ügynökök létrehozásához és a teljes Kubernetes-fürt figyelésének megkezdéséhez.

![A méretezési ügynök konfigurációja](./media/container-service-kubernetes-coscale/installation.png)

Az ügynök a fürtön való indításához futtassa a megadott parancsot:

![A méretarányos ügynök elindítása](./media/container-service-kubernetes-coscale/agent_script.png)

Ennyi az egész! Az ügynökök üzembe helyezése után néhány percen belül meg kell jelennie az adatnak a-konzolon. Az [ügynök lapon](https://developer.newrelic.com/) megtekintheti a fürt összegzését, további konfigurációs lépéseket hajthat végre, és megtekintheti az irányítópultok, például a **Kubernetes-fürt áttekintését**.

![A Kubernetes-fürt áttekintése](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

A rendszer a fürtben lévő új gépekre automatikusan telepíti a méretezési ügynököt. Az ügynök automatikusan frissül, amikor megjelent egy új verzió.


## <a name="next-steps"></a>További lépések

A többméretű figyelési megoldásokkal kapcsolatos további információkért tekintse meg a többoldalas [dokumentációt](https://web.archive.org/web/20180415164304/http://docs.coscale.com:80/) és a [blogot](https://web.archive.org/web/20170501021344/http://www.coscale.com:80/blog) . 

