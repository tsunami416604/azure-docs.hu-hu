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
ms.openlocfilehash: 7dd768d3f0059f4b26f09298992483553f1508d2
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52681246"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Az Azure Key Vault-tűzfalak és virtuális hálózatok konfigurálása

Ebben a cikkben részletes útmutatást nyújt az Azure Key Vault-tűzfalak és virtuális hálózatok hozzáférés korlátozása a key vault konfigurálásához. A [virtuális hálózati Szolgáltatásvégpontok a Key vault](key-vault-overview-vnet-service-endpoints.md) lehetővé teszi a hozzáférés korlátozása egy adott virtuális hálózatban és IPv4-címtartományokat (az internet protocol version 4) készletét.

> [!IMPORTANT]
> Tűzfalszabályok vannak érvényben, miután a felhasználók csak hajthat végre a Key Vault [adatsík](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) műveleteket, ha az ügyfélkérések az engedélyezett virtuális hálózatok vagy IPv4-címtartományokat. Ez vonatkozik a Key Vault elérése az Azure Portalról. Bár az Azure Portalon, egy kulcstárolóba is böngésző felhasználók számára, akkor előfordulhat, hogy nem tudják kulcsok listázása, titkos kódok és tanúsítványok, ha az ügyfélszámítógép nem szerepel az engedélyezési listán. Ez is hatással van a Key Vault-választó más Azure-szolgáltatások. Lehet, hogy a felhasználók tudni kulcstartók listája látható, de nem kulcsok, listázását, ha a tűzfal-szabályok megakadályozzák fejlesztőkörnyezetükben.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

Íme a Key Vault-tűzfalak és virtuális hálózatok konfigurálása az Azure portal használatával:

1. Keresse meg a key vault szeretné biztonságossá tenni.
2. Válassza ki **tűzfalak és virtuális hálózatok**.
3. A **engedélyezze a hozzáférést**válassza **kiválasztott hálózatok**.
4. Meglévő virtuális hálózatok tűzfalak és virtuális hálózati szabályok hozzáadásához válassza **+ meglévő virtuális hálózatok hozzáadása**.
5. A megnyíló új panelen válassza ki az előfizetést, virtuális hálózatok és alhálózatok, amelyet szeretne ehhez a kulcstartóhoz való hozzáférés engedélyezése. Ha a virtuális hálózatok és alhálózatok választja, nincs engedélyezve a Szolgáltatásvégpontok, győződjön meg róla, engedélyezze a szolgáltatásvégpontokat, és válassza ki a kívánt **engedélyezése**. Az érvénybe lépéséhez akár 15 percet igénybe vehet.
6. A **IP hálózatokra**, adja hozzá az IPv4-címtartományokat IPv4-címtartományokat beírásával [(Classless Inter-Domain Routing) CIDR-jelölés](https://tools.ietf.org/html/rfc4632) vagy egyedi IP-címek.
7. Kattintson a **Mentés** gombra.

Új virtuális hálózatok és alhálózatok is hozzáadhat, és, majd engedélyezze a szolgáltatásvégpontokat az újonnan létrehozott virtuális hálózatok és alhálózatok kiválasztásával **+ új virtuális hálózat hozzáadása**. Ezután kövesse az utasításokat.

## <a name="use-the-azure-cli-20"></a>Az Azure CLI 2.0 használata

Íme a Key Vault-tűzfalak és virtuális hálózatok konfigurálása az Azure CLI 2.0 használatával:

1. [Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) és [jelentkezzen be a](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Elérhető a virtuális hálózati szabályok listája. A kulcstartó szabályok nem állított be, ha a lista üres lesz.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. A Key vault szolgáltatásvégpont engedélyezése a egy meglévő virtuális hálózatot és alhálózatot.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Adjon hozzá egy virtuális hálózatot és alhálózatot a hálózati szabályt.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Adjon hozzá egy IP-címtartományt, ahonnan a forgalom engedélyezéséhez.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Ha ezt a kulcstartót kell lennie minden olyan megbízható szolgáltatások által elérhető, állítsa be `bypass` való `AzureServices`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Kapcsolja be a hálózati szabályokat úgy, hogy az alapértelmezett művelet a `Deny`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Azure PowerShell használatával

Íme a Key Vault-tűzfalak és virtuális hálózatok konfigurálása a PowerShell használatával:

1. Telepítse a legújabb [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/install-azurerm-ps), és [jelentkezzen be a](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Elérhető a virtuális hálózati szabályok listája. Ha nem állított be ehhez a kulcstartóhoz tartozó szabályokat, a lista üres lesz.
   ```PowerShell
   (Get-AzureRmKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Szolgáltatásvégpont engedélyezése a Key vault a egy meglévő virtuális hálózatot és alhálózatot.
   ```PowerShell
   Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzureRmVirtualNetwork
   ```

4. Adjon hozzá egy virtuális hálózatot és alhálózatot a hálózati szabályt.
   ```PowerShell
   $subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Adjon hozzá egy IP-címtartományt, ahonnan a forgalom engedélyezéséhez.
   ```PowerShell
   Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Ha ezt a kulcstartót kell lennie minden olyan megbízható szolgáltatások által elérhető, állítsa be `bypass` való `AzureServices`.
   ```PowerShell
   Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Kapcsolja be a hálózati szabályokat úgy, hogy az alapértelmezett művelet a `Deny`.
   ```PowerShell
   Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Referencia

* Az Azure CLI 2.0-parancsok: [az keyvault hálózati-szabály](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Azure PowerShell-parancsmagok: [Get-AzureRmKeyVault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurermkeyvault), [Add-AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureRmKeyVaultNetworkRule), [Remove-AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureRmKeyVaultNetworkRule), [ Frissítés-AzureRmKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureRmKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>További lépések

* [A Key vault virtuális hálózati Szolgáltatásvégpontok](key-vault-overview-vnet-service-endpoints.md)
* [Kulcstartó védelme](key-vault-secure-your-key-vault.md)