---
title: Azure DDoS Protection terv létrehozása és konfigurálása az Azure CLI használatával
description: Megtudhatja, hogyan hozhat létre DDoS Protection tervet az Azure CLI használatával
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 0307fcee207f045c2808b3c66e9911623391d486
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905483"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-cli"></a>Rövid útmutató: Azure DDoS Protection standard létrehozása és konfigurálása az Azure CLI-vel

Ismerkedjen meg Azure DDoS Protection standard szintű Azure CLI használatával. 

A DDoS Protection-csomag olyan virtuális hálózatokat határoz meg, amelyeken engedélyezve van a DDoS Protection standard, az előfizetések között. Beállíthat egy DDoS Protection-tervet a szervezet számára, és a virtuális hálózatokat több előfizetésből ugyanahhoz a csomaghoz kapcsolhatja. 

Ebben a rövid útmutatóban létrehoz egy DDoS Protection-tervet, és összekapcsolja azt egy virtuális hálózattal. 

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Helyileg telepített Azure CLI vagy Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="create-a-ddos-protection-plan"></a>DDoS Protection terv létrehozása

Az Azure-ban kapcsolódó erőforrásokat oszt ki egy erőforráscsoporthoz. Használhat meglévő erőforráscsoportot, vagy létrehozhat egy újat.

Erőforráscsoport létrehozásához használja [az az Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create&preserve-view=true)paranccsal. Ebben a példában az erőforráscsoport _MyResourceGroup_ nevet fogjuk használni, és az _USA keleti_ régióját használjuk:

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus
```

Most hozzon létre egy _MyDdosProtectionPlan_ nevű DDoS Protection-csomagot:

```azurecli-interactive
az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

## <a name="enable-ddos-protection-for-a-virtual-network"></a>DDoS-védelem engedélyezése virtuális hálózathoz

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>DDoS-védelem engedélyezése új virtuális hálózat esetén

A DDoS Protection a virtuális hálózatok létrehozásakor engedélyezhető. Ebben a példában a virtuális hálózati _MyVnet_ fogjuk elnevezni: 

```azurecli-interactive
az network vnet create \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --location eastus \
    --ddos-protection true
```

A virtuális hálózat nem helyezhető át másik erőforráscsoporthoz vagy előfizetésbe, ha a DDoS standard engedélyezve van a virtuális hálózathoz. Ha a virtuális hálózatot a DDoS standard használatával kell áthelyeznie, először tiltsa le a DDoS standardot, helyezze át a virtuális hálózatot, majd engedélyezze a DDoS standard használatát. Az áthelyezést követően a rendszer alaphelyzetbe állítja a virtuális hálózatban lévő összes védett nyilvános IP-cím automatikusan beállított szabályzatának küszöbértékeit.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>DDoS-védelem engedélyezése meglévő virtuális hálózat esetén

[A DDoS elleni védelmi terv létrehozásakor](#create-a-ddos-protection-plan)egy vagy több virtuális hálózatot is hozzárendelhet a csomaghoz. Több virtuális hálózat hozzáadásához egyszerűen sorolja fel a neveket vagy az azonosítókat, szóközzel elválasztva. Ebben a példában a következő _MyVnet_ vesszük fel:

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
    --vnet MyVnet
```

Azt is megteheti, hogy egy adott virtuális hálózat esetében engedélyezte a DDoS-védelmet:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --vnet MyVnet \
    --ddos-protection true
```

## <a name="validate-and-test"></a>Ellenőrzés és tesztelés

Először olvassa el a DDoS Protection-terv részleteit:

```azurecli-interactive
az network ddos-protection show \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

Ellenőrizze, hogy a parancs visszaadja-e a DDoS Protection-terv helyes részleteit.

Tesztelés szimulációk használatával

## <a name="clean-up-resources"></a>Az erőforrások felszabadítása

A következő oktatóanyagban megtarthatja az erőforrásait. Ha már nincs rá szükség, törölje a _MyResourceGroup_ erőforráscsoportot. Az erőforráscsoport törlésekor a DDoS elleni védelmi tervet és az ahhoz kapcsolódó összes erőforrást is törli. 

Az erőforráscsoport törléséhez használja az [az Group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az_group_delete&preserve-view=true):

```azurecli-interactive
az group delete \
--name MyResourceGroup 
```

Egy adott virtuális hálózat frissítése a DDoS-védelem letiltásához:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --vnet MyVnet \
    --ddos-protection false
```

Ha törölni szeretné a DDoS Protection-csomagot, először el kell távolítania az összes virtuális hálózatot. 

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan tekintheti meg és konfigurálhatja a DDoS elleni védelmi terv telemetria, folytassa az oktatóanyagokkal.

> [!div class="nextstepaction"]
> [A DDoS Protection telemetria megtekintése és konfigurálása](telemetry-monitoring-alerting.md)