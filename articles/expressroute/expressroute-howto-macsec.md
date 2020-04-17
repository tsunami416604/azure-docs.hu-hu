---
title: 'Azure ExpressRoute: MACsec konfigurálása'
description: Ez a cikk segít a MACsec konfigurálásában a peremhálózati útválasztók és a Microsoft peremhálózati útválasztói közötti kapcsolatok védelmére.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: cherylmc
ms.openlocfilehash: 572147ca43e9a4dea9d9601dfa1dac8ba1c97ed0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458232"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>MACsec konfigurálása ExpressRoute Direct portokon

Ez a cikk segít konfigurálni MACsec a kapcsolatot a peremhálózati útválasztók és a Microsoft peremhálózati útválasztók a PowerShell használatával.

## <a name="before-you-begin"></a>Előkészületek

A konfiguráció megkezdése előtt erősítse meg a következőket:

* Az [ExpressRoute Direct létesítési munkafolyamatait](expressroute-erdirect-about.md)ismeri.
* Létrehozott egy [ExpressRoute Direct porterőforrást.](expressroute-howto-erdirect.md)
* Ha helyileg szeretné futtatni a PowerShellt, ellenőrizze, hogy az Azure PowerShell legújabb verziója telepítve van-e a számítógépen.

### <a name="working-with-azure-powershell"></a>Az Azure PowerShell együttműködése

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>Bejelentkezés és a megfelelő előfizetés kiválasztása

A konfiguráció elindításához jelentkezzen be az Azure-fiókjába, és válassza ki a használni kívánt előfizetést.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. Hozzon létre Azure Key Vault, MACsec titkos kulcsok, és a felhasználói identitás

1. Hozzon létre egy Key Vault-példányt a MACsec titkos kulcsok egy új erőforráscsoportban tárolásához.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    Ha már rendelkezik egy key vault vagy egy erőforráscsoport, újra felhasználhatja őket. Fontos azonban, hogy engedélyezze a [ **helyreállítható törlési** funkciót](../key-vault/general/overview-soft-delete.md) a meglévő kulcstartóban. Ha a helyreállítható törlés nincs engedélyezve, a következő parancsokkal engedélyezheti azt:

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. Hozzon létre egy felhasználói identitást.

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    Ha a New-AzUserAssignedIdentity nem ismeri fel érvényes PowerShell-parancsmagként, telepítse a következő modult (rendszergazdai módban), és futtassa újra a fenti parancsot.

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. Hozzon létre egy kapcsolattársítási kulcsot (CAK) és egy kapcsolattársi kulcsnevet (CKN), és tárolja őket a kulcstartóban.

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
4. Rendelje hozzá a GET engedélyt a felhasználói identitáshoz.

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   Most ez az identitás letudja kérni a titkos kulcsokat, például a CAK és a CKN, a key vault.
5. Állítsa be az ExpressRoute által használandó felhasználói identitást.

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. MACsec konfigurálása ExpressRoute Direct portokon

### <a name="to-enable-macsec"></a>A MACsec engedélyezése

Minden ExpressRoute Direct-példány két fizikai porttal rendelkezik. Engedélyezheti a MACsec-et mindkét porton egyszerre, vagy egyszerre csak egy porton engedélyezi a MACsec-et. Ha egyszerre egy portot csinál (a forgalmat egy aktív portra váltja a másik port karbantartása közben), az expressroute direct már szolgálatban van.

1. Állítsa be a MACsec titkos kulcsokat és rejtjelezést, és társítsa a felhasználói identitást a porthoz, hogy az ExpressRoute felügyeleti kód szükség esetén hozzáférhessen a MACsec titkos kulcsokhoz.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[0]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[1]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.identity = $erIdentity
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```
2. (Nem kötelező) Ha a portok felügyeleti le állapotban vannak, a következő parancsokfuttatásával hozhatja létre a portokat.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    Ezen a ponton a MACsec engedélyezve van a Microsoft oldalán lévő ExpressRoute Direct portokon. Ha még nem konfigurálta a peremhálózati eszközökön, folytathatja a konfigurálásukat ugyanazzal a MACsec-titkosítási titkokkal és titkosítással.

### <a name="to-disable-macsec"></a>A MACsec letiltása

Ha a MACsec már nem kívánatos az ExpressRoute Direct példányon, a következő parancsokkal letilthatja azt.

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

Ezen a ponton a MACsec le van tiltva a Microsoft oldalán lévő ExpressRoute Direct portokon.

### <a name="test-connectivity"></a>Kapcsolat tesztelése
Miután konfigurálta a MACsec-et (beleértve a MACsec kulcsfrissítést) az ExpressRoute Direct portokon, [ellenőrizze,](expressroute-troubleshooting-expressroute-overview.md) hogy az áramkörök BGP-munkamenetei működnek-e. Ha még nincs kapcsolat a portokon, hozzon létre egyet először, és állítsa be az Azure privát társviszonylétesítést vagy a microsoftos társviszony-létesítést a kapcsolatcsoportban. Ha a MACsec helytelenül van konfigurálva, beleértve a MACsec-kulcs eltérését a hálózati eszközök és a Microsoft hálózati eszközei között, a 3. Ha minden megfelelően van konfigurálva, mindkét irányban helyesen kell hirdetnie a BGP-útvonalakat, és ennek megfelelően kell látnia az alkalmazás-adatfolyamot az ExpressRoute-on keresztül.

## <a name="next-steps"></a>További lépések
1. [ExpressRoute-kapcsolat létrehozása az ExpressRoute Directen](expressroute-howto-erdirect.md)
2. [ExpressRoute-kapcsolat egy Azure virtuális hálózattal](expressroute-howto-linkvnet-arm.md)
3. [ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
