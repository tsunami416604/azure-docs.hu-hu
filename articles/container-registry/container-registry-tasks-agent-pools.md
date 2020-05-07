---
title: Dedikált készlet használata feladat-feladatok futtatásához
description: Hozzon létre egy dedikált számítási készletet (Agent-készletet) a beállításjegyzékben egy Azure Container Registry feladat futtatásához.
ms.topic: article
ms.date: 04/30/2020
ms.openlocfilehash: 9eac8d620dd6ce268692494499ade39173fc9b75
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864655"
---
# <a name="run-an-acr-task-on-a-dedicated-agent-pool"></a>ACR-feladat futtatása dedikált ügynök-készleten

Állítson be egy Azure által felügyelt virtuálisgép-készletet (*Agent-készletet*) a [Azure Container Registry feladatok][acr-tasks] dedikált számítási környezetben való futtatásának engedélyezéséhez. Miután konfigurált egy vagy több készletet a beállításjegyzékben, kiválaszthatja a készletet, hogy a szolgáltatás alapértelmezett számítási környezete helyett feladatot futtasson.

Az ügynök készlete a következőket biztosítja:

- **Virtuális hálózatok támogatása** – ügynök-készletet rendelhet egy Azure-VNet, amely hozzáférést biztosít a VNet lévő erőforrásokhoz, például egy tároló-beállításjegyzékhez, egy kulcstartóhoz vagy egy tárolóhoz.
- **Méretezés igény szerint** – növelje az ügynök készletében lévő példányok számát a nagy számítási igényű feladatokhoz, vagy a méretezést nullára. A számlázás a kiosztáson alapul.
- **Rugalmas beállítások** – különböző készlet- [rétegekből](#pool-tiers) és méretezési lehetőségek közül választhat a feladatok számítási feladatainak kielégítése érdekében.
- **Azure-felügyelet** – a feladatok készleteit az Azure javította és karbantartja, és fenntartott foglalást biztosít az egyes virtuális gépek karbantartásának szükségessége nélkül.

Ez a funkció a **prémium** szintű Container Registry szolgáltatási szinten érhető el. További információ a beállításjegyzék szolgáltatási szintjeiről és korlátairól: [Azure Container Registry SKU][acr-tiers]-i.

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és bizonyos [korlátozások érvényesek](#preview-limitations). Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.
>

## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

- A Task Agent-készletek jelenleg támogatják a Linux-csomópontokat. A Windows-csomópontok jelenleg nem támogatottak.
- A Feladatkezelő-készletek előzetes verzióban érhetők el a következő régiókban: USA 2. nyugati régiója, USA déli középső régiója, USA 2. keleti régiója és az USA keleti régiója.
- Minden egyes beállításjegyzék esetében a teljes vCPU (Core) kvóta az összes ügynök-készlet esetében 16. Nyisson meg egy [támogatási kérést][open-support-ticket] további foglaláshoz.

## <a name="prerequisites"></a>Előfeltételek

* A cikkben szereplő Azure CLI-lépések használatához az Azure CLI 2.3.1-es vagy újabb verziójára van szükség. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli]. Vagy futtassa [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Ha még nem rendelkezik tároló-beállításjegyzékkel, [hozzon létre egyet][create-reg-cli] (prémium szintű csomag szükséges) az előzetes verzió régiójában.

## <a name="pool-tiers"></a>Készlet szintjei

Az ügynökök szintjei a készletben a következő erőforrásokat biztosítják.

|Szint  |CPU  |Memória (GB)  |
|---------|---------|---------|
|S1     |  2       |  3       |
|S2     |  4       |   8      |
|S3     |    8     |   16      |

## <a name="create-and-manage-a-task-agent-pool"></a>Task Agent-készlet létrehozása és kezelése

### <a name="set-default-registry-optional"></a>Alapértelmezett beállításjegyzék beállítása (nem kötelező)

Az alábbi Azure CLI-parancsok leegyszerűsítéséhez állítsa be az alapértelmezett beállításjegyzéket az az [configure][az-configure] parancs futtatásával:

```azurecli
az configure --defaults acr=<registryName>
```

Az alábbi példák azt feltételezik, hogy beállította az alapértelmezett beállításjegyzéket. Ha nem, adjon meg `--registry <registryName>` egy paramétert `az acr` az egyes parancsokban.

### <a name="create-agent-pool"></a>Ügynök készletének létrehozása

Hozzon létre egy ügynök-készletet az az [ACR agentpool Create][az-acr-agentpool-create] paranccsal. A következő példában létrehozunk egy S2-es (4 processzor/példány) szintű készletet. Alapértelmezés szerint a készlet 1 példányt tartalmaz.

```azurecli
az acr agentpool create \
    --name myagentpool \
    --tier S2
```

Az ügynök-készlet és a többi készletezési kezelési művelet elvégzése több percet is igénybe vehet.

### <a name="scale-pool"></a>Készlet méretezése

A készlet méretének felfelé vagy lefelé méretezése az az [ACR agentpool Update][az-acr-agentpool-update] paranccsal. Az alábbi példa a készletet 2 példányra méretezi. Akár 0 példányra is méretezhető.

```azurecli
az acr agentpool update \
    --name myagentpool \
    --count 2
```

## <a name="create-pool-in-a-virtual-network"></a>Készlet létrehozása egy virtuális hálózaton

### <a name="add-firewall-rules"></a>Tűzfalszabályok hozzáadása

A Task Agent-készletek a következő Azure-szolgáltatásokhoz való hozzáférést igénylik. A következő tűzfalszabályok hozzáadására van szükség a meglévő hálózati biztonsági csoportokhoz vagy felhasználó által megadott útvonalakhoz.

| Irány | Protocol (Protokoll) | Forrás         | Forrásport | Cél          | Cél port | Használt    |
|-----------|----------|----------------|-------------|----------------------|-----------|---------|
| Kimenő  | TCP      | VirtualNetwork | Bármelyik         | Storage              | 443       | Alapértelmezett |
| Kimenő  | TCP      | VirtualNetwork | Bármelyik         | EventHub             | 443       | Alapértelmezett |
| Kimenő  | TCP      | VirtualNetwork | Bármelyik         | AzureActiveDirectory | 443       | Alapértelmezett |
| Kimenő  | TCP      | VirtualNetwork | Bármelyik         | AzureMonitor         | 443       | Alapértelmezett |

> [!NOTE]
> Ha a feladatok további erőforrásokat igényelnek a nyilvános internetről, adja hozzá a megfelelő szabályokat. Ezek a szabályok például akkor szükségesek, ha olyan Docker-Build feladatot futtat, amelynek a Docker hub alapképeit kell lekérnie, vagy helyreállít egy NuGet-csomagot.

### <a name="create-pool-in-vnet"></a>Készlet létrehozása a VNet-ben

A következő példa egy ügynököt hoz létre a hálózati *myvnet* *mysubnet* alhálózatában:

```azurecli
# Get the subnet ID
subnetId=$(az network vnet subnet show \
        --resource-grop myresourcegroup \
        --vnet-name myvnet \
        --name mysubnetname \
        --query id --output tsv)

az acr agentpool create \
    --name myagentpool \
    --tier S2 \
    --subnet-id $subnetId
```

## <a name="run-task-on-agent-pool"></a>Feladat futtatása az ügynök-készleten

Az alábbi példák bemutatják, hogyan lehet ügynök-készletet megadni egy feladat sorba állítása során.

> [!NOTE]
> Ha egy ACR-feladatban szeretné használni az ügynököt, győződjön meg arról, hogy a készlet legalább 1 példányt tartalmaz.
>

### <a name="quick-task-run"></a>Gyors feladat futtatása

Az az [ACR Build][az-acr-build] paranccsal az ügynök készletének gyors futtatását, és adja át a `--agent-pool` következő paramétert:

```azurecli
az acr build \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --file Dockerfile \
    https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

### <a name="automatically-triggered-task"></a>Automatikusan aktivált feladat

Például hozzon létre egy ütemezett feladatot az ügynök-készleten az [az ACR Task Create][az-acr-task-create]paranccsal, `--agent-pool` és adja át a paramétert.

```azurecli
az acr task create \
    --name mytask \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --schedule "0 21 * * *" \
    --file Dockerfile \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --commit-trigger-enabled false
```

A feladat beállításának ellenőrzéséhez futtassa [az az ACR Task Run][az-acr-task-run]parancsot:

```azurecli
az acr task run \
    --name mytask
```

### <a name="query-pool-status"></a>Lekérdezési készlet állapota

Az ügynök készletében jelenleg ütemezett futtatások számának megállapításához futtassa az [az ACR agentpool show][az-acr-agentpool-show]parancsot.

```azurecli
az acr agentpool show \
    --name myagentpool \
    --queue-count
```

## <a name="next-steps"></a>További lépések

A tárolók rendszerképének Build és a Felhőbeli karbantartásával kapcsolatos további példákért tekintse meg az [ACR-feladatok oktatóanyag-sorozatát](container-registry-tutorial-quick-task.md).



[acr-tasks]:           container-registry-tasks-overview.md
[acr-tiers]:           container-registry-skus.md
[azure-cli]:           /cli/azure/install-azure-cli
[open-support-ticket]: https://aka.ms/acr/support/create-ticket
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[az-configure]: /cli/azure#az-configure
[az-acr-agentpool-create]: /cli/azure/acr/agentpool#az-acr-agentpool-create
[az-acr-agentpool-update]: /cli/azure/acr/agentpool#az-acr-agentpool-update
[az-acr-agentpool-show]: /cli/azure/acr/agentpool#az-acr-agentpool-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[create-reg-cli]: container-registry-get-started-azure-cli.md
