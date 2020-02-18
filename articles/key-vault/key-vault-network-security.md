---
title: Azure Key Vault tűzfalak és virtuális hálózatok konfigurálása – Azure Key Vault
description: Részletes útmutató Key Vault tűzfalak és virtuális hálózatok konfigurálásához
services: key-vault
author: amitbapat
manager: rkarlin
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 870064406c86e9cef6a45dfbe47c61cdaa9ecab0
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77426309"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Azure Key Vault tűzfalak és virtuális hálózatok konfigurálása

Ez a cikk részletes útmutatást nyújt Azure Key Vault tűzfalak és virtuális hálózatok konfigurálásához a kulcstartóhoz való hozzáférés korlátozásához. A [Key Vault virtuális hálózati szolgáltatási végpontok](key-vault-overview-vnet-service-endpoints.md) lehetővé teszik, hogy korlátozza a hozzáférést egy adott virtuális hálózathoz, és IPv4 (Internet Protocol Version 4) címtartományt állítson be.

> [!IMPORTANT]
> A tűzfalszabályok érvénybe léptetése után a felhasználók csak akkor hajthatják végre Key Vault [adatsík](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) -műveleteket, ha a kérésük engedélyezett virtuális hálózatokból vagy IPv4-címtartományok származnak. Ez a Azure Portal Key Vault elérésére is vonatkozik. Bár a felhasználók megkereshetik a kulcstartót a Azure Portalból, előfordulhat, hogy nem tudják listázni a kulcsokat, titkokat vagy tanúsítványokat, ha az ügyfélszámítógépük nem szerepel az engedélyezési listán. Ez hatással van a más Azure-szolgáltatások Key Vault választóra is. Előfordulhat, hogy a felhasználók megtekinthetik a kulcstárolók listáját, de nem listázják a kulcsokat, ha a tűzfalszabályok megakadályozzák az ügyfélszoftvert.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

A Key Vault tűzfalak és virtuális hálózatok konfigurálása a Azure Portal használatával:

1. Keresse meg a védeni kívánt Key vaultot.
2. Válassza a **hálózatkezelés**lehetőséget, majd válassza a **tűzfalak és a virtuális hálózatok** fület.
3. **A hozzáférés engedélyezése lehetőségnél**válassza a **kiválasztott hálózatok**elemet.
4. Ha meglévő virtuális hálózatokat szeretne hozzáadni a tűzfalakhoz és a virtuális hálózati szabályokhoz, válassza a **+ meglévő virtuális hálózatok hozzáadása**elemet.
5. A megnyíló új panelen válassza ki azt az előfizetést, virtuális hálózatot és alhálózatot, amely számára engedélyezni kívánja a kulcstartó elérését. Ha a kiválasztott virtuális hálózatok és alhálózatok nem rendelkeznek engedélyezett szolgáltatási végpontokkal, erősítse meg, hogy engedélyezni szeretné a szolgáltatási végpontokat, majd válassza az **Engedélyezés**lehetőséget. Az érvénybe léptetés akár 15 percet is igénybe vehet.
6. Az **IP-hálózatok**területen adja hozzá az IPv4-címtartományokat az IPv4-címtartományok beírásával a [CIDR (osztály nélküli tartományok közötti útválasztás) jelöléssel](https://tools.ietf.org/html/rfc4632) vagy az egyes IP-címekkel.
7. Kattintson a **Mentés** gombra.

Új virtuális hálózatokat és alhálózatokat is hozzáadhat, majd engedélyezheti az újonnan létrehozott virtuális hálózatok és alhálózatok szolgáltatási végpontját az **+ új virtuális hálózat hozzáadása**lehetőség kiválasztásával. Ezután kövesse az utasításokat.

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata 

A következőképpen konfigurálhatja Key Vault tűzfalakat és virtuális hálózatokat az Azure CLI használatával

1. [Telepítse az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) -t, és [Jelentkezzen be](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. A rendelkezésre álló virtuális hálózati szabályok listázása. Ha nem állított be szabályokat ehhez a kulcstartóhoz, a lista üres lesz.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Egy meglévő virtuális hálózat és alhálózat Key Vaultának engedélyezése a szolgáltatási végpont számára.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Adjon hozzá egy virtuális hálózatot és alhálózatot a hálózati szabályt.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Adjon hozzá egy IP-címtartományt, amelyből engedélyezi a forgalmat.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Ha a kulcstárolónak bármely megbízható szolgáltatás számára elérhetőnek kell lennie, állítsa be `bypass` `AzureServices`re.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. A hálózati szabályok bekapcsolásához állítsa az alapértelmezett műveletet `Deny`ra.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Azure PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következőképpen konfigurálhatja Key Vault tűzfalakat és virtuális hálózatokat a PowerShell használatával:

1. Telepítse a legújabb [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps), és [Jelentkezzen be](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. A rendelkezésre álló virtuális hálózati szabályok listázása. Ha nem állított be szabályokat ehhez a kulcstartóhoz, a lista üres lesz.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. A szolgáltatás végpontjának engedélyezése meglévő virtuális hálózat és alhálózat Key Vaultához.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Adjon hozzá egy virtuális hálózatot és alhálózatot a hálózati szabályt.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Adjon hozzá egy IP-címtartományt, amelyből engedélyezi a forgalmat.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Ha a kulcstárolónak bármely megbízható szolgáltatás számára elérhetőnek kell lennie, állítsa be `bypass` `AzureServices`re.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. A hálózati szabályok bekapcsolásához állítsa az alapértelmezett műveletet `Deny`ra.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Referencia

* Azure CLI-parancsok: [az Key Vault Network-Rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Azure PowerShell parancsmagok: [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Következő lépések

* [Virtuális hálózati szolgáltatás végpontjai Key Vault](key-vault-overview-vnet-service-endpoints.md)
* [Kulcstartó védelme](key-vault-secure-your-key-vault.md)
