---
title: Virtuális hálózat és alhálózat-alapú hozzáférés az Azure Cosmos DB-fiók konfigurálása
description: Ez a dokumentum az Azure Cosmos DB egy virtuális hálózati szolgáltatásvégpont beállításához szükséges lépéseket ismerteti.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: efe85e017d883ca82414fe5ff10b5c2932366e48
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101125"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>Hozzáférés a virtuális hálózatok (VNet) konfigurálása

Konfigurálhatja az Azure Cosmos DB fiókok használatával engedélyezze a hozzáférést az Azure-beli virtuális hálózathoz csak egy adott alhálózatról. Az Azure Cosmos DB-fiókot, a kapcsolatok hozzáférés korlátozására a virtuális hálózatban lévő alhálózat:
 
1. Engedélyezze az alhálózat és virtuális hálózati azonosságának küldése az Azure Cosmos DB az alhálózat. Ez egy szolgáltatásvégpont engedélyezése az Azure Cosmos DB az adott alhálózat szerint érheti el.

1. Adja hozzá a szabály az Azure Cosmos DB-fiókot, adja meg az alhálózatot, amelyből a fiókhoz elérhető forrásként.

> [!NOTE]
> Az Azure Cosmos DB-fiók egy szolgáltatásvégpont engedélyezésekor egy alhálózaton, amely az Azure Cosmos DB eléri a forgalom forrását a nyilvános IP-címről vált, amennyiben egy virtuális hálózatot és alhálózatot. A forgalom váltás bármilyen Azure Cosmos DB-fiókot az alhálózatról elért vonatkozik. Ha az Azure Cosmos DB-fiókokhoz, hogy ez az alhálózat egy IP-alapú tűzfal, a szolgáltatás engedélyezve van az alhálózatról kérelmek már nem felel meg az IP-tűzfalszabályainak, és azokat Ön elutasította. 
>
> További tudnivalókért tekintse meg a leírt lépéseket a [áttelepítés a virtuális hálózati hozzáférés-vezérlési lista IP-tűzfalszabály](#migrate-from-firewall-to-vnet) című szakaszát. 

A következő szakaszok ismertetik az Azure Cosmos DB-fiók egy virtuális hálózati szolgáltatásvégpont beállítása.

## <a id="configure-using-portal"></a>Egy szolgáltatásvégpont konfigurálása az Azure portal használatával

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>A meglévő Azure virtuális hálózat és alhálózat szolgáltatásvégpont beállítása

1. Az a **összes erőforrás** panelen, és keresse meg az Azure Cosmos DB-fiók, amely szeretné biztonságossá tenni.

1. Válassza ki **tűzfalak és virtuális hálózatok** a beállítások menüben, és engedélyezze a hozzáférést **kiválasztott hálózatok**.

1. Egy meglévő virtuális hálózat alhálózatán, hozzáférés adható a **virtuális hálózatok**, jelölje be **meglévő Azure virtuális hálózat hozzáadása**.

1. Válassza ki a **előfizetés** , amely egy Azure virtual network hozzáadni kívánt. Válassza ki az Azure **virtuális hálózatok** és **alhálózatok** , hogy szeretné-e az Azure Cosmos DB-fiók eléréséhez. Majd **engedélyezése** kiválasztott hálózatokon a Szolgáltatásvégpontok engedélyezése "Microsoft.AzureCosmosDB". Ha elkészült, válassza ki a **Hozzáadás**. 

   ![Válassza ki a virtuális hálózatot és alhálózatot](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)


1. Miután az Azure Cosmos DB-fiók engedélyezve van a virtuális hálózatról a hozzáférést, csak a kiválasztott alhálózat forgalmát engedélyezi. A virtuális hálózatot és alhálózatot, amelyet hozzáadott meg kell jelennie az alábbi képernyőképen látható módon:

   ![virtuális hálózat és alhálózat sikeresen konfigurálva](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Ahhoz, hogy a virtuális hálózati Szolgáltatásvégpontok, a következő előfizetés-engedélyek szükségesek:
>   * Előfizetés a virtual network szolgáltatással: Hálózati közreműködő
>   * Előfizetés az Azure Cosmos DB-fiók: A DocumentDB-fiókközreműködő
>   * Ha különböző előfizetésekhez tartoznak a virtuális hálózat és az Azure Cosmos DB-fiókot, ellenőrizze, hogy a virtuális hálózatot tartalmazó előfizetést is `Microsoft.DocumentDB` erőforrás-szolgáltató regisztrálva. Erőforrás-szolgáltató regisztrálásához lásd [Azure-erőforrás-szolgáltatókat és típusaikat](../azure-resource-manager/resource-manager-supported-services.md) cikk. 

Az alábbiakban az előfizetés regisztrálása az erőforrás-szolgáltató utasításait.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Egy új Azure virtuális hálózat és alhálózat szolgáltatásvégpont beállítása

1. Az a **összes erőforrás** panelen, és keresse meg az Azure Cosmos DB-fiók, amely szeretné biztonságossá tenni.  

1. Válassza ki **tűzfalak és az Azure virtuális hálózatok** a beállítások menüben, és engedélyezze a hozzáférést **kiválasztott hálózatok**.  

1. Hozzáférést kell adni egy új Azure virtuális hálózat alatt **virtuális hálózatok**válassza **hozzáadása új virtuális hálózat**.  

1. Adja meg a hozzon létre egy új virtuális hálózatot, és válassza ki a szükséges adatokat **létrehozás**. Az alhálózat "Microsoft.AzureCosmosDB" engedélyezett szolgáltatásvégponttal jön.

   ![Egy virtuális hálózatot és alhálózatot egy új virtuális hálózat kiválasztása](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Az Azure Cosmos DB-fiók más Azure-szolgáltatásokhoz hasonlóan az Azure Search által használt, vagy a Stream analytics vagy a Power bi-ban érhető el, ha engedélyezi a hozzáférést kiválasztásával **belül nyilvános Azure-adatközpontok kapcsolatokat fogadjon**.

Gondoskodjon arról, hogy hozzáférést az Azure Cosmos DB-metrikák a portálról, engedélyeznie kell **engedélyezze a hozzáférést az Azure portal** beállítások. Ezek a beállítások kapcsolatos további információkért tekintse meg a [egy IP-tűzfal konfigurálása](how-to-configure-firewall.md) cikk. Miután engedélyezte a hozzáférést, válassza ki a **mentése** a beállítások mentéséhez.

## <a id="remove-vnet-or-subnet"></a>Egy virtuális hálózat vagy alhálózat törlése 

1. Az a **összes erőforrás** panelen, és keresse meg az Azure Cosmos DB-fiók, amelynek a Szolgáltatásvégpontok hozzárendelve.  

2. Válassza ki **tűzfalak és virtuális hálózatok** beállítások menüjében.  

3. Egy virtuális hálózat vagy alhálózat szabály eltávolításához jelölje ki **...**  mellett a virtuális hálózat vagy alhálózat, és válassza **eltávolítása**.

   ![Virtuális hálózat eltávolítása](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4.  Válassza ki **mentése** alkalmazza a módosításokat.

## <a id="configure-using-powershell"></a>Egy szolgáltatásvégpont konfigurálása az Azure PowerShell-lel

> [!NOTE]
> Ha a PowerShell vagy az Azure CLI használata esetén mindenképpen adja meg az IP-szűrők és a virtuális hálózati hozzáférés-vezérlési listák teljes listáját a paraméterek, nem csak az címkéket is hozzá kell adni.

Használja az alábbi lépéseket egy végpontot egy Azure Cosmos DB-fiók konfigurálása az Azure PowerShell használatával:  

1. Telepítés [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) és [jelentkezzen be a](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. A virtuális hálózat egy meglévő alhálózat szolgáltatásvégpont engedélyezése.  

   ```powershell
   $rgname = "<Resource group name>"
   $vnName = "<Virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

1. Virtuális hálózat adatai olvashatók be.

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName `
     -ResourceGroupName $rgName
   ```

1. Az Azure Cosmos DB-fiók tulajdonságainak beolvasása a következő parancsmagot:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. A későbbi felhasználás céljára változók inicializálása. Beállítani az összes változót a meglévő fiók-definíciót.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $virtualNetworkRules = @(@{
      id = "$($vnProp.Id)/subnets/$sname";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. Azure Cosmos DB-fiók tulajdonságainak frissítése az új konfigurációval a következő parancsmag futtatásával: 

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName `
     -Properties $CosmosDBProperties
   ```

1. Győződjön meg arról, hogy a virtuális hálózati szolgáltatásvégpontot az előző lépésben beállított frissül az Azure Cosmos DB-fiókot az alábbi parancsot futtassa:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>Egy szolgáltatásvégpont konfigurálása az Azure CLI használatával 

1. A virtuális hálózatban lévő alhálózat szolgáltatásvégpont engedélyezése.

1. Frissítse a meglévő Azure Cosmos DB-fiók alhálózati hozzáférés-vezérlési listák (ACL).

   ```azurecli-interactive

   name="<Azure Cosmos DB account name>"
   resourceGroupName="<Resource group name>"

   az cosmosdb update \
    --name $name \
    --resource-group $resourceGroupName \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/frontend"
   ```

1. Hozzon létre egy új Azure Cosmos DB-fiókot alhálózati ACL-ek.

   ```azurecli-interactive
   az cosmosdb create \
    --name $name \
    --kind GlobalDocumentDB \
    --resource-group $resourceGroupName \
    --max-interval 10 \
    --max-staleness-prefix 200 \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/default"
   ```

## <a id="migrate-from-firewall-to-vnet"></a>Áttelepítés a virtuális hálózat ACL-t egy IP-tűzfalszabály 

Kövesse az alábbi lépéseket csak az Azure Cosmos DB-fiókokat a meglévő IP-tűzfalszabályainak, amelyek lehetővé teszik egy alhálózatot, ha meg szeretné használni a virtuális hálózat és alhálózat-alapú hozzáférés-vezérlési helyett egy IP-tűzfalszabály.

Miután egy végpontot egy Azure Cosmos DB-fiók egy alhálózathoz van kapcsolva, a kérelmek egy nyilvános IP-cím helyett virtuális hálózatot és alhálózatot információt tartalmazó adatforrás lesznek elküldve. Ezek a kérelmek egy IP-szűrő nem egyezik. A forrás kapcsoló hoz engedélyezett szolgáltatásvégponttal rendelkező az alhálózatról elért összes Azure Cosmos DB-fiókok esetében történik. Állásidő elkerülése érdekében használja az alábbi lépéseket:

1. Az Azure Cosmos DB-fiók tulajdonságainak beolvasása a következő parancsmagot:

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. A későbbi használat céljából változók inicializálása. Beállítani az összes változót a meglévő fiók-definíciót. A virtuális hálózat ACL hozzáadása az alhálózatról elért összes Azure Cosmos DB-fiók `ignoreMissingVNetServiceEndpoint` jelzőt.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $subnetID = "Subnet ARM URL" e.g "/subscriptions/f7ddba26-ab7b-4a36-a2fa-7d01778da30b/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/subnet1"

   $virtualNetworkRules = @(@{
      id = $subnetID;
      ignoreMissingVNetServiceEndpoint = "True";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. Azure Cosmos DB-fiók tulajdonságainak frissítése az új konfigurációval a következő parancsmag futtatásával:

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzureRmResource `
      -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
      -ApiVersion $apiVersion `
      -ResourceGroupName $rgName `
      -Name $acctName `
      -Properties $CosmosDBProperties
   ```

1. 1 – 3. lépést ismételje meg az alhálózatról elérhető összes Azure Cosmos DB-fiók.

1.  Várjon 15 percig, és frissítse az alhálózat a szolgáltatásvégpont engedélyezése.

1.  A virtuális hálózat egy meglévő alhálózat szolgáltatásvégpont engedélyezése.

    ```powershell
    $rgname= "<Resource group name>"
    $vnName = "<virtual network name>"
    $sname = "<Subnet name>"
    $subnetPrefix = "<Subnet address range>"

    Get-AzureRmVirtualNetwork `
       -ResourceGroupName $rgname `
       -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
       -Name $sname `
       -AddressPrefix $subnetPrefix `
       -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
    ```

1. Távolítsa el az alhálózat IP-tűzfalszabály.

## <a name="next-steps"></a>További lépések

* Tűzfal beállítása az Azure Cosmos DB: a [tűzfal-támogatás](firewall-support.md) cikk.
