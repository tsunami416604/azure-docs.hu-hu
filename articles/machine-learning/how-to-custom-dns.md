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
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 07ff656c5eacbbcdc16c6c7cf098478ca6baf745
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509291"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>A munkaterület használata egyéni DNS-kiszolgálóval

Ha Azure Machine Learning munkaterületet használ privát végponttal, a [DNS-névfeloldás többféleképpen is kezelhető](../private-link/private-endpoint-dns.md). Alapértelmezés szerint az Azure automatikusan kezeli a munkaterület és a magánhálózati végpont névfeloldását. Ha ehelyett _saját, egyéni DNS-kiszolgálót használ_, akkor manuálisan kell létrehoznia a munkaterület DNS-bejegyzéseit.

> [!IMPORTANT]
> Ez a cikk csak a teljes tartománynév (FQDN) és az IP-címek megkeresését ismerteti ezen bejegyzések esetében, ezért nem nyújt információt ezen elemek DNS-rekordjainak konfigurálásáról. A rekordok hozzáadásával kapcsolatos információkért olvassa el a DNS-szoftver dokumentációját.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-Virtual Network, amely [a saját DNS-kiszolgálóját](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)használja.

- Egy privát végponttal rendelkező Azure Machine Learning munkaterület. További információ: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).

- A [hálózat elkülönítésének ismerete a & következtetések betanítása során](./how-to-network-security-overview.md).

- Az [Azure Private Endpoint DNS-zóna konfigurációjának](../private-link/private-endpoint-dns.md) ismerete

- Opcionálisan, [Azure CLI](/cli/azure/install-azure-cli) vagy [Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="find-the-ip-addresses"></a>IP-címek keresése

A következő lista tartalmazza a munkaterület és a privát végpont által használt teljes tartományneveket (FQDN):

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
* `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
* `<workspace-GUID>.workspace.<region>.aether.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.net`
* Ha számítási példányt hoz létre, a saját IP-címéhez is hozzá kell adnia egy bejegyzést a `<instance-name>.<region>.instances.azureml.ms` munkaterület privát végpontja számára.

    > [!NOTE]
    > A számítási példányok csak a virtuális hálózaton belül érhetők el.

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
1. A __Beállítások__ szakaszban válassza a __privát végponti kapcsolatok__ elemet.
1. Válassza ki a hivatkozást a __privát végpont__ oszlopban, amely megjelenik.
1. A munkaterület privát végpontjának teljes tartományneve (FQDN) és IP-címeinek listája az oldal alján található.

:::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="A portálon található teljes tartománynevek listája":::

---

Az összes metódus által visszaadott információ ugyanaz; az erőforrások teljes tartománynevének és magánhálózati IP-címének listája.

| FQDN | IP-cím |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

> [!IMPORTANT]
> Egyes teljes tartománynevek nem jelennek meg a privát végpontok listáján, de a munkaterület számára szükséges. Ezek a teljes tartománynevek az alábbi táblázatban láthatók, és a DNS-kiszolgálóhoz is hozzá kell adni:
>
> * `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
> * `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
> * `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
> * `<workspace-GUID>.workspace.<region>.aether.ms`
> * Ha számítási példánya van, használja a (z `<instance-name>.<region>.instances.azureml.ms` ) értéket, ahol a a `<instance-name>` számítási példány neve. Használja a munkaterület magánhálózati végpontjának magánhálózati IP-címét. Vegye figyelembe, hogy a számítási példány csak a virtuális hálózaton belülről érhető el.
>
> Az összes IP-cím esetében ugyanazt a címet használja, mint az `*.api.azureml.ms` előző lépésekből visszaadott bejegyzéseket.

## <a name="next-steps"></a>További lépések

A Azure Machine Learning virtuális hálózattal való használatával kapcsolatos további információkért lásd a [virtuális hálózat áttekintését](how-to-network-security-overview.md).
