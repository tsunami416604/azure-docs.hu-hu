---
ms.assetid: ''
title: Az Azure Key Vault-tűzfalak és virtuális hálózatok konfigurálása
description: Részletes útmutatás a Key Vault-tűzfalak és virtuális hálózatok konfigurálása
services: key-vault
author: amitbapat
manager: mbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.workload: identity
ms.date: 08/31/2018
ms.author: ambapat
ms.openlocfilehash: 6315434c1e8acc82e02f5c9e5ae8ab2d1cacc887
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302053"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Az Azure Key Vault-tűzfalak és virtuális hálózatok konfigurálása

Ez az útmutató részletes utasításokat követve konfigurálhatja a Key Vault-tűzfalak és virtuális hálózatok a key vaultban való hozzáférés korlátozásához. A [virtuális hálózati Szolgáltatásvégpontok a Key vault](key-vault-overview-vnet-service-endpoints.md) lehetővé teszi, hogy korlátozza a hozzáférést a key vault megadott virtuális hálózati és/vagy egy IPv4-címtartományokat (az Internet Protocol version 4) készletét.

> [!IMPORTANT]
> Ha a tűzfal- és a virtuális hálózati szabályok érvényben, az összes Key Vault [adatsík](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) csak akkor hajtható végre műveleteket, ha a hívó ügyfélkérések engedélyezett virtuális hálózat vagy IPV4-címtartományokat. Ez vonatkozik a key vault elérése az Azure Portalról. Amíg a felhasználó böngészőben is a key vault Azure Portalról, előfordulhat, hogy nem tudnak kulcsok/titkos kulcsok és tanúsítványok listája, ha az ügyfélszámítógép nem szerepel az engedélyezési listán. Ez is hatással van a "Key Vault választó" más Azure-szolgáltatások. Lehet, hogy a felhasználók kulcstartók listája, de nem kulcsok, listázását, ha a tűzfal-szabályok megakadályozzák fejlesztőkörnyezetükben.

## <a name="azure-portal"></a>Azure Portal

1. Keresse meg a key vault szeretné biztonságossá tenni.
2. Kattintson a **tűzfalak és virtuális hálózatok**.
3. Kattintson a **kiválasztott hálózatok** alatt **engedélyezze a hozzáférést**.
4. Meglévő virtuális hálózatok tűzfalak és virtuális hálózati szabályok hozzáadásához kattintson **+ meglévő virtuális hálózatok hozzáadása**.
5. Az új panelen felugró válassza ki az előfizetés, virtuális hálózat és alhálózat, amelyet szeretne ehhez a kulcstartóhoz való hozzáférés engedélyezése. Ha a virtuális hálózat és alhálózat választja, nincs engedélyezve a Szolgáltatásvégpontok látni fogja az üzenetet, "a következő hálózatok szolgáltatás végpontok enavled nem rendelkezik". Kattintson a **engedélyezése** Miután meggyőződött arról, hogy szeretné-e a listán szereplő szolgáltatásvégpont engedélyezése a virtuális hálózat és alhálózat. Érvénybe léptetéséhez akár 15 percig is eltarthat.
6. Is hozzáadhat az új virtuális hálózat és alhálózat, és ezután engedélyezze a szolgáltatásvégpontokat az újonnan létrehozott virtuális hálózat és alhálózat, kattintva **+ új virtuális hálózat hozzáadása** és utasításokat követve.
7. A **IP hálózatokra**, IPv4-címtartományokat az IPv4-címtartományokat beírásával is hozzáadhat [(Classless Inter-Domain Routing) CIDR-jelölés](https://tools.ietf.org/html/rfc4632) vagy egyedi IP-címeket.
8. Kattintson a **Save** (Mentés) gombra.

## <a name="azure-cli-20"></a>Azure CLI 2.0

1. [Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) és [bejelentkezési](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Lista érhető el a virtuális hálózati szabályok, ha nem állított be ehhez a kulcstartóhoz tartozó szabályokat, a lista üres lesz.
```azurecli
az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
```

3. A Key vault egy meglévő virtuális hálózatot és alhálózatot a szolgáltatásvégpont engedélyezése
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
```

4. Egy virtuális hálózatot és alhálózatot a hálózati szabály hozzáadása
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
```

5. Érkező adatforgalom engedélyezéséhez IP-címtartomány hozzáadása
```azurecli
az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
```

6. Ha ezt a kulcstartót kell lennie a megbízható szolgáltatások által elérhető, állítsa "Mellőzés" mellőzhető Azure-szolgáltatásokat
```azurecli
az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
```

7. Most már a végső és fontos lépés, kapcsolja be a hálózati szabályok ON az alapértelmezett műveletének átállítása az "Elutasítás"
```azurecli
az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
```

## <a name="azure-powershell"></a>Azure PowerShell

1. Telepítse a legújabb [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) és [bejelentkezési](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Lista érhető el a virtuális hálózati szabályok, ha nem állított be ehhez a kulcstartóhoz tartozó szabályokat, a lista üres lesz.
```PowerShell
(Get-AzureRmKeyVault -VaultName "mykeyvault").NetworkAcls
```

3. A Key vault egy meglévő virtuális hálózatot és alhálózatot a szolgáltatásvégpont engedélyezése
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzureRmVirtualNetwork
```

4. Egy virtuális hálózatot és alhálózatot a hálózati szabály hozzáadása
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
```

5. Érkező adatforgalom engedélyezéséhez IP-címtartomány hozzáadása
```PowerShell
Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
```

6. Ha ezt a kulcstartót kell lennie a megbízható szolgáltatások által elérhető, állítsa "Mellőzés" mellőzhető Azure-szolgáltatásokat
```PowerShell
Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
```

7. Most már a végső és fontos lépés, kapcsolja be a hálózati szabályok ON az alapértelmezett műveletének átállítása az "Elutasítás"
```PowerShell
Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
```

## <a name="references"></a>Referencia

* Az Azure CLI 2.0-parancsok - [az keyvault hálózati-szabály](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Az Azure PowerShell-parancsmagok – [Get-AzureRmKeyVault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurermkeyvault), [Add-AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureRmKeyVaultNetworkRule), [Remove-AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureRmKeyVaultNetworkRule), [ Frissítés-AzureRmKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureRmKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>További lépések

* [Virtuális hálózati Szolgáltatásvégpontok a Key Vault számára](key-vault-overview-vnet-service-endpoints.md)
* [Kulcstartó védelme](key-vault-secure-your-key-vault.md)