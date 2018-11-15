---
title: Virtuális hálózat és alhálózat-alapú hozzáférés az Azure Cosmos-fiók konfigurálása
description: Ez a dokumentum ismerteti az Azure Cosmos DB-virtuális hálózati szolgáltatásvégpont beállítása szükséges lépéseket.
author: kanshiG
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: a7c2d1e41fa4ac26854e2e6ab57184cd6ed0bd0c
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633682"
---
# <a name="how-to-access-azure-cosmos-db-resources-from-virtual-networks"></a>A virtuális hálózatok az Azure Cosmos DB-erőforrások elérése

Az Azure cosmos DB-fiókokhoz beállítható úgy, hogy engedélyezze a hozzáférést csak adott, az Azure virtuális hálózat alhálózatán. A hozzáférés korlátozásához kapcsolatok az Azure Cosmos-fiókjába egy alhálózatról egy virtuális hálózaton (VNET) szükséges két lépésből áll.
 
1. Az alhálózat küldése az alhálózat és a VNET identitását az Azure Cosmos DB engedélyezése. Ez a szolgáltatásvégpont engedélyezése az Azure Cosmos DB az adott alhálózat érheti el.

1. Adjon meg egy szabályt az alhálózat-t egy forrást a, adja meg az Azure Cosmos-fiókban a fiókhoz elérhető lesz.

> [!NOTE]
> Egyszer, a fiók engedélyezve van egy alhálózatot az Azure Cosmos szolgáltatásvégpontját a Azure Cosmos DB elérése a forgalom forrását a nyilvános IP-címről vált, amennyiben az virtuális Hálózatot és alhálózatot. A forgalom váltás vonatkozik az alhálózatról elért összes Azure Cosmos-fiók. Ha az Azure Cosmos (ok), hogy ez az alhálózat IP-alapú tűzfal, kérelmek szolgáltatás engedélyezve van az alhálózat nem felel meg a IP-tűzfalszabályainak, és azokat a rendszer elutasítja. További tudnivalókért lásd: lépéseinek [áttelepítés a virtuális hálózati hozzáférés-vezérlési lista IP-tűzfalszabály](#migrate-from-firewall-to-vnet) című szakaszát. 

A következő szakaszok ismertetik, hogyan konfigurálhatja a virtuális hálózati szolgáltatásvégpontot egy Azure Cosmos DB-fiók.

## <a id="configure-using-portal"></a>Szolgáltatásvégpont konfigurálása az Azure portal használatával

### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>A meglévő Azure virtuális hálózat és alhálózat szolgáltatásvégpont beállítása

1. A **összes erőforrás** panelen keresse meg az Azure Cosmos-fiók szeretné biztonságossá tételéhez.

1. Válassza ki **tűzfalak és virtuális hálózatok** beállítások menüjében, majd engedélyezze a hozzáférést **kiválasztott hálózatok**.

1. Egy meglévő virtuális hálózat alhálózatán, hozzáférés adható a **virtuális hálózatok**, jelölje be **meglévő Azure virtuális hálózat hozzáadása**.

1. Válassza ki a **előfizetés** , amely az Azure virtual network hozzáadni kívánt. Válassza ki az Azure **virtuális hálózatok** és **alhálózatok** , amelyeket meg szeretne-e az Azure Cosmos-fiók eléréséhez. Ezután válassza ki **engedélyezése** kiválasztott hálózatokon a Szolgáltatásvégpontok engedélyezése "Microsoft.AzureCosmosDB". Ha elkészült, válassza ki a **Hozzáadás**. 

   ![Válassza ki a virtuális hálózatot és alhálózatot](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)


1. A virtuális hálózat eléréséhez az Azure Cosmos-fiók engedélyezése után csak akkor engedélyezi a kiválasztott alhálózat forgalmát. A virtuális hálózatot és alhálózatot hozzáadott meg kell jelennie az alábbi képernyőképen látható módon:

   ![virtuális hálózat és alhálózat sikeresen konfigurálva](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Ahhoz, hogy a virtuális hálózati Szolgáltatásvégpontok, kell a következő előfizetési engedélyekkel:
  * VNET-előfizetésbe: hálózati közreműködő
  * Az Azure Cosmos-fiók előfizetés: a DocumentDB-Fiókközreműködő

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Egy új Azure virtuális hálózat és alhálózat szolgáltatásvégpont beállítása

1. A **összes erőforrás** panelen keresse meg az Azure Cosmos DB-fiók szeretné biztonságossá tételéhez.  

2. Mielőtt engedélyezné a virtuális hálózati szolgáltatásvégpont, másolja a használatalapú Azure Cosmos DB-fiókjához társított IP-tűzfal információit. IP-tűzfalon szolgáltatásvégpont beállítása után újra engedélyezheti.  

3. Válassza ki **tűzfalak és az Azure virtuális hálózatok** beállítások menüjében, majd engedélyezze a hozzáférést **kiválasztott hálózatok**.  

4. Válassza ki a hozzáférést egy új Azure virtuális hálózatot, virtuális hálózatok **hozzáadása új virtuális hálózat**.  

5. Adja meg az új virtuális hálózat létrehozásához szükséges adatait, és kattintson a létrehozás. Az alhálózat "Microsoft.AzureCosmosDB" engedélyezett szolgáltatásvégponttal jön.

   ![Válassza ki a virtuális hálózatot és alhálózatot az új virtuális hálózat](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Az Azure Cosmos-fiók más Azure-szolgáltatásokhoz hasonlóan az Azure Search által használt, vagy a Stream analytics vagy a Power bi-ban érhető el, ha engedélyezi a hozzáférést az Azure-szolgáltatásokhoz való hozzáférés engedélyezése ellenőrzésével.

Annak érdekében, hogy hozzáférése van az Azure Cosmos DB-metrikák a portálról, engedélyeznie kell **engedélyezi a hozzáférést az Azure Portalra** beállítások. Ezek a beállítások kapcsolatos további információkért lásd: kérelmek Azure Portalról és a kérést az Azure PaaS szolgáltatások szakaszokból konfigurálása [IP-tűzfalon](how-to-configure-firewall.md) cikk. Miután kijelölte a hozzáférést, jelölje ki **mentése** a beállítások mentéséhez.

## <a id="remove-vnet-or-subnet"></a>Egy virtuális hálózat vagy alhálózat törlése 

1. A **összes erőforrás** panelen, és keresse meg az Azure Cosmos DB-fiók, amelynek a Szolgáltatásvégpontok hozzárendelve.  

2. Válassza ki **tűzfalak és virtuális hálózatok** beállítások menüjében.  

3. Egy virtuális hálózat vagy alhálózat szabály eltávolításához válassza az "..." mellett a virtuális hálózat vagy alhálózat, majd válassza ki **eltávolítása**.

   ![Virtuális hálózat eltávolítása](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4.  Kattintson a **mentése** alkalmazza a módosításokat.

## <a id="configure-using-powershell"></a>Szolgáltatásvégpont konfigurálása az Azure PowerShell-lel 

A következő lépések használatával konfigurálja a szolgáltatásvégpontot egy Azure Cosmos DB-fiókot az Azure PowerShell-lel:  

1. Telepítse a legújabb [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) és [bejelentkezési](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. A virtuális hálózat egy meglévő alhálózat szolgáltatásvégpont engedélyezése.  

   ```powershell
   $rgname= "<Resource group name>"
   $vnName = "<virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

1. Felkészülés az ACL-t az Azure Cosmos-fiókon lehetővé tétele azáltal, hogy a virtuális hálózat és alhálózat szolgáltatásvégpont engedélyezve van a fiók rendelkezik-e.

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName  -ResourceGroupName $rgName
   ```

1. Azure Cosmos DB-fiók tulajdonságainak lekéréséhez futtassa a következő parancsmagot:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. A későbbi felhasználás céljára változók inicializálása. Állítsa be összes változót a meglévő fiók definícióból, ha több helyen van, hozzá kell őket a tömb részeként. Ebben a lépésben konfigurálnia is virtuális hálózati szolgáltatásvégpont "accountVNETFilterEnabled" változó "True" értékre állításával. Ezt az értéket később "isVirtualNetworkFilterEnabled" paraméter van hozzárendelve. 

   ```powershell
   $locations = @(@{})

   <# If you have read regions in addition to a write region, use the following code to set the $locations variable instead.

   $locations = @(@{"locationName"="<Write location>"; 
                 "failoverPriority"=0}, 
               @{"locationName"="<Read location>"; 
                  "failoverPriority"=1}) #>

   $consistencyPolicy = @{}
   $cosmosDBProperties = @{}

   $locations[0]['failoverPriority'] = $cosmosDBConfiguration.Properties.failoverPolicies.failoverPriority
   $locations[0]['locationName'] = $cosmosDBConfiguration.Properties.failoverPolicies.locationName

   $consistencyPolicy = $cosmosDBConfiguration.Properties.consistencyPolicy

   $accountVNETFilterEnabled = $True
   $subnetID = $vnProp.Id+"/subnets/" + $sname  
   $virtualNetworkRules = @(@{"id"=$subnetID})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

1. Azure Cosmos DB-fiók tulajdonságainak frissítése az új konfigurációval a következő parancsmag futtatásával: 

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName -Properties $CosmosDBProperties
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

## <a id="configure-using-cli"></a>Szolgáltatásvégpont konfigurálása az Azure CLI-vel 

1. A virtuális hálózatban lévő alhálózat szolgáltatásvégpont engedélyezése.

1. Alhálózati ACL-ek a meglévő Azure-Cosmos-fiók frissítése

   ```azurecli-interactive

   name="<Azure Cosmos account name>"
   resourceGroupName="<Resource group name>"

   az cosmosdb update \
      --name $name \
    --resource-group $resourceGroupName \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/frontend"
   ```

1. Hozzon létre egy új Azure Cosmos-fiókot alhálózati ACL-EK

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

## <a id="migrate-from-firewall-to-vnet"></a>Áttelepítés a virtuális hálózatok közötti ACL IP-tűzfalszabály 

Az alábbi lépéseket csak szükséges Azure-Cosmos-fiókok a meglévő IP-tűzfalszabályainak lehetővé teszi egy alhálózathoz, és szeretné használni a virtuális hálózat és alhálózat-alapú ACL-ek helyett IP-tűzfalszabály.

Miután az Azure Cosmos-fiók szolgáltatásvégpontját alhálózat be van kapcsolva, a kérések érkeznek, a nyilvános IP-cím helyett a virtuális Hálózatot és alhálózatot információt tartalmazó adatforrás. Ezért az ilyen kérések nem egyeznek meg egy IP-szűrő. A forrás kapcsoló a szolgáltatásvégpont engedélyezve van az alhálózatról elért összes Azure Cosmos-fiókok esetében történik. Annak érdekében, hogy az állásidő elkerülése érdekében használja az alábbi lépéseket:

1. Az Azure Cosmos-fiók tulajdonságainak beolvasása a következő parancsmagot:

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Cosmos account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. A későbbi használat céljából változók inicializálása. Beállítani az összes változót a meglévő fiók-definíciót. Adja hozzá a virtuális hálózatok közötti ACL-t az összes Azure-Cosmos-fiókok az alhálózatról elért `ignoreMissingVNetServiceEndpoint` jelzőt.  

   Ha több helyen, hozzá kell őket a tömb részeként. Ebben a lépésben konfigurálnia is virtuális hálózati szolgáltatásvégpont "accountVNETFilterEnabled" változó "True" értékre állításával. Ezt az értéket később "isVirtualNetworkFilterEnabled" paraméter van hozzárendelve.

   ```powershell
   $locations = @(@{})

   <# If you have read regions in addition to a write region, use the following code to set the $locations variable instead.

   $locations = @(@{"locationName"="<Write location>"; 
              "failoverPriority"=0}, 
            @{"locationName"="<Read location>"; 
               "failoverPriority"=1}) #>

   $consistencyPolicy = @{}
   $cosmosDBProperties = @{}

   $locations[0]['failoverPriority'] = $cosmosDBConfiguration.Properties.failoverPolicies.failoverPriority
   $locations[0]['locationName'] = $cosmosDBConfiguration.Properties.failoverPolicies.locationName
   $consistencyPolicy = $cosmosDBConfiguration.Properties.consistencyPolicy
   $accountVNETFilterEnabled = $True
   $subnetID = “Subnet ARM URL” e.g "/subscriptions/f7ddba26-ab7b-4a36-a2fa-7d01778da30b/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/subnet1"

   $virtualNetworkRules = @(@{"id"=$subnetID, "ignoreMissingVNetServiceEndpoint"="True"})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

1. Az Azure Cosmos-fiók tulajdonságainak frissítése az új konfigurációval a következő parancsmag futtatásával:

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
    -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
    -ApiVersion $apiVersion `
    -ResourceGroupName $rgName `
    -Name $acctName -Properties $CosmosDBProperties
   ```

1. Akkor hozzáférhet az alhálózat a fiókok az Azure Cosmos ismételje meg az 1 – 3.

1.  Várjon 15 percig, és frissítse az alhálózati szolgáltatásvégpont engedélyezése.

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

* Tűzfal beállítása az Azure Cosmos DB: [tűzfal-támogatás](firewall-support.md) cikk.

