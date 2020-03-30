---
title: Virtuális hálózati hozzáférés konfigurálása Egy Azure Cosmos-fiókhoz
description: Ez a dokumentum ismerteti az Azure Cosmos DB virtuális hálózati szolgáltatásvégpontjának beállításához szükséges lépéseket.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mjbrown
ms.openlocfilehash: 442623880c1b95f3d7e038ae44832b74853d2c4a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366243"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>Hozzáférés konfigurálása virtuális hálózatokból (VNet)

Az Azure Cosmos DB-fiókok konfigurálhatók úgy, hogy csak egy Azure-beli virtuális hálózat adott alhálózatáról legyen engedélyezett a hozzáférés. Az Azure Cosmos DB-fiókhoz való hozzáférés korlátozása egy virtuális hálózat alhálózatából származó kapcsolatokkal:

1. Engedélyezze az alhálózat ot az alhálózat és a virtuális hálózati identitás elküldéséhez az Azure Cosmos DB-nek. Ezt úgy érheti el, hogy engedélyezi az Azure Cosmos DB szolgáltatásvégpontját az adott alhálózaton.

1. Adjon hozzá egy szabályt az Azure Cosmos DB-fiókban, hogy adja meg az alhálózatot forrásként, amelyből a fiók elérhető.

> [!NOTE]
> Ha az Azure Cosmos DB-fiók szolgáltatásvégpontja engedélyezve van egy alhálózaton, az Azure Cosmos DB-t elérő forgalom forrása nyilvános IP-címről virtuális hálózatra és alhálózatra vált. A forgalomváltás minden olyan Azure Cosmos DB-fiókra vonatkozik, amely ebből az alhálózatból érhető el. Ha az Azure Cosmos DB-fiókjai rendelkeznek egy IP-alapú tűzfallal az alhálózat engedélyezéséhez, a szolgáltatás-képes alhálózattól érkező kérelmek már nem felelnek meg az IP tűzfalszabályainak, és a rendszer elutasítja őket.
>
> További információ: Az [IP-tűzfalszabályról a virtuális hálózati hozzáférés-vezérlési listára](#migrate-from-firewall-to-vnet) való áttelepítés e cikkben ismertetett lépések.

Az alábbi szakaszok ismertetik, hogyan konfigurálhatja a virtuális hálózati szolgáltatás végpont egy Azure Cosmos DB-fiók.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Szolgáltatásvégpont konfigurálása az Azure Portal használatával

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Szolgáltatásvégpont konfigurálása meglévő Azure virtuális hálózathoz és alhálózathoz

1. Az **Összes erőforrás** panelen keresse meg a biztonságosAzure Cosmos DB-fiókot.

1. A Beállítások menüben válassza a **Tűzfalak és a virtuális hálózatok** lehetőséget, és válassza a kijelölt **hálózatokról**való hozzáférést.

1. Egy meglévő virtuális hálózat alhálózatához való hozzáférés engedélyezéséhez a **Virtuális hálózatok**csoportban válassza a Meglévő Azure virtuális **hálózat hozzáadása**lehetőséget.

1. Válassza ki azt az **előfizetést,** amelyből azure-beli virtuális hálózatot szeretne hozzáadni. Válassza ki az Azure **Virtuális hálózatok** és **alhálózatok,** amelyek hozzáférést szeretne biztosítani az Azure Cosmos DB-fiók. Ezután válassza **az Engedélyezés** lehetőséget a "Microsoft.AzureCosmosDB" szolgáltatásvégpontokkal rendelkező kiválasztott hálózatok engedélyezéséhez. Ha elkészült, válassza a **Hozzáadás gombot.**

   ![Virtuális hálózat és alhálózat kiválasztása](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)

1. Miután az Azure Cosmos DB-fiók engedélyezve van a virtuális hálózatról való hozzáféréshez, csak a kiválasztott alhálózatból engedélyezi a forgalmat. A hozzáadott virtuális hálózatnak és alhálózatnak a következő képernyőképen látható módon kell megjelennie:

   ![A virtuális hálózat és az alhálózat sikeresen konfigurálva](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> A virtuális hálózati szolgáltatás végpontjainak engedélyezéséhez a következő előfizetési engedélyekre van szükség:
>   * Előfizetés virtuális hálózattal: Hálózati közreműködő
>   * Előfizetés Az Azure Cosmos DB-fiókkal: DocumentDB-fiók közreműködője
>   * Ha a virtuális hálózat és az Azure Cosmos DB-fiók különböző előfizetések, `Microsoft.DocumentDB` győződjön meg arról, hogy a virtuális hálózatot tartalmazó előfizetés is rendelkezik erőforrás-szolgáltató regisztrált. Erőforrás-szolgáltató regisztrálásához tekintse meg [az Azure-erőforrás-szolgáltatók és típusok](../azure-resource-manager/management/resource-providers-and-types.md) cikk.

Az alábbiakban az előfizetés erőforrás-szolgáltatóval való regisztrálásához szükséges utasításokat kell fel.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Szolgáltatásvégpont konfigurálása új Azure virtuális hálózathoz és alhálózathoz

1. Az **Összes erőforrás** panelen keresse meg a biztonságosAzure Cosmos DB-fiókot.  

1. A beállítások menüben válassza a **Tűzfalak és az Azure virtuális hálózatok** lehetőséget, és válassza a kijelölt **hálózatokról**való hozzáférést.  

1. Új Azure virtuális hálózathoz való hozzáférés engedélyezéséhez a **Virtuális hálózatok**csoportban válassza az Új virtuális **hálózat hozzáadása**lehetőséget.  

1. Adja meg az új virtuális hálózat létrehozásához szükséges részleteket, majd válassza a **Létrehozás gombot.** Az alhálózat a "Microsoft.AzureCosmosDB" szolgáltatásvégpontjával jön létre.

   ![Virtuális hálózat és alhálózat kiválasztása új virtuális hálózathoz](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Ha az Azure Cosmos DB-fiókját más Azure-szolgáltatások, például az Azure Cognitive Search is használják, vagy a Stream Analytics vagy a Power BI szolgáltatásból érhető el, a **globális Azure-adatközpontokból származó kapcsolatok elfogadása**lehetőség kiválasztásával engedélyezheti a hozzáférést.

Annak érdekében, hogy hozzáférhessen az Azure Cosmos DB-metrikáihoz a portálról, engedélyeznie kell a **hozzáférés engedélyezése az Azure Portal beállításaiból.** Ezekről a beállításokról az [IP-tűzfal konfigurálása](how-to-configure-firewall.md) című cikkben olvashat bővebben. Miután engedélyezte a hozzáférést, a Beállítások mentéséhez válassza a **Mentés** gombot.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>Virtuális hálózat vagy alhálózat eltávolítása

1. Az **Összes erőforrás** panelen keresse meg az Azure Cosmos DB-fiókot, amelyhez szolgáltatásvégpontokat rendelt.  

1. Válassza a Beállítások menü **Tűzfalak és virtuális hálózatok** parancsát.  

1. Virtuális hálózati vagy alhálózati szabály eltávolításához válassza **a ...** lehetőséget a virtuális hálózat vagy alhálózat mellett, majd az **Eltávolítás gombra.**

   ![Virtuális hálózat eltávolítása](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

1. A módosítások alkalmazásához kattintson a **Mentés** gombra.

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Szolgáltatásvégpont konfigurálása az Azure PowerShell használatával

> [!NOTE]
> PowerShell vagy az Azure CLI használata esetén ügyeljen arra, hogy adja meg az IP-szűrők és a virtuális hálózati ACL-ek teljes listáját a paraméterekben, nem csak azokat, amelyeket hozzá kell adni.

Az alábbi lépésekkel konfigurálhat egy szolgáltatásvégpontot egy Azure Cosmos DB-fiókhoz az Azure PowerShell használatával:  

1. Telepítse az [Azure PowerShellt,](https://docs.microsoft.com/powershell/azure/install-Az-ps) és [jelentkezzen be.](https://docs.microsoft.com/powershell/azure/authenticate-azureps)  

1. Engedélyezze a szolgáltatásvégpontot egy virtuális hálózat meglévő alhálózatához.  

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

1. Virtuális hálózati információk beszerezése.

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Cosmos DB virtuális hálózati szabály előkészítése

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Frissítse az Azure Cosmos DB-fiók tulajdonságait az új virtuális hálózati végpontkonfigurációval: 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Futtassa a következő parancsot annak ellenőrzéséhez, hogy az Azure Cosmos DB-fiókja frissült-e az előző lépésben konfigurált virtuális hálózati szolgáltatás-végpontdal:

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a name="configure-a-service-endpoint-by-using-the-azure-cli"></a><a id="configure-using-cli"></a>Szolgáltatásvégpont konfigurálása az Azure CLI használatával

Az Azure Cosmos-fiókok konfigurálhatók a szolgáltatás végpontokhoz, amikor azok létrehozása vagy frissítése egy későbbi időpontban, ha az alhálózat már konfigurálva van. A szolgáltatásvégpontok is engedélyezhetők a Cosmos-fiókban, ahol az alhálózat még nincs konfigurálva, és majd az alhálózat későbbi konfigurálásakor megkezdi a munkát. Ez a rugalmasság lehetővé teszi, hogy a rendszergazdák, akik nem férnek hozzá sem a Cosmos-fiók és a virtuális hálózati erőforrásokat, hogy a konfigurációk egymástól független.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>Hozzon létre egy új Cosmos-fiókot, és csatlakoztassa egy új virtuális hálózat háttérrendszer-alhálózatához

Ebben a példában a virtuális hálózat és az alhálózat jön létre a szolgáltatás végpontok engedélyezve van mind a létrehozásukkor.

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

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Cosmos-fiók csatlakoztatása és konfigurálása háttér-alhálózathoz egymástól függetlenül

Ez a minta azt mutatja be, hogyan csatlakoztathat egy Azure Cosmos-fiókot egy meglévő új virtuális hálózathoz, ahol az alhálózat még nincs konfigurálva a szolgáltatásvégpontokhoz. Ez a `--ignore-missing-vnet-service-endpoint` paraméter használatával történik. Ez lehetővé teszi, hogy a Cosmos-fiók konfigurációja hiba nélkül befejeződjen, mielőtt a virtuális hálózat alhálózatának konfigurációja befejeződne. Az alhálózati konfiguráció befejezése után a Cosmos-fiók ezután elérhető lesz a konfigurált alhálózaton keresztül.

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

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>Áttérés IP-tűzfalszabályról virtuális hálózati acl-re

Az Azure Cosmos DB-fiók átáttelepítéséhez ip-tűzfalszabályok használatával a virtuális hálózati szolgáltatás végpontjainak használatával, kövesse az alábbi lépéseket.

Miután egy Azure Cosmos DB-fiók konfigurálva van egy alhálózat szolgáltatásvégpontjára, az adott alhálózatból érkező kérelmeket az Azure Cosmos DB virtuális hálózati és alhálózati forrásadatokkal küldi el a forrás nyilvános IP-cím helyett. Ezek a kérelmek már nem egyezik meg az Azure Cosmos DB-fiókban konfigurált IP-szűrővel, ezért a következő lépések szükségesek az állásidő elkerülése érdekében.

Folytatás előtt engedélyezze az Azure Cosmos DB szolgáltatás végpontját a virtuális hálózaton és az alhálózaton a "Virtuális hálózat meglévő alhálózatának szolgáltatásvégpontjának engedélyezése" című fenti lépés használatával.

1. Virtuális hálózat- és alhálózati információk beszerezése:

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

1. Készítsen elő egy új virtuális hálózati szabályobjektumot az Azure Cosmos DB-fiókhoz:

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Frissítse az Azure Cosmos DB-fiókot a szolgáltatásvégpont-hozzáférés engedélyezéséhez az alhálózatról:

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Ismételje meg az előző lépéseket az alhálózatról elért összes Azure Cosmos DB-fiókesetében.

1. Távolítsa el az alhálózat IP-tűzfalszabályát az Azure Cosmos DB-fiók tűzfalszabályaiból.

## <a name="next-steps"></a>További lépések

* Az Azure Cosmos DB tűzfalának konfigurálásához tekintse meg a [firewall támogatási](firewall-support.md) cikket.
