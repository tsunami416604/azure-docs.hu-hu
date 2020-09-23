---
title: A PostgreSQL nagy kapacitású-kiszolgálócsoportok hibáinak megoldása
description: A PostgreSQL nagy kapacitású-kiszolgálócsoportok hibáinak megoldása Jupyter Notebook
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 9c0d3d9c74be8dabaec20ff5d4c7e7cfc74d8eef
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936052"
---
# <a name="troubleshooting-postgresql-hyperscale-server-groups"></a>A PostgreSQL nagy kapacitású-kiszolgálócsoportok hibaelhárítása

A jegyzetfüzetek olyan eljárásokat is dokumentálnak, amelyek a Markdown-tartalmakat ismertetik, és leírják, hogy mi a teendő. Végrehajtható kódot is biztosít az eljárások automatizálásához.  Ez a minta minden esetben hasznos, ha a szokásos működési eljárások alapján hibaelhárítási útmutatók vannak.

Tegyük fel például, hogy a PostgreSQL nagy kapacitású-kiszolgálócsoport hibaelhárítása során problémák merülhetnek fel Azure Data Studio használatával.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="install-tools"></a>Eszközök telepítése

Telepítse a Azure Data Studiot, `kubectl` és azon `azdata` az ügyfélszámítógépen, amelyet a jegyzetfüzetnek a Azure Data Studio-ben való futtatásához használ. Ehhez kövesse az [ügyféleszközök telepítése](install-client-tools.md) című témakör útmutatását.

## <a name="update-the-path-environment-variable"></a>A PATH környezeti változó frissítése

Győződjön meg arról, hogy ezek az eszközök bárhonnan meghívhatók ezen az ügyfélszámítógépen. Például egy Windows-ügyfélgépen frissítse a PATH rendszerkörnyezeti változót, és adja hozzá azt a mappát, amelyben a kubectl telepítette.

## <a name="sign-in-with-azdata"></a>Bejelentkezés a `azdata`

Jelentkezzen be az ív-adatkezelőből erről az ügyfélszámítógépről, és a Azure Data Studio elindítása előtt. Ehhez a következőhöz hasonló parancsot kell futtatnia:

```console
azdata login --endpoint https://<IP address>:<port>
```

A helyére írja `<IP address>` be a Kubernetes-fürt IP-címét, valamint `<port>` azt a portot, amelyen a Kubernetes figyel. A rendszer kérni fogja a Felhasználónév és a jelszó megadását. További részletekért futtassa a következőt: _

```console
azdata login --help
```

## <a name="log-into-your-kubernetes-cluster-with-kubectl"></a>Jelentkezzen be a Kubernetes-fürtbe a kubectl

Ehhez érdemes lehet az [ebben](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/) a blogbejegyzésben megadott példa parancsokat használni.
A következőhöz hasonló parancsokat kell futtatnia:

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

### <a name="the-troubleshooting-notebook"></a>A hibaelhárítási jegyzetfüzet

Indítsa el Azure Data Studio, és nyissa meg a hibaelhárítási jegyzetfüzetet. 

Hajtsa végre a következő témakörben ismertetett lépéseket:  [033-Manage-postgres-with-AzureDataStudio.MD](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md) :

1. Kapcsolódás az ív-adatkezelőhöz
2. Kattintson a jobb gombbal a postgres-példányra, és válassza a **[kezelés]** lehetőséget.
3. Válassza a **[problémák diagnosztizálása és megoldása] irányítópultot** .
4. Válassza a **[hibakeresés] hivatkozást**

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Azure Data Studio – a PostgreSQL hibaelhárítási Jegyzetfüzetének megnyitása":::

A **TSG100 – az Azure arc-kompatibilis PostgreSQL nagy kapacitású hibaelhárító jegyzetfüzete** a következőket nyitja meg: :::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="Azure Data Studio – a PostgreSQL hibaelhárítási notebookjának használata":::

### <a name="run-the-scripts"></a>Parancsfájlok futtatása
Válassza az összes futtatása gombot a lap tetején, hogy egyszerre hajtsa végre a jegyzetfüzetet, vagy az egyes kódrészleteket egyenként is végrehajthatja.

Megtekintheti a programkódok végrehajtásának kimenetét az esetleges problémákhoz.

A jegyzetfüzettel kapcsolatban a gyakori problémák felismerésével és megoldásával kapcsolatos további részleteket is felvesszük.

## <a name="next-step"></a>Következő lépés
- További információ [Az Azure arc-kompatibilis adatszolgáltatások naplófájljainak beszerzéséről](troubleshooting-get-logs.md)
- További információ a [Kibana](monitor-grafana-kibana.md)
- További információ a Grafana-mel végzett [figyelésről](monitor-grafana-kibana.md)
- Saját jegyzetfüzetek létrehozása
