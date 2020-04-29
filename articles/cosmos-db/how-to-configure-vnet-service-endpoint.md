---
title: Virtuális hálózat alapú hozzáférés konfigurálása Azure Cosmos-fiókhoz
description: Ez a dokumentum ismerteti a Azure Cosmos DB virtuális hálózati szolgáltatás végpontjának beállításához szükséges lépéseket.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mjbrown
ms.openlocfilehash: 442623880c1b95f3d7e038ae44832b74853d2c4a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80366243"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>Virtuális hálózatok (VNet) elérésének konfigurálása

Az Azure Cosmos DB-fiókok konfigurálhatók úgy, hogy csak egy Azure-beli virtuális hálózat adott alhálózatáról legyen engedélyezett a hozzáférés. Egy Azure Cosmos DB-fiókhoz való hozzáférés korlátozása egy virtuális hálózatban lévő alhálózattal létesített kapcsolattal:

1. Engedélyezze az alhálózatnak az alhálózat és a virtuális hálózat identitásának küldését Azure Cosmos DB. Ezt úgy érheti el, ha engedélyezi egy szolgáltatási végpontot az adott alhálózat Azure Cosmos DBához.

1. Adjon hozzá egy szabályt a Azure Cosmos DB-fiókban az alhálózat forrásként való megadásához, amely a fiók elérésére szolgál.

> [!NOTE]
> Ha a Azure Cosmos DB-fiókhoz tartozó szolgáltatási végpont engedélyezve van az alhálózaton, akkor a forgalom forrása Azure Cosmos DB a nyilvános IP-címekről egy virtuális hálózatra és alhálózatra vált. A forgalom váltás minden olyan Azure Cosmos DB-fiókra vonatkozik, amely ebből az alhálózatból elérhető. Ha a Azure Cosmos DB-fiókok IP-alapú tűzfallal engedélyezik ezt az alhálózatot, a szolgáltatással kompatibilis alhálózatról érkező kérések már nem felelnek meg az IP-tűzfalszabályok feltételeinek, és el lesznek utasítva.
>
> További információ: az [áttelepítés IP-tűzfallal szabályból a virtuális hálózati hozzáférés-vezérlési listára](#migrate-from-firewall-to-vnet) című szakasz lépései.

A következő szakaszok azt ismertetik, hogyan konfigurálható egy virtuális hálózati szolgáltatás végpontja egy Azure Cosmos DB-fiókhoz.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Szolgáltatási végpont konfigurálása a Azure Portal használatával

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Szolgáltatási végpont konfigurálása meglévő Azure-beli virtuális hálózathoz és alhálózathoz

1. A **minden erőforrás** panelen keresse meg a védeni kívánt Azure Cosmos db fiókot.

1. Válassza a **tűzfalak és virtuális hálózatok** lehetőséget a beállítások menüben, és válassza a hozzáférés engedélyezése a **kiválasztott hálózatokból**lehetőséget.

1. Ha hozzáférést szeretne biztosítani egy meglévő virtuális hálózat alhálózatához, a **virtuális hálózatok**területen válassza a **meglévő Azure-beli virtuális hálózat hozzáadása**elemet.

1. Válassza ki azt az **előfizetést** , amelyből hozzá szeretné adni az Azure-beli virtuális hálózatot. Válassza ki azokat az Azure-beli **virtuális hálózatokat** és **alhálózatokat** , amelyeknek hozzáférést kíván biztosítani a Azure Cosmos db-fiókjához. Ezután válassza az **Engedélyezés** lehetőséget a kiválasztott hálózatok engedélyezéséhez a "Microsoft. AzureCosmosDB" szolgáltatási végpontokkal. Ha elkészült, válassza a **Hozzáadás**lehetőséget.

   ![Válassza ki a virtuális hálózatot és az alhálózatot](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)

1. Ha a Azure Cosmos DB fiók engedélyezve van egy virtuális hálózatról való hozzáférésre, akkor csak a kiválasztott alhálózatról érkező forgalmat engedélyezi. A hozzáadott virtuális hálózatnak és alhálózatnak az alábbi képernyőképen látható módon kell megjelennie:

   ![A virtuális hálózat és az alhálózat sikeresen konfigurálva](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> A virtuális hálózati szolgáltatás végpontok engedélyezéséhez a következő előfizetési engedélyek szükségesek:
>   * Előfizetés virtuális hálózattal: hálózati közreműködő
>   * Előfizetés Azure Cosmos DB fiókkal: DocumentDB-fiók közreműködői
>   * Ha a virtuális hálózat és a Azure Cosmos DB fiók különböző előfizetésekben található, győződjön meg arról, hogy a virtuális hálózattal `Microsoft.DocumentDB` rendelkező előfizetéshez erőforrás-szolgáltató is van regisztrálva. Erőforrás-szolgáltató regisztrálásához tekintse meg az [Azure Resource Providers és types](../azure-resource-manager/management/resource-providers-and-types.md) című cikket.

Az előfizetés erőforrás-szolgáltatóval való regisztrálásának útmutatója.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Szolgáltatási végpont konfigurálása új Azure-beli virtuális hálózat és alhálózat számára

1. A **minden erőforrás** panelen keresse meg a védeni kívánt Azure Cosmos db fiókot.  

1. Válassza a **tűzfalak és az Azure-beli virtuális hálózatok** lehetőséget a beállítások menüben, és válassza a hozzáférés engedélyezése a **kiválasztott hálózatokból**lehetőséget.  

1. Ha hozzáférést szeretne biztosítani egy új Azure-beli virtuális hálózathoz, a **virtuális hálózatok**területen válassza az **új virtuális hálózat hozzáadása**lehetőséget.  

1. Adja meg az új virtuális hálózat létrehozásához szükséges adatokat, majd válassza a **Létrehozás**lehetőséget. Az alhálózat a "Microsoft. AzureCosmosDB" számára engedélyezett szolgáltatási végponttal lesz létrehozva.

   ![Válasszon ki egy virtuális hálózatot és alhálózatot egy új virtuális hálózat számára](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Ha a Azure Cosmos DB-fiókot más Azure-szolgáltatások, például az Azure Cognitive Search vagy a stream Analytics vagy a Power BI használatával érik el, akkor a hozzáférést a **globális Azure-adatközpontokban lévő kapcsolatok fogadása**lehetőség választásával engedélyezheti.

Annak biztosítása érdekében, hogy a portálon Azure Cosmos DB metrikák hozzáférhessenek, engedélyeznie kell a **hozzáférést a Azure Portal** lehetőségek közül. Ha többet szeretne megtudni ezekről a lehetőségekről, tekintse meg az [IP-tűzfal konfigurálása](how-to-configure-firewall.md) című cikket. A hozzáférés engedélyezése után válassza a **Mentés** lehetőséget a beállítások mentéséhez.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>Virtuális hálózat vagy alhálózat eltávolítása

1. A **minden erőforrás** panelen keresse meg azt a Azure Cosmos db fiókot, amelyhez hozzárendelte a szolgáltatási végpontokat.  

1. A beállítások menüben válassza a **tűzfalak és virtuális hálózatok** lehetőséget.  

1. Virtuális hálózat vagy alhálózat szabályának eltávolításához válassza a **...** lehetőséget a virtuális hálózat vagy alhálózat mellett, és válassza az **Eltávolítás**lehetőséget.

   ![Virtuális hálózat eltávolítása](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

1. A módosítások alkalmazásához kattintson a **Mentés** gombra.

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Szolgáltatási végpont konfigurálása Azure PowerShell használatával

> [!NOTE]
> Ha a PowerShellt vagy az Azure CLI-t használja, ügyeljen arra, hogy az IP-szűrők és a virtuális hálózati ACL-ek teljes listáját adja meg a paraméterekben, nem csak azokat, amelyeket hozzá kell adnia.

A következő lépésekkel konfigurálhatja a szolgáltatási végpontot egy Azure Cosmos DB-fiókra a Azure PowerShell használatával:  

1. Telepítse a [Azure PowerShellt](https://docs.microsoft.com/powershell/azure/install-Az-ps) , és [Jelentkezzen be](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Engedélyezze a szolgáltatási végpontot egy virtuális hálózat meglévő alhálózatához.  

   ```powershell
   $resourceGroupName = "<Resource group name>"
   $vnetName = "<Virtual network name>"
   $subnetName = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"
   $serviceEndpoint = "Microsoft.AzureCosmosDB"

   Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName | Set-AzVirtualNetworkSubnetConfig `
      -Name $subnetName `
      -AddressPrefix $subnetPrefix `
      -ServiceEndpoint $serviceEndpoint | Set-AzVirtualNetwork
   ```

1. Virtuális hálózati adatok beolvasása.

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Cosmos DB Virtual Network szabály előkészítése

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Azure Cosmos DB fiók tulajdonságainak frissítése az új Virtual Network Endpoint konfigurációval: 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Futtassa a következő parancsot annak ellenőrzéséhez, hogy a Azure Cosmos DB-fiókja frissítve lett-e az előző lépésben konfigurált Virtual Network Service-végponttal:

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a name="configure-a-service-endpoint-by-using-the-azure-cli"></a><a id="configure-using-cli"></a>Szolgáltatási végpont konfigurálása az Azure CLI használatával

Az Azure Cosmos-fiókok a szolgáltatás-végpontokhoz konfigurálhatók, amikor később jönnek létre vagy frissülnek, ha az alhálózat már konfigurálva van. A szolgáltatási végpontok akkor is engedélyezhetők a Cosmos-fiókon, ha az alhálózat még nincs konfigurálva, és a későbbiekben is működni fog, ha az alhálózat később van konfigurálva. Ez a rugalmasság lehetővé teszi azoknak a rendszergazdáknak, akik nem férnek hozzá a Cosmos-fiókhoz és a virtuális hálózati erőforrásokhoz, hogy a konfigurációk egymástól függetlenek legyenek.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>Új Cosmos-fiók létrehozása és egy új virtuális hálózat háttérbeli alhálózathoz való kapcsolódása

Ebben a példában a virtuális hálózatot és az alhálózatot olyan szolgáltatási végpontok hozzák létre, amelyek mind a létrehozáskor engedélyezve vannak.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet with service endpoints enabled for Cosmos DB
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values and service endpoints
az cosmosdb create \
   -n $accountName \
   -g $resourceGroupName \
   --enable-virtual-network true \
   --virtual-network-rules $svcEndpoint
```

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Cosmos-fiók összekapcsolása és konfigurálása a háttérbeli alhálózatokhoz egymástól függetlenül

Ebből a példából megtudhatja, hogyan csatlakoztatható egy Azure Cosmos-fiók egy meglévő új virtuális hálózathoz, ahol az alhálózat még nincs konfigurálva a szolgáltatási végpontokhoz. Ezt a `--ignore-missing-vnet-service-endpoint` paraméter használatával végezheti el. Ez lehetővé teszi, hogy a Cosmos-fiók konfigurációja hiba nélkül befejeződjön, mielőtt a virtuális hálózat alhálózatának konfigurációja befejeződik. Az alhálózat-konfiguráció befejezése után a Cosmos-fiók elérhető lesz a konfigurált alhálózaton keresztül.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet
# that is not yet enabled for service endpoints.

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet but without configuring service endpoints (--service-endpoints Microsoft.AzureCosmosDB)
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values
az cosmosdb create -n $accountName -g $resourceGroupName

# Add the virtual network rule but ignore the missing service endpoint on the subnet
az cosmosdb network-rule add \
   -n $accountName \
   -g $resourceGroupName \
   --virtual-network $vnetName \
   --subnet svcEndpoint \
   --ignore-missing-vnet-service-endpoint true

read -p'Press any key to now configure the subnet for service endpoints'

az network vnet subnet update \
   -n $backEnd \
   -g $resourceGroupName \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB
```

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>Áttelepítés IP-tűzfalszabály alapján virtuális hálózati ACL-re

Ha Azure Cosmos DB fiókot szeretne áttelepíteni az IP-tűzfalszabályok használatával a virtuális hálózati szolgáltatás végpontjának használatával, kövesse az alábbi lépéseket.

Ha egy alhálózathoz egy Azure Cosmos DB fiók van konfigurálva egy alhálózat szolgáltatási végpontja számára, a rendszer az adott alhálózat kérelmeit a forrás nyilvános IP-cím helyett a virtuális hálózattal és az alhálózati forrással kapcsolatos adatokkal Azure Cosmos DB küldi el. Ezek a kérések nem egyeznek meg a Azure Cosmos DB fiókban konfigurált IP-szűrővel, ezért az állásidő elkerüléséhez a következő lépések szükségesek.

A továbblépés előtt engedélyezze a Azure Cosmos DB szolgáltatási végpontot a virtuális hálózaton és az alhálózaton a "szolgáltatás végpontjának engedélyezése egy virtuális hálózat meglévő alhálózatához" című részében ismertetett lépéssel.

1. Virtuális hálózat és alhálózat adatainak beolvasása:

   ```powershell
   $resourceGroupName = "myResourceGroup"
   $accountName = "mycosmosaccount"
   $vnetName = "myVnet"
   $subnetName = "mySubnet"

   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Új Virtual Network szabály objektum előkészítése a Azure Cosmos DB-fiókhoz:

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Frissítse a Azure Cosmos DB fiókot a szolgáltatás-végponti hozzáférés engedélyezéséhez az alhálózatról:

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Ismételje meg az előző lépéseket az alhálózatról elért összes Azure Cosmos DB fióknál.

1. Távolítsa el az alhálózathoz tartozó IP-tűzfalszabály szabályát a Azure Cosmos DB fiók tűzfalszabály-szabályai alapján.

## <a name="next-steps"></a>További lépések

* Ha Azure Cosmos DB tűzfalat szeretne konfigurálni, tekintse meg a [tűzfal támogatását](firewall-support.md) ismertető cikket.
