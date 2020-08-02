---
title: A nyilvános IP-cím útválasztási beállításainak konfigurálása az Azure CLI használatával
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogyan hozhat létre egy nyilvános IP-címet egy internetes forgalom útválasztási beállításával
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.custom: devx-track-azurecli
ms.openlocfilehash: 64284b198fc76c219ffe0dfbc57461b587b23130
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87504602"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-cli"></a>A nyilvános IP-cím útválasztási beállításainak konfigurálása az Azure CLI használatával

Ez a cikk bemutatja, hogyan konfigurálhatja az útválasztási beállításokat a nyilvános IP-címekre vonatkozó ISP-hálózaton (**Internet** -beállítás) az Azure CLI használatával. A nyilvános IP-cím létrehozása után a következő Azure-erőforrásokkal társíthatja a bejövő és kimenő adatforgalmat az internethez:

* virtuális gép
* Virtuálisgép-méretezési csoport
* Azure Kubernetes Service (AKS)
* Internetkapcsolattal rendelkező Load Balancer
* Application Gateway
* Azure Firewall

Alapértelmezés szerint a nyilvános IP-cím útválasztási beállítása az összes Azure-szolgáltatáshoz a Microsoft globális hálózatra van állítva, és bármely Azure-szolgáltatáshoz társítható.

> [!IMPORTANT]
> Az útválasztási preferencia jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
Ha az Azure CLI helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.49 vagy újabb verzióját kell használnia. A telepített verziójának megkereséséhez futtassa a parancsot `az --version` . További információ: az [Azure CLI telepítése](/cli/azure/install-azure-cli) a telepítéshez vagy a frissítéshez.

## <a name="register-the-feature-for-your-subscription"></a>Az előfizetéshez tartozó funkció regisztrálása
Az útválasztási preferencia szolgáltatás jelenleg előzetes verzióban érhető el. Regisztrálja az előfizetéshez tartozó szolgáltatást a következőképpen:
```azurecli
az feature register --namespace Microsoft.Network --name AllowRoutingPreferenceFeature
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. Az alábbi példa egy erőforráscsoportot hoz létre az **USA keleti** régiója Azure-régióban:

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

Hozzon létre egy nyilvános IP-címet az "Internet" típusú útválasztási beállítással az az [Network Public-IP Create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)paranccsal, az alábbi formátumban.

A következő parancs egy új nyilvános IP-címet hoz létre az **Internet** Routing PREFERENCIA az **USA keleti** régiója Azure-régióban.

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

> [!NOTE]
>  Az útválasztási beállítások jelenleg csak IPV4 nyilvános IP-címeket támogatnak.

A fenti létrehozott nyilvános IP-címet egy Windows vagy [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [rendszerű](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális géppel társíthatja. A CLI szakasz az oktatóanyag oldalon: [nyilvános IP-cím hozzárendelése egy virtuális géphez](associate-public-ip-address-vm.md#azure-cli) , hogy a nyilvános IP-címet a virtuális géphez rendelje. A fent létrehozott nyilvános IP-címet társíthatja egy [Azure Load Balancer](../load-balancer/load-balancer-overview.md)is, ha hozzárendeli a terheléselosztó előtér **-konfigurációjához.** A nyilvános IP-cím terheléselosztásos virtuális IP-címként (VIP) szolgál majd.

## <a name="next-steps"></a>További lépések

- További információ az [útválasztási beállításokról a nyilvános IP-címekben](routing-preference-overview.md). 
- [Konfigurálja a virtuális gép útválasztási beállításait az Azure CLI használatával](configure-routing-preference-virtual-machine-cli.md).

