---
title: Privát végpont konfigurálása
titleSuffix: Azure Machine Learning
description: Az Azure Private link használatával biztonságosan férhet hozzá a Azure Machine Learning-munkaterülethez egy virtuális hálózatról.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/30/2020
ms.openlocfilehash: 2953f85a5c21cdd670d6e133d09ffacf06f178ef
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842702"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>Azure Private-hivatkozás konfigurálása Azure Machine Learning munkaterülethez

Ebből a dokumentumból megtudhatja, hogyan használhatja az Azure Private-hivatkozást a Azure Machine Learning munkaterülettel. Azure Machine Learning virtuális hálózatának létrehozásával kapcsolatos információkért lásd: a [virtuális hálózatok elkülönítése és az adatvédelem áttekintése](how-to-network-security-overview.md)

Az Azure Private link segítségével privát végponton keresztül csatlakozhat a munkaterülethez. A magánhálózati végpont a virtuális hálózaton belüli magánhálózati IP-címek készlete. Ezután korlátozhatja a hozzáférést a munkaterülethez, hogy csak a magánhálózati IP-címeken keresztül történjen. A privát hivatkozás segít csökkenteni az adatkiszűrése kockázatát. Ha többet szeretne megtudni a privát végpontokról, tekintse meg az [Azure privát hivatkozását](../private-link/private-link-overview.md) ismertető cikket.

> [!IMPORTANT]
> Az Azure Private-hivatkozás nem befolyásolja az Azure Control Plant (felügyeleti műveletek), például a munkaterület törlését vagy a számítási erőforrások kezelését. Például létrehozhatja, frissítheti vagy törölheti a számítási célt. Ezeket a műveleteket a rendszer a szokásos módon, a nyilvános interneten hajtja végre. Az adatsík műveletei, például a Azure Machine Learning Studio, az API-k (beleértve a közzétett folyamatokat is), vagy az SDK használja a privát végpontot.
>
> A Mozilla Firefox használata esetén problémák merülhetnek fel a munkaterület privát végpontjának elérésére tett kísérlet során. Ez a probléma a HTTPS-en keresztül a Mozilla-on keresztül köthető a DNS-hez. Javasoljuk, hogy megkerülő megoldásként használja a Google Chrome Microsoft Edge használatát.

## <a name="prerequisites"></a>Előfeltételek

Ha egy ügyfél által felügyelt kulccsal rendelkező magánhálózati kapcsolaton alapuló munkaterületet tervez használni, ezt a szolgáltatást támogatási jegy használatával kell kérnie. További információ: a [kvóták kezelése és növelése](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

## <a name="limitations"></a>Korlátozások

A privát hivatkozást tartalmazó Azure Machine Learning munkaterület nem érhető el a Azure Government-régiókban vagy az Azure China 21Vianet-régiókban.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Privát végpontot használó munkaterület létrehozása

A következő módszerek egyikével hozzon létre egy olyan munkaterületet, amely privát végpontot használ. Mindkét módszerhez __egy meglévő virtuális hálózat szükséges__:

> [!TIP]
> Ha egy munkaterületet, privát végpontot és virtuális hálózatot szeretne egyszerre létrehozni, tekintse meg a [Azure Resource Manager sablon használata munkaterületek Azure Machine Learninghoz való létrehozásával foglalkozó](how-to-create-workspace-template.md)témakört.

# <a name="python"></a>[Python](#tab/python)

A Azure Machine Learning Python SDK biztosítja a [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) osztályt, amely a [munkaterülettel együtt használható. hozzon létre ()](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) egy olyan munkaterületet, amely privát végponttal rendelkezik. Ehhez az osztályhoz egy meglévő virtuális hálózat szükséges.

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.create(name='myworkspace',
    subscription_id='<my-subscription-id>',
    resource_group='myresourcegroup',
    location='eastus2',
    private_endpoint_config=pe,
    private_endpoint_auto_approval=True,
    show_output=True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A [Machine learninghez készült Azure CLI-bővítmény](reference-azure-machine-learning-cli.md) az az [ml Workspace Create](/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_create) parancsot biztosítja. Ehhez a parancshoz a következő paraméterek használhatók egy privát hálózattal rendelkező munkaterület létrehozásához, de egy meglévő virtuális hálózatra van szükség:

* `--pe-name`: A létrehozott privát végpont neve.
* `--pe-auto-approval`: Azt határozza meg, hogy a rendszer automatikusan jóváhagyja-e a személyes végponti kapcsolatokat a munkaterülethez.
* `--pe-resource-group`: Az az erőforráscsoport, amelyben létre kell hozni a magánhálózati végpontot a alkalmazásban. A virtuális hálózatot tartalmazó csoportnak kell lennie.
* `--pe-vnet-name`: A meglévő virtuális hálózat, amely létrehozza a magánhálózati végpontot a alkalmazásban.
* `--pe-subnet-name`: Annak az alhálózatnak a neve, amelybe a magánhálózati végpontot létre kívánja hozni. Az alapértelmezett érték `default`.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Machine Learning Studio __hálózatkezelés__ lapja lehetővé teszi privát végpontok konfigurálását. Azonban szükség van egy meglévő virtuális hálózatra. További információ: [munkaterületek létrehozása a portálon](how-to-manage-workspace.md).

---

## <a name="add-a-private-endpoint-to-a-workspace"></a>Privát végpont hozzáadása munkaterülethez

A következő módszerek egyikével adhat hozzá privát végpontot egy meglévő munkaterülethez:

> [!IMPORTANT]
>
> A privát végpont létrehozásához rendelkeznie kell egy meglévő virtuális hálózattal. A privát végpont hozzáadása előtt [le kell tiltania a magánhálózati végpontok hálózati házirendjeit](../private-link/disable-private-endpoint-network-policy.md) is.

> [!WARNING]
>
> Ha rendelkezik a munkaterülethez kapcsolódó meglévő számítási célokkal, és nem ugyanazon a virtuális hálózat mögött található, a titkos végpont a-ben jön létre, nem fog működni.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.from_config()
ws.add_private_endpoint(private_endpoint_config=pe, private_endpoint_auto_approval=True, show_output=True)
```

Az ebben a példában használt osztályokkal és módszerekkel kapcsolatos további információkért lásd: [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) és [Workspace.add_private_endpoint](/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#add-private-endpoint-private-endpoint-config--private-endpoint-auto-approval-true--location-none--show-output-true--tags-none-).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A [Machine learninghez készült Azure CLI-bővítmény](reference-azure-machine-learning-cli.md) az az [ml munkaterület Private-Endpoint Add](/cli/azure/ext/azure-cli-ml/ml/workspace/private-endpoint?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_private_endpoint_add) parancsot biztosítja.

```azurecli
az ml workspace private-endpoint add -w myworkspace  --pe-name myprivateendpoint --pe-auto-approval true --pe-vnet-name myvnet
```

# <a name="portal"></a>[Portál](#tab/azure-portal)

A portál Azure Machine Learning munkaterületén válassza ki a __privát végponti kapcsolatok__ elemet, majd válassza a __+ privát végpont__ lehetőséget. Hozzon létre egy új privát végpontot a mezők használatával.

* A __régió__ kiválasztásakor válassza ki a virtuális hálózattal megegyező régiót. 
* Az __erőforrástípus__ kiválasztásakor használja a __Microsoft. MachineLearningServices/workspaces__ elemet. 
* Állítsa az __erőforrást__ a munkaterület nevére.

Végül a __Létrehozás__ gombra kattintva hozza létre a privát végpontot.

---

## <a name="remove-a-private-endpoint"></a>Privát végpont eltávolítása

A következő módszerek egyikével távolíthatja el a privát végpontot egy munkaterületről:

# <a name="python"></a>[Python](#tab/python)

A [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#delete-private-endpoint-connection-private-endpoint-connection-name-) használatával távolíthat el egy privát végpontot.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
# get the connection name
_, _, connection_name = ws.get_details()['privateEndpointConnections'][0]['id'].rpartition('/')
ws.delete_private_endpoint_connection(private_endpoint_connection_name=connection_name)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A [Machine learninghez készült Azure CLI-bővítmény](reference-azure-machine-learning-cli.md) az az [ml Workspace Private-Endpoint delete](/cli/azure/ext/azure-cli-ml/ml/workspace/private-endpoint?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_private_endpoint_delete) parancsot biztosítja.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A portál Azure Machine Learning munkaterületén válassza a __privát végponti kapcsolatok__ lehetőséget, majd válassza ki az eltávolítani kívánt végpontot. Végül válassza az __Eltávolítás__ lehetőséget.

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>Munkaterület használata privát végponton keresztül

Mivel a munkaterület felé irányuló kommunikáció csak a virtuális hálózatról engedélyezett, a munkaterületet használó fejlesztési környezeteknek a virtuális hálózat tagjának kell lenniük. Például egy virtuális gép a virtuális hálózaton.

> [!IMPORTANT]
> A kapcsolat ideiglenes megszakadásának elkerülése érdekében a Microsoft javasolja a DNS-gyorsítótár kiürítését a munkaterülethez csatlakozó számítógépeken a privát hivatkozás engedélyezése után. 

Az Azure Virtual Machinesról a [Virtual Machines dokumentációjában](../virtual-machines/index.yml)olvashat bővebben.


## <a name="next-steps"></a>Következő lépések

* A Azure Machine Learning munkaterület biztonságossá tételével kapcsolatos további információkért tekintse meg a [Virtual Network elkülönítés és adatvédelem áttekintése](how-to-network-security-overview.md) című cikket.

* Ha egyéni DNS-megoldást szeretne használni a virtuális hálózaton, tekintse meg a [munkaterületek egyéni DNS-kiszolgálóval való használatát](how-to-custom-dns.md)ismertető témakört.
