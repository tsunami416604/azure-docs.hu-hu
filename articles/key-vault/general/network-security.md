---
title: Az Azure Key Vault tűzfalainak és virtuális hálózatainak konfigurálása – Azure Key Vault
description: Lépésenkénti útmutató a Key Vault tűzfalainak és virtuális hálózatainak konfigurálásához
services: key-vault
author: amitbapat
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 93fb718c28d015162d3c43d0936bbf4de9cad2fe
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422577"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Az Azure Key Vault tűzfalainak és virtuális hálózatainak konfigurálása

Ez a cikk lépésenkénti útmutatást nyújt az Azure Key Vault tűzfalainak és virtuális hálózatainak a kulcstartóhoz való hozzáférés korlátozására. A [Key Vault virtuális hálózati szolgáltatásvégpontjai)](overview-vnet-service-endpoints.md)lehetővé teszik a hozzáférés korlátozását egy adott virtuális hálózathoz és az IPv4 (internet protokoll 4-es verzió) címtartományaihoz.

> [!IMPORTANT]
> A tűzfalszabályok érvénybe lépése után a felhasználók csak akkor hajthatnak végre Key [Vault-adatsík-műveleteket,](secure-your-key-vault.md#data-plane-access-control) ha a kéréseik engedélyezett virtuális hálózatokból vagy IPv4-címtartományokból származnak. Ez az Azure Portalról való key vault elérésére is vonatkozik. Bár a felhasználók az Azure Portalról is megkereshetik a kulcstartót, előfordulhat, hogy nem tudják felsorolni a kulcsokat, titkos kulcsokat vagy tanúsítványokat, ha az ügyfélgépük nem szerepel az engedélyezett listában. Ez hatással van a Key Vault-választó más Azure-szolgáltatások által. Előfordulhat, hogy a felhasználók láthatják a kulcstartók listáját, de a kulcsokat nem, ha a tűzfalszabályok megakadályozzák az ügyfélgépüket.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

A következőképpen konfigurálhatja a Key Vault tűzfalait és a virtuális hálózatokat az Azure Portal használatával:

1. Tallózással keresse meg a biztonságosan szeretne lépni.
2. Válassza **a Hálózat lehetőséget,** majd a **Tűzfalak és a virtuális hálózatok** lapot.
3. A Hozzáférés engedélyezése a területen **csoportban**válassza a **Kijelölt hálózatok**lehetőséget.
4. Ha meglévő virtuális hálózatokat szeretne hozzáadni a tűzfalakhoz és a virtuális hálózati szabályokhoz, válassza **a + Meglévő virtuális hálózatok hozzáadása**lehetőséget.
5. Az új panel, amely megnyitja, válassza ki az előfizetés, a virtuális hálózatok és alhálózatok, amelyek hozzáférést szeretne engedélyezni a key vault. Ha a kiválasztott virtuális hálózatok és alhálózatok nem engedélyezve vannak a szolgáltatásvégpontok, ellenőrizze, hogy engedélyezni szeretné-e a szolgáltatásvégpontokat, és válassza **az Engedélyezés**lehetőséget. Az érvénybe lépés akár 15 percet is igénybe vehet.
6. Az **IP-hálózatok**csoportban adja hozzá az IPv4-címtartományokat a [CIDR (Osztály nélküli tartományok közötti útválasztás)](https://tools.ietf.org/html/rfc4632) jelölése vagy az egyes IP-címek IPv4-címtartományainak beírásával.
7. Kattintson a **Mentés** gombra.

Új virtuális hálózatokat és alhálózatokat is hozzáadhat, majd engedélyezheti a szolgáltatásvégpontokat az újonnan létrehozott virtuális hálózatokhoz és alhálózatokhoz a **+ Új virtuális hálózat hozzáadása**lehetőség kiválasztásával. Ezután kövesse az utasításokat.

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata 

A következőképpen konfigurálhatja a Key Vault tűzfalait és a virtuális hálózatokat az Azure CLI használatával

1. [Telepítse az Azure CLI-t,](https://docs.microsoft.com/cli/azure/install-azure-cli) és [jelentkezzen be.](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)

2. Az elérhető virtuális hálózati szabályok listája. Ha nem állított be szabályokat ehhez a kulcstartóhoz, a lista üres lesz.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Engedélyezze a szolgáltatásvégpontot a Key Vault számára egy meglévő virtuális hálózaton és alhálózaton.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Hálózati szabály hozzáadása virtuális hálózathoz és alhálózathoz.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Adjon hozzá egy IP-címtartományt, amelyből engedélyezni szeretné a forgalmat.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Ha a kulcstartónak bármely megbízható szolgáltatás `bypass` `AzureServices`számára elérhetőnek kell lennie, állítsa a beállítását.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Kapcsolja be a hálózati szabályokat `Deny`az alapértelmezett művelet beállításával .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Azure PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A következőképpen konfigurálhatja a Key Vault tűzfalait és a virtuális hálózatokat a PowerShell használatával:

1. Telepítse a legújabb [Azure PowerShellt](https://docs.microsoft.com/powershell/azure/install-az-ps), és [jelentkezzen be.](https://docs.microsoft.com/powershell/azure/authenticate-azureps)

2. Az elérhető virtuális hálózati szabályok listája. Ha nem állított be szabályokat ehhez a kulcstartóhoz, a lista üres lesz.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Szolgáltatásvégpont engedélyezése a Key Vault számára egy meglévő virtuális hálózaton és alhálózaton.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Hálózati szabály hozzáadása virtuális hálózathoz és alhálózathoz.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Adjon hozzá egy IP-címtartományt, amelyből engedélyezni szeretné a forgalmat.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Ha a kulcstartónak bármely megbízható szolgáltatás `bypass` `AzureServices`számára elérhetőnek kell lennie, állítsa a beállítását.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Kapcsolja be a hálózati szabályokat `Deny`az alapértelmezett művelet beállításával .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Referencia

* Azure CLI-parancsok: [az keyvault hálózati szabály](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Azure PowerShell-parancsmagok: [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>További lépések

* [Virtuális hálózati szolgáltatás végpontjai a Key Vaulthoz)](overview-vnet-service-endpoints.md)
* [A kulcstartó biztonsága)](secure-your-key-vault.md)
