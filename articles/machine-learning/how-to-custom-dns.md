---
title: Egyéni DNS-kiszolgáló használata
titleSuffix: Azure Machine Learning
description: Egyéni DNS-kiszolgáló konfigurálása Azure Machine Learning munkaterülettel és privát végponttal való együttműködéshez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 10/05/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 1d215c9564d89e5bd410e68839807f5c2c752356
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828516"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>A munkaterület használata egyéni DNS-kiszolgálóval

Ha virtuális hálózattal Azure Machine Learning használ, a [DNS-névfeloldás több módon is kezelhető](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances). Alapértelmezés szerint az Azure automatikusan kezeli a munkaterület és a magánhálózati végpont névfeloldását. A __saját egyéni DNS-kiszolgáló használatakor__azonban manuálisan kell létrehoznia a munkaterület DNS-bejegyzéseit.

> [!IMPORTANT]
> Ez a cikk csak a teljes tartománynév (FQDN) és az IP-címek megkeresését ismerteti ezen bejegyzések esetében, ezért nem nyújt információt ezen elemek DNS-rekordjainak konfigurálásáról. A rekordok hozzáadásával kapcsolatos információkért olvassa el a DNS-szoftver dokumentációját.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-Virtual Network, amely [a saját DNS-kiszolgálóját](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)használja.

- Egy privát végponttal rendelkező Azure Machine Learning munkaterület. További információ: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).

- A [hálózat elkülönítésének ismerete a & következtetések betanítása során](how-to-enable-virtual-network.md).

- Opcionálisan, [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) vagy [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="find-the-ip-addresses"></a>IP-címek keresése

A következő lista tartalmazza a munkaterület és a privát végpont által használt teljes tartományneveket (FQDN):

* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.studio.workspace.<region>.api.azureml.ms`
* `cert-<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
* `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
* `<workspace-GUID>.workspace.<region>.aether.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.ml`
* Számítási példány létrehozásakor hozzá kell adnia egy bejegyzést is a következőhöz: `<instance-name>.<region>.instances.azureml.ms` .

A VNet lévő teljes tartománynevek belső IP-címeinek megkereséséhez használja az alábbi módszerek egyikét:

> [!NOTE]
> A teljes tartománynevek és IP-címek a konfigurációtól függően eltérőek lesznek. A tartománynév GUID értéke például a munkaterületre lesz jellemző.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az network private-endpoint show --endpoint-name <endpoint> --resource-group <resource-group> --query 'customDnsConfigs[*].{FQDN: fqdn, IPAddress: ipAddresses[0]}' --output table
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
$workspaceDns=Get-AzPrivateEndpoint -Name <endpoint> -resourcegroupname <resource-group>
$workspaceDns.CustomDnsConfigs | format-table
```

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. A [Azure Portal](https://portal.azure.com)válassza ki Azure Machine learning __munkaterületét__.
1. A __Beállítások__ szakaszban válassza a __privát végponti kapcsolatok__elemet.
1. Válassza ki a hivatkozást a __privát végpont__ oszlopban, amely megjelenik.
1. A munkaterület privát végpontjának teljes tartományneve (FQDN) és IP-címeinek listája az oldal alján található.

:::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="A portálon található teljes tartománynevek listája":::

---

Az összes metódus által visszaadott információ ugyanaz; az erőforrások teljes tartománynevének és magánhálózati IP-címének listája.

| FQDN | IP-cím |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.studio.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `cert-fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

> [!IMPORTANT]
> Egyes teljes tartománynevek nem jelennek meg a privát végpontok listáján, de a munkaterület számára szükséges. Ezek a teljes tartománynevek az alábbi táblázatban láthatók, és a DNS-kiszolgálóhoz is hozzá kell adni:
>
> * `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
> * `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
> * `<workspace-GUID>.workspace.<region>.aether.ms`
> * Ha számítási példánya van, használja a (z `<instance-name>.<region>.instances.azureml.ms` ) értéket, ahol a a `<instance-name>` számítási példány neve.
>
> Az összes IP-cím esetében ugyanazt a címet használja, mint az `*.api.azureml.ms` előző lépésekből visszaadott bejegyzéseket.

## <a name="next-steps"></a>Következő lépések

A Azure Machine Learning virtuális hálózattal való használatával kapcsolatos további információkért lásd a [virtuális hálózat áttekintését](how-to-network-security-overview.md).