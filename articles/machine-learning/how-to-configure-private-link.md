---
title: Privát végpont konfigurálása
titleSuffix: Azure Machine Learning
description: Az Azure Private link használatával biztonságosan férhet hozzá a Azure Machine Learning-munkaterülethez egy virtuális hálózatról.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/30/2020
ms.openlocfilehash: 4ba7ec73ac70723e21b6acad571d62d14edd250a
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828133"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>Azure Private-hivatkozás konfigurálása Azure Machine Learning munkaterülethez

Ebből a dokumentumból megtudhatja, hogyan használhatja az Azure Private-hivatkozást a Azure Machine Learning munkaterülettel. Azure Machine Learning virtuális hálózatának létrehozásával kapcsolatos információkért lásd: a [virtuális hálózatok elkülönítése és az adatvédelem áttekintése](how-to-network-security-overview.md)

Az Azure Private link segítségével privát végponton keresztül csatlakozhat a munkaterülethez. A magánhálózati végpont a virtuális hálózaton belüli magánhálózati IP-címek készlete. Ezután korlátozhatja a hozzáférést a munkaterülethez, hogy csak a magánhálózati IP-címeken keresztül történjen. A privát hivatkozás segít csökkenteni az adatkiszűrése kockázatát. Ha többet szeretne megtudni a privát végpontokról, tekintse meg az [Azure privát hivatkozását](/azure/private-link/private-link-overview) ismertető cikket.

> [!IMPORTANT]
> Az Azure Private-hivatkozás nem befolyásolja az Azure Control Plant (felügyeleti műveletek), például a munkaterület törlését vagy a számítási erőforrások kezelését. Például létrehozhatja, frissítheti vagy törölheti a számítási célt. Ezeket a műveleteket a rendszer a szokásos módon, a nyilvános interneten hajtja végre. Az adatsík műveletei, például a Azure Machine Learning Studio, az API-k (beleértve a közzétett folyamatokat is), vagy az SDK használja a privát végpontot.
>
> A Mozilla Firefox használata esetén problémák merülhetnek fel a munkaterület privát végpontjának elérésére tett kísérlet során. Ez a probléma a HTTPS-en keresztül a Mozilla-on keresztül köthető a DNS-hez. Javasoljuk, hogy megkerülő megoldásként használja a Google Chrome Microsoft Edge használatát.

## <a name="prerequisites"></a>Előfeltételek

Ha egy ügyfél által felügyelt kulccsal rendelkező magánhálózati kapcsolaton alapuló munkaterületet tervez használni, ezt a szolgáltatást támogatási jegy használatával kell kérnie. További információ: a [kvóták kezelése és növelése](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

## <a name="limitations"></a>Korlátozások

A privát hivatkozást tartalmazó Azure Machine Learning munkaterület nem érhető el a Azure Government-régiókban vagy az Azure China 21Vianet-régiókban.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Privát végpontot használó munkaterület létrehozása

A következő módszerek egyikével hozzon létre egy olyan munkaterületet, amely privát végpontot használ:

> [!TIP]
> Ha szükséges, a Azure Resource Manager sablon új virtuális hálózatot hozhat létre. A többi módszerhez mind meglévő virtuális hálózat szükséges.

# <a name="resource-manager-template"></a>[Resource Manager-sablon](#tab/azure-resource-manager)

A (z) Azure Resource Manager sablon segítségével [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) egyszerűen hozhat létre egy privát végponttal és virtuális hálózattal rendelkező munkaterületet.

További információ a sablon használatáról, beleértve a privát végpontokat is: [Azure Resource Manager sablon használata munkaterületek létrehozásához Azure Machine Learninghoz](how-to-create-workspace-template.md).

# <a name="python"></a>[Python](#tab/python)

A Azure Machine Learning Python SDK biztosítja a [PrivateEndpointConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) osztályt, amely a [munkaterülettel együtt használható. hozzon létre ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) egy olyan munkaterületet, amely privát végponttal rendelkezik. Ehhez az osztályhoz egy meglévő virtuális hálózat szükséges.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A [Machine learninghez készült Azure CLI-bővítmény](reference-azure-machine-learning-cli.md) az az [ml Workspace Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_create) parancsot biztosítja. Ehhez a parancshoz a következő paraméterek használhatók egy privát hálózattal rendelkező munkaterület létrehozásához, de egy meglévő virtuális hálózatra van szükség:

* `--pe-name`: A létrehozott privát végpont neve.
* `--pe-auto-approval`: Azt határozza meg, hogy a rendszer automatikusan jóváhagyja-e a személyes végponti kapcsolatokat a munkaterülethez.
* `--pe-resource-group`: Az az erőforráscsoport, amelyben létre kell hozni a magánhálózati végpontot a alkalmazásban. A virtuális hálózatot tartalmazó csoportnak kell lennie.
* `--pe-vnet-name`: A meglévő virtuális hálózat, amely létrehozza a magánhálózati végpontot a alkalmazásban.
* `--pe-subnet-name`: Annak az alhálózatnak a neve, amelybe a magánhálózati végpontot létre kívánja hozni. Az alapértelmezett érték `default`.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Machine Learning Studio __hálózatkezelés__ lapja lehetővé teszi privát végpontok konfigurálását. Azonban szükség van egy meglévő virtuális hálózatra. További információ: [munkaterületek létrehozása a portálon](how-to-manage-workspace.md).

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>Munkaterület használata privát végponton keresztül

Mivel a munkaterület felé irányuló kommunikáció csak a virtuális hálózatról engedélyezett, a munkaterületet használó fejlesztési környezeteknek a virtuális hálózat tagjának kell lenniük. Például egy virtuális gép a virtuális hálózaton.

> [!IMPORTANT]
> A kapcsolat ideiglenes megszakadásának elkerülése érdekében a Microsoft javasolja a DNS-gyorsítótár kiürítését a munkaterülethez csatlakozó számítógépeken a privát hivatkozás engedélyezése után. 

Az Azure Virtual Machinesról a [Virtual Machines dokumentációjában](/azure/virtual-machines/)olvashat bővebben.


## <a name="next-steps"></a>Következő lépések

* A Azure Machine Learning munkaterület biztonságossá tételével kapcsolatos további információkért tekintse meg a [Virtual Network elkülönítés és adatvédelem áttekintése](how-to-network-security-overview.md) című cikket.

* Ha egyéni DNS-megoldást szeretne használni a virtuális hálózaton, tekintse meg a [munkaterületek egyéni DNS-kiszolgálóval való használatát](how-to-custom-dns.md)ismertető témakört.
