---
title: Az Azure Disk Encryption kulcstárolójának létrehozása és konfigurálása az Azure AD-vel (előző kiadás)
description: Ez a cikk előfeltételei a Microsoft Azure lemeztitkosítás iaaS virtuális gépek használatának előfeltételeit.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 5a42b1e0dc82b3340bbebe176c71cb1754d00664
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243379"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>Az Azure Disk Encryption kulcstárolójának létrehozása és konfigurálása az Azure AD-vel (előző kiadás)

**Az Azure Disk Encryption új kiadása kiküszöböli az Azure AD-alkalmazás paraméter biztosításának követelményét a virtuális gép lemeztitkosításának engedélyezéséhez. Az új kiadás, már nem kell megadnia az Azure AD hitelesítő adatait a titkosítás engedélyezése a titkosítási lépés során. Minden új virtuális gép titkosítására az Azure AD alkalmazás paraméterei az új kiadás használatával. A virtuális gép lemeztitkosításának az új kiadással történő engedélyezésére vonatkozó utasítások megtekintéséhez olvassa el az Azure Disk Encryption című [témakört.](disk-encryption-overview.md) Az Azure AD-alkalmazás paramétereivel már titkosított virtuális gépek továbbra is támogatottak, és továbbra is karban kell tartani az AAD szintaxissal.**

Az Azure Disk Encryption az Azure Key Vault segítségével szabályozza és kezeli a lemeztitkosítási kulcsokat és titkos kulcsokat.  A kulcstartókról az [Azure Key Vault – és](../../key-vault/key-vault-get-started.md) a Key Vault [biztonságossá tétele](../../key-vault/key-vault-secure-your-key-vault.md)című témakörben talál további információt. 

Az Azure Disk Encryption és az Azure AD (előző kiadás) használatával használható kulcstároló létrehozása és konfigurálása három lépést foglal magában:

1. Kulcstartó létrehozása. 
2. Hozzon létre egy Azure AD-alkalmazást és egyszerű szolgáltatás.
3. A kulcstartó hozzáférési szabályzatának beállítása az Azure AD-alkalmazás számára.
4. A kulcstartó speciális hozzáférési szabályzatainak beállítása.
 
Ha kívánja, kulcstitkosítási kulcsot (KEK) is létrehozhat vagy importálhat.

Tekintse meg a fő [létrehozása és konfigurálása a key vault az Azure Disk Encryption](disk-encryption-key-vault.md) cikket, hogyan [telepíti az eszközöket, és csatlakozzon az Azure-hoz.](disk-encryption-key-vault.md#install-tools-and-connect-to-azure)

> [!Note]
> A cikkben ismertetett lépések automatikusan az [Azure Disk Encryption előfeltételei CLI-parancsfájl](https://github.com/ejarvi/ade-cli-getting-started) és az [Azure Disk Encryption előfeltételei PowerShell-parancsfájl.](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)


## <a name="create-a-key-vault"></a>Kulcstartó létrehozása 
Az Azure Disk Encryption integrálva van az [Azure Key Vaultszolgáltatással,](https://azure.microsoft.com/documentation/services/key-vault/) így szabályozhatja és kezelheti a kulcstároló-előfizetéslemez-titkosítási kulcsait és titkos kulcsait. Létrehozhat egy key vaultot, vagy használhat egy meglévőt az Azure Disk Encryption számára. A kulcstartókról az [Azure Key Vault – és](../../key-vault/key-vault-get-started.md) a Key Vault [biztonságossá tétele](../../key-vault/key-vault-secure-your-key-vault.md)című témakörben talál további információt. Használhatja a Resource Manager-sablon, az Azure PowerShell vagy az Azure CLI egy key vault létrehozásához. 


>[!WARNING]
>Annak érdekében, hogy a titkosítási titkok ne lépjék át a területi határokat, az Azure Disk Encryption a Key Vaultés a virtuális gépek kell egy helyen ugyanabban a régióban. Hozzon létre és használjon egy Key Vault, amely ugyanabban a régióban, mint a virtuális gép titkosítva. 


### <a name="create-a-key-vault-with-powershell"></a>Kulcstartó létrehozása a PowerShell használatával

Az Azure PowerShell segítségével létrehozhat egy key vaultot a [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) parancsmag használatával. A Key Vault további parancsmagjait az [Az.KeyVault](/powershell/module/az.keyvault/). 

1. Szükség esetén hozzon létre egy új erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup)segítségével.  Az adatközpontok helyeinek listázásához használja a [Get-AzLocation parancsot.](/powershell/module/az.resources/get-azlocation) 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. Új kulcstartó létrehozása a [New-AzKeyVault használatával](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Vegye figyelembe a **tároló nevét,** **az erőforráscsoport nevét**, az **erőforrás-azonosítót**, a **tároló URI-ját**és a lemezek titkosításakor későbbi használatra visszaadott **objektumazonosítót.** 


### <a name="create-a-key-vault-with-azure-cli"></a>Kulcstartó létrehozása az Azure CLI-vel
A key vault az Azure CLI az [az keyvault](/cli/azure/keyvault#commands) parancsok használatával kezelheti. Key vault létrehozásához használja [az keyvault create](/cli/azure/keyvault#az-keyvault-create).

1. Szükség esetén hozzon létre egy új erőforráscsoportot az [az csoport létrehozásával.](/cli/azure/group#az-group-create) Helyek listázásához használja [az fióklista-helyeket](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Hozzon létre egy új key vault [létrehozása.](/cli/azure/keyvault#az-keyvault-create)
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Vegye figyelembe a **tároló nevét** (nevét), **az erőforráscsoport nevét**, az **erőforrás-azonosítót** (ID), a **Tároló URI-ját**és a később használatra visszaadott **objektumazonosítót.** 

### <a name="create-a-key-vault-with-a-resource-manager-template"></a>Kulcstartó létrehozása Erőforrás-kezelő sablonnal

Az [Erőforrás-kezelő sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)használatával létrehozhat egy kulcstartót.

1. Az Azure gyorsindítási sablonján kattintson **a Telepítés az Azure-ba**elemre.
2. Válassza ki az előfizetést, az erőforráscsoportot, az erőforráscsoport helyét, a Kulcstár nevét, az objektumazonosítót, a jogi feltételeket és a megállapodást, majd kattintson a **Vásárlás gombra.** 


## <a name="set-up-an-azure-ad-app-and-service-principal"></a>Azure AD-alkalmazás és egyszerű szolgáltatás beállítása 
Ha titkosítást kell engedélyezni egy futó virtuális gép az Azure-ban, az Azure Disk Encryption generálja és írja a titkosítási kulcsokat a kulcstartóba. A titkosítási kulcsok kezelése a kulcstartóban Azure AD-hitelesítést igényel. Ehhez a célból hozzon létre egy Azure AD-alkalmazást. Hitelesítési célokra használhatja az ügyfél titkos hitelesítését vagy [az ügyféltanúsítvány-alapú Azure AD-hitelesítést.](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-powershell"></a>Azure AD-alkalmazás és egyszerű szolgáltatás beállítása az Azure PowerShell használatával 
A következő parancsok végrehajtásához az [Azure AD PowerShell-modul](/powershell/azure/active-directory/install-adv2)bekésése és használata. 

1. A [New-AzADApplication](/powershell/module/az.resources/new-azadapplication) PowerShell-parancsmag használatával hozzon létre egy Azure AD-alkalmazást. A MyApplicationHomePage és a MyApplicationUri tetszőleges értékek lehetnek.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. A $azureAdApplication.ApplicationId az Azure AD Ügyfélazonosító, és a $aadClientSecret az ügyféltitok, amelyet később az Azure Disk Encryption engedélyezéséhez használni fog. Az Azure AD-ügyféltitkos kulcsot megfelelően védje. Futás `$azureAdApplication.ApplicationId` megmutatja az ApplicationID.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-cli"></a>Azure AD-alkalmazás és egyszerű szolgáltatás beállítása az Azure CLI-vel

A szolgáltatásnévi tagok at Azure CLI az [az ad sp](/cli/azure/ad/sp) parancsokkal kezelheti. További információ: [Create an Azure service principal](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Hozzon létre egy új egyszerű szolgáltatás.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  A visszaadott appId az Azure AD ClientID más parancsokban használt. Ez is az az keyvault set-policy az spn.It's also the SN you'll use for az keyvault set-policy. A jelszó az ügyféltitok, amelyet később az Azure Disk Encryption engedélyezéséhez kell használnia. Az Azure AD-ügyféltitkos kulcsot megfelelően védje.
 
### <a name="set-up-an-azure-ad-app-and-service-principal-though-the-azure-portal"></a>Azure AD-alkalmazás és egyszerű szolgáltatás beállítása az Azure Portalon
A portál használata a portál használatával [hozzon létre egy Azure Active Directory-alkalmazást és egyszerű szolgáltatást, amely hozzáférhet](../../active-directory/develop/howto-create-service-principal-portal.md) az erőforrásokcikkhez egy Azure AD-alkalmazás létrehozásához. Az alább felsorolt lépésekkel közvetlenül a cikk részhez kell mennie. 

1. [A szükséges engedélyek ellenőrzése](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [Azure Active Directory-alkalmazás létrehozása](../../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - Bármilyen nevet és bejelentkezési URL-címet használhat, amelyet az alkalmazás létrehozásakor szeretne használni.
3. [Az alkalmazásazonosító és a hitelesítési kulcs beszerezése.](../../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in) 
     - A hitelesítési kulcs az ügyféltitkos kulcs, és a Set-AzVMDiskEncryptionExtension AadClientSecret részeként használatos. 
        - A hitelesítési kulcsot az alkalmazás hitelesítő adatként használja az Azure AD-be való bejelentkezéshez. Az Azure Portalon ez a titkos kulcsot kulcsoknak nevezzük, de nincs kapcsolata a kulcstartók. Biztosítsa ezt a titkot megfelelően. 
     - Az alkalmazásazonosító később a Set-AzVMDiskEncryptionExtension AadClientId azonosítójaként, valamint a Set-AzKeyVaultAccessPolicy ServicePrincipalName néven lesz használva. 

## <a name="set-the-key-vault-access-policy-for-the-azure-ad-app"></a>A kulcstartó hozzáférési szabályzatának beállítása az Azure AD-alkalmazás számára
Titkosítási titkos kulcsok írásához egy adott key vault, az Azure Disk Encryption szüksége van az ügyfél-azonosító és az ügyféltitkos kulcsot az Azure Active Directory-alkalmazás, amely rendelkezik engedélyekkel a kulcstitok írásához a Key Vault. 

> [!NOTE]
> Az Azure Disk Encryption megköveteli, hogy konfigurálja a következő hozzáférési szabályzatokat az Azure AD-ügyfélalkalmazáshoz: _WrapKey_ és _engedélyek beállítása._

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-powershell"></a>Az Azure PowerShell segítségével beállíthatja az Azure AD-alkalmazás key vault-hozzáférési szabályzatát
Az Azure AD-alkalmazás nak jogokat kell elérnie a kulcsokat vagy titkos kulcsokat a tárolóban. A [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) parancsmag használatával engedélyeket adhat az alkalmazásnak, az ügyfélazonosítót (amely az alkalmazás regisztrálásakor jött létre) a _-ServicePrincipalName_ paraméter értékként. További információ: az [Azure Key Vault – Lépésről lépésre](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)című blogbejegyzés. 

1. Állítsa be a key vault hozzáférési szabályzatot az AD-alkalmazás PowerShell.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-cli"></a>Az Azure CLI-vel beállíthatja az Azure AD-alkalmazás key vault-hozzáférési szabályzatát
Használja [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) a hozzáférési szabályzat beállításához. További információt a [Key Vault kezelése a CLI 2.0 használatával](../../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret)című témakörben talál.

Adja meg az Azure CLI-n keresztül létrehozott egyszerű szolgáltatáshozzáférést a titkos kulcsok és a wrap kulcsok beszerezéséhez a következő paranccsal:
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-the-portal"></a>Állítsa be az Azure AD-alkalmazás key vault-hozzáférési szabályzatát a portállal

1. Nyissa meg az erőforráscsoportot a key vault.
2. Jelölje ki a kulcstartót, nyissa meg az **Access Policies (Hozzáférés-házirendek)** lehetőséget, majd kattintson **az Új hozzáadása**gombra.
3. A **Fő felhasználó kiválasztása**csoportban keresse meg a létrehozott Azure AD-alkalmazást, és jelölje ki. 
4. A **kulcsengedélyekhez**a **Titkosítási műveletek csoportban**jelölje be a Kulcs **tördelése** jelölőnégyzetet.
5. A **Titkos engedélyek érti**a **Készlet** a Titkos kezelési **műveletek csoportban jelölőnégyzetet.**
6. A hozzáférési házirend mentéséhez kattintson az **OK** gombra. 

![Azure Key Vault titkosítási műveletek – Wrap kulcs](../media/disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault titkos engedélyei – Beállítás](../media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="set-key-vault-advanced-access-policies"></a>A kulcstartó speciális hozzáférési szabályzatainak beállítása
Az Azure platform hozzáférést igényel a titkosítási kulcsokat vagy titkos kulcsokat a key vaultban, hogy azok elérhetővé a virtuális gép a kulcsok indításához és a kötetek visszafejtéséhez. Engedélyezze a lemeztitkosítást a kulcstárolón, vagy a központi telepítések sikertelenek lesznek.  

### <a name="set-key-vault-advanced-access-policies-with-azure-powershell"></a>Kulcstartó speciális hozzáférési szabályzatának beállítása az Azure PowerShell használatával
 Használja a Key Vault PowerShell-parancsmag [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) a kulcstartó lemeztitkosítás engedélyezéséhez.

  - **A Kulcstároló engedélyezése lemeztitkosításhoz:** Az Azure Disk titkosításához enabledForDiskEncryption szükséges.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Szükség esetén engedélyezze a Key Vault ot a telepítéshez:** Lehetővé teszi, hogy a Microsoft.Compute erőforrás-szolgáltató titkos kulcsokat olvasson le ebből a kulcstartóból, amikor erre a key vaultra hivatkozik az erőforrás-létrehozássorán, például egy virtuális gép létrehozásakor.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Szükség esetén engedélyezze a Key Vaultot a sablon telepítéséhez:** Lehetővé teszi, hogy az Azure Resource Manager titkokat szerezzen be ebből a kulcstartóból, amikor a kulcstartóra hivatkozik egy sablon központi telepítésében.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="set-key-vault-advanced-access-policies-using-the-azure-cli"></a>Kulcstartó speciális hozzáférési szabályzatok beállítása az Azure CLI használatával
Használja [az keyvault frissítés](/cli/azure/keyvault#az-keyvault-update) a kulcstartó lemeztitkosítás engedélyezéséhez. 

 - **A Kulcstároló engedélyezése lemeztitkosításhoz:** A lemezre engedélyezett titkosítás szükséges. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Szükség esetén engedélyezze a Key Vault ot a telepítéshez:** Lehetővé teszi a virtuális gépek számára, hogy a tárolóból titkos kulcsként tárolt tanúsítványokat kérjenek le.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Szükség esetén engedélyezze a Key Vaultot a sablon telepítéséhez:** Engedélyezze az Erőforrás-kezelőnek, hogy titkokat olvasson le a tárolóból.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="set-key-vault-advanced-access-policies-through-the-azure-portal"></a>Kulcstartó speciális hozzáférési szabályzatok beállítása az Azure Portalon keresztül

1. Válassza ki a keyvaultot, nyissa meg az **Access Policies**lehetőséget, és **kattintson ide a speciális hozzáférési házirendek megjelenítéséhez.**
2. Jelölje be az **Azure Disk Encryption hozzáférés engedélyezése kötettitkosításhoz**feliratú jelölőnégyzetet.
3. Válassza **a Hozzáférés engedélyezése az Azure virtuális gépek üzembe helyezéshez** és/vagy az Access engedélyezése az Azure Resource Manager a sablon **telepítéséhez**, ha szükséges. 
4. Kattintson a **Mentés** gombra.

![Az Azure key vault speciális hozzáférési szabályzatai](../media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-optional"></a>Kulcstitkosítási kulcs beállítása (nem kötelező)
Ha kulcstitkosítási kulcsot (KEK) szeretne használni a titkosítási kulcsok további biztonsági rétegéhez, adjon hozzá egy KEK-et a kulcstartóhoz. Az [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) parancsmag használatával hozzon létre egy kulcstitkosítási kulcsot a kulcstartóban. KEK-et is importálhat a helyszíni kulcsfelügyeleti HSM-ből. További információt a [Key Vault dokumentációjában](../../key-vault/key-vault-hsm-protected-keys.md)talál. Kulcstitkosítási kulcs megadása esetén az Azure Disk Encryption ezt a kulcsot használja a titkosítási titkos kulcsok beburkolásához, mielőtt a Key Vaultba írna. 

* Kulcsok létrehozásakor használjon RSA-kulcstípust. Az Azure Disk Encryption még nem támogatja az Elliptikus ív kulcsok használatát.

* A kulcstartó titkos kulcsa és a KEK URL-címek verziószámba kell esve. Az Azure kényszeríti ezt a verziószámozási korlátozást. Az érvényes titkos és KEK URL-címeket az alábbi példákban talál:

  * Példa érvényes titkos URL-címre:*https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Példa érvényes KEK URL-re:*https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Az Azure Disk Encryption nem támogatja a portszámok megadását a key vault titkos kulcsai és a KEK URL-címek részeként. A nem támogatott és támogatott kulcstartó URL-címeiaz alábbi példákban láthatók:

  * Elfogadhatatlan kulcstartó URL-címe*https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Elfogadható kulcstartó URL-címe:*https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="set-up-a-key-encryption-key-with-azure-powershell"></a>Kulcstitkosítási kulcs beállítása az Azure PowerShell használatával 
A PowerShell-parancsfájl használata előtt ismernie kell az Azure disk titkosítási előfeltételeit a parancsfájl ban leírt lépések megismeréséhez. Előfordulhat, hogy a mintaparancsfájlt módosítani kell a környezetben. Ez a parancsfájl létrehozza az Azure disk titkosítási előfeltételeit, és titkosítja a meglévő IaaS virtuális gép, a lemez titkosítási kulcs kulcs titkosítási kulcs használatával. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name  $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName  $KVRGname;
     
 #Step 4: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
      $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="certificate-based-authentication-optional"></a>Tanúsítványalapú hitelesítés (nem kötelező)
Ha tanúsítvány-hitelesítést szeretne használni, feltölthet egyet a key vaultba, és telepítheti azt az ügyfélre. A PowerShell-parancsfájl használata előtt ismernie kell az Azure disk titkosítási előfeltételeit a parancsfájl ban leírt lépések megismeréséhez. Előfordulhat, hogy a mintaparancsfájlt módosítani kell a környezetben.

     
 ```powershell

 # Fill in "MyKeyVaultResourceGroup", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM'
   $VMRGName = 'MyVirtualMachineResourceGroup'
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="certificate-based-authentication-and-a-kek-optional"></a>Tanúsítványalapú hitelesítés és KEK (nem kötelező)

Ha tanúsítványhitelesítést szeretne használni, és a titkosítási kulcsot KEK-vel szeretné tördösni, példaként használhatja az alábbi parancsfájlt. A PowerShell-parancsfájl használata előtt ismernie kell az összes korábbi Azure disk titkosítási előfeltételeit a parancsfájl ban leírt lépések megértéséhez. Előfordulhat, hogy a mintaparancsfájlt módosítani kell a környezetben.

     
 ```powershell
# Fill in 'MyKeyVaultResourceGroup', 'MySecureVault', and 'MyLocation' (if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM';
   $VMRGName = 'MyVirtualMachineResourceGroup';
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>További lépések

[Az Azure disk encryption engedélyezése az Azure AD-vel Windows virtuális gépeken (előző kiadás)](disk-encryption-windows-aad.md)
