---
title: Kulcstartó létrehozása és konfigurálása az Azure AD-vel való Azure Disk Encryptionhoz (előző kiadás)
description: Ez a cikk ismerteti a Microsoft Azure Disk Encryption használatával IaaS-beli virtuális gépek előfeltételei.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 5a42b1e0dc82b3340bbebe176c71cb1754d00664
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79243379"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>Kulcstartó létrehozása és konfigurálása az Azure AD-vel való Azure Disk Encryptionhoz (előző kiadás)

**A Azure Disk Encryption új kiadása szükségtelenné teszi az Azure AD-alkalmazás paramétereinek biztosítását a virtuális gépek lemezes titkosításának engedélyezéséhez. Az új kiadással már nem szükséges az Azure AD-beli hitelesítő adatok megadása a titkosítás engedélyezése lépés során. Az új kiadás használatával az új virtuális gépeket az Azure AD-alkalmazás paramétereinek használata nélkül kell titkosítani. Az új kiadással a VM-lemezek titkosításának engedélyezéséhez szükséges utasításokat itt tekintheti meg: [Azure Disk Encryption](disk-encryption-overview.md). Azok a virtuális gépek, amelyek már az Azure AD-alkalmazás paramétereivel lettek titkosítva, továbbra is támogatottak, és továbbra is fenn kell tartaniuk a HRE szintaxisát**

A Azure Disk Encryption a Azure Key Vault használatával vezérli és kezeli a lemezes titkosítási kulcsokat és titkokat.  További információ a kulcstartókkal kapcsolatban: [Bevezetés a Azure Key Vault használatába](../../key-vault/key-vault-get-started.md) és [a kulcstartó biztonságossá tétele](../../key-vault/key-vault-secure-your-key-vault.md). 

A kulcstartó létrehozása és konfigurálása az Azure Disk Encryption Azure AD-vel való használatra (előző kiadás) három lépésből áll:

1. Kulcstartó létrehozása. 
2. Állítsa be az Azure AD-alkalmazást és egy szolgáltatásnevet.
3. Az Azure AD-alkalmazás számára a kulcstartó hozzáférési házirendjének beállítása.
4. Set a key vault speciális hozzáférési szabályzatok.
 
Ha szeretné, létrehozhat vagy importálhat egy kulcs-titkosítási kulcsot (KEK) is.

Az [eszközök telepítéséhez és az Azure-hoz való kapcsolódáshoz](disk-encryption-key-vault.md#install-tools-and-connect-to-azure)szükséges lépésekért tekintse meg a [Key Vault létrehozásával és konfigurálásával foglalkozó Azure Disk Encryption](disk-encryption-key-vault.md) cikket.

> [!Note]
> A cikkben ismertetett lépések a [Azure Disk Encryption előfeltételként megadott parancssori felület parancsfájljában](https://github.com/ejarvi/ade-cli-getting-started) és [Azure Disk Encryption előfeltételek PowerShell-parancsfájlban](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)találhatók.


## <a name="create-a-key-vault"></a>Kulcstartó létrehozása 
A Azure Disk Encryption integrálva van [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) a Key Vault-előfizetésben lévő lemezes titkosítási kulcsok és titkos kódok vezérléséhez és kezeléséhez. Hozzon létre egy kulcstartót, vagy használjon egy meglévőt az Azure Disk Encryption. További információ a kulcstartókkal kapcsolatban: [Bevezetés a Azure Key Vault használatába](../../key-vault/key-vault-get-started.md) és [a kulcstartó biztonságossá tétele](../../key-vault/key-vault-secure-your-key-vault.md). Resource Manager-sablonnal, az Azure PowerShell vagy az Azure CLI használatával hozzon létre egy kulcstartót. 


>[!WARNING]
>Annak érdekében, hogy a titkosítási titkos kulcsok nem adatbázisközi regionális határokat, az Azure Disk Encryption a Key Vault és a virtuális gépek közös elhelyezése ugyanabban a régióban van szüksége. Létrehozhat és használhat egy Key Vaultot, amely ugyanabban a régióban, mint a virtuális gép titkosítását. 


### <a name="create-a-key-vault-with-powershell"></a>Key Vault létrehozása a PowerShell-lel

A [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) parancsmag használatával Azure PowerShell kulcstartót hozhat létre. További parancsmagok a Key Vaulthoz: [az.](/powershell/module/az.keyvault/)kulcstartó. 

1. Szükség esetén hozzon létre egy új erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Az adatközpont helyeinek listázásához használja a [Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. Új kulcstartó létrehozása a [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) használatával
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Jegyezze fel a tár **nevét**, az **erőforráscsoport nevét**, az **erőforrás-azonosítót**, a tároló **URI-ját**és a későbbi használatra visszaadott **objektumazonosító-azonosítót** a lemezek titkosításakor. 


### <a name="create-a-key-vault-with-azure-cli"></a>Kulcstartó létrehozása az Azure CLI-vel
A Key vaultot [az az kulcstartó parancsaival](/cli/azure/keyvault#commands) kezelheti az Azure CLI használatával. Kulcstartó létrehozásához használja [az az Key Vault Create](/cli/azure/keyvault#az-keyvault-create)lehetőséget.

1. Szükség esetén hozzon létre egy új erőforráscsoportot az [az Group Create](/cli/azure/group#az-group-create)paranccsal. A helyszínek listázásához használja [az az Account List-Locations](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Hozzon létre egy új Key vaultot [az az kulcstartó Create](/cli/azure/keyvault#az-keyvault-create)paranccsal.
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Jegyezze fel a tároló **nevét** (név), az **erőforráscsoport nevét**, az **erőforrás-azonosítót** (ID), a tároló **URI azonosítóját**, valamint a később a használatra visszaadott **objektumazonosítót** . 

### <a name="create-a-key-vault-with-a-resource-manager-template"></a>Key Vault létrehozása Resource Manager-sablonnal

A Key vaultot a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)használatával lehet létrehozni.

1. Az Azure Gyorsindítás sablonon kattintson az **üzembe helyezés az Azure**-ban lehetőségre.
2. Válassza ki az előfizetést, az erőforráscsoportot, az erőforráscsoport helyét, Key Vault nevét, az objektumazonosító, a jogi feltételek és a szerződés elemet, majd kattintson a **vásárlás**elemre. 


## <a name="set-up-an-azure-ad-app-and-service-principal"></a>Azure AD-alkalmazás és egyszerű szolgáltatás beállítása 
Engedélyezni kell az Azure-ban futó virtuális gépek titkosítási van szüksége, amikor az Azure Disk Encryption állít elő, és ír a titkosítási kulcsok a key vaultban. Az Azure AD-hitelesítés kezelése a titkosítási kulcsok a key vaultban lévő igényel. Hozzon létre egy Azure AD-alkalmazást erre a célra. Hitelesítési célból az ügyfél titkos hitelesítése vagy az [ügyféltanúsítvány-alapú Azure ad-hitelesítés](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)is használható.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-powershell"></a>Azure AD-alkalmazás és egyszerű szolgáltatásnév beállítása Azure PowerShell 
A következő parancsok végrehajtásához szerezze be és használja az [Azure ad PowerShell-modult](/powershell/azure/active-directory/install-adv2). 

1. Azure AD-alkalmazás létrehozásához használja a [New-AzADApplication PowerShell-](/powershell/module/az.resources/new-azadapplication) parancsmagot. MyApplicationHomePage és a MyApplicationUri lehet a kívánt értékeket.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. A $azureAdApplication.ApplicationId az Azure AD ClientID, a $aadClientSecret pedig a titkos ügyfélkulcsot használó, később az Azure Disk Encryption engedélyezése. Megfelelő védelme érdekében az Azure AD ügyfél titkos kulcsát. A `$azureAdApplication.ApplicationId` futtatásakor a ApplicationID fog megjelenni.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-cli"></a>Azure AD-alkalmazás és egyszerű szolgáltatás beállítása az Azure CLI-vel

Az az [ad SP](/cli/azure/ad/sp) parancsaival kezelheti az egyszerű szolgáltatásokat az Azure CLI-vel. További információ: Azure- [szolgáltatásnév létrehozása](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Hozzon létre egy új egyszerű szolgáltatást.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  Az appId, az adott vissza az Azure AD ClientID használatban van más parancsok. Emellett akkor is az egyszerű Szolgáltatásnevet fogja használni az keyvault set-házirendet. A jelszó, a titkos ügyfélkulcsot, amely az Azure Disk Encryption engedélyezése később használjon. Megfelelő védelme érdekében az Azure AD ügyfél titkos kulcsát.
 
### <a name="set-up-an-azure-ad-app-and-service-principal-though-the-azure-portal"></a>Azure AD-alkalmazás és egyszerű szolgáltatásnév beállítása, ha a Azure Portal
A használati portál lépéseivel [létrehozhat egy Azure Active Directory alkalmazást és egyszerű szolgáltatást, amely hozzáférhet az erőforrásokhoz, és](../../active-directory/develop/howto-create-service-principal-portal.md) létrehoz egy Azure ad-alkalmazást. Az alábbiakban az egyes lépések léphet közvetlenül a következő cikkszakaszt befejezéséhez. 

1. [Szükséges engedélyek ellenőrzése](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [Azure Active Directory-alkalmazás létrehozása](../../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - Használhat bármilyen nevet és bejelentkezés URL-CÍMÉT szeretné az alkalmazás létrehozásakor.
3. [Kérje le az alkalmazás azonosítóját és a hitelesítési kulcsot](../../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in). 
     - A hitelesítési kulcs az ügyfél titka, és a set-AzVMDiskEncryptionExtension AadClientSecret használatos. 
        - A hitelesítési kulcs szolgál hitelesítő adatokat, az alkalmazás által jelentkezzen be Azure ad-ben. Az Azure Portalon a titkos kulcsok nevezzük, de nem rendelkezik kapcsolattal a kulcstartókhoz. A titkos kód megfelelően biztonságos. 
     - Az alkalmazás AZONOSÍTÓját később a set-AzVMDiskEncryptionExtension AadClientId fogja használni a set-AzKeyVaultAccessPolicy ServicePrincipalName. 

## <a name="set-the-key-vault-access-policy-for-the-azure-ad-app"></a>A Key Vault hozzáférési szabályzatának beállítása az Azure AD-alkalmazáshoz
Titkosítási titkos kulcsok egy megadott Key Vault ír, az Azure Disk Encryption kell rendelkeznie az ügyfél-Azonosítót és az ügyfél titkos kulcsát az Azure Active Directory-alkalmazás titkos kulcsok a Key Vault írási engedélyekkel rendelkező. 

> [!NOTE]
> Azure Disk Encryption megköveteli a következő hozzáférési házirendek konfigurálását az Azure AD-ügyfélalkalmazás számára: _WrapKey_ és az engedélyek _beállítása_ .

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-powershell"></a>A Key Vault hozzáférési szabályzatának beállítása az Azure AD-alkalmazáshoz Azure PowerShell
Az Azure AD-alkalmazást a és a tárolóban lévő titkos hozzáférési jogokkal kell. A [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) parancsmag használatával engedélyeket adhat az alkalmazásnak az ügyfél-azonosítóval (amely az alkalmazás regisztrálásakor jött létre), a _– ServicePrincipalName_ paraméter értékeként. További információkért tekintse meg a blogbejegyzés [Azure Key Vault – lépésről lépésre](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). 

1. Állítsa be a kulcstartó hozzáférési szabályzatot az AD-alkalmazás, a PowerShell használatával.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-cli"></a>A Key Vault hozzáférési szabályzatának beállítása az Azure AD-alkalmazáshoz az Azure CLI-vel
A hozzáférési házirend beállításához használja az [az Key Vault set-Policy](/cli/azure/keyvault#az-keyvault-set-policy) beállítást. További információ: [Key Vault kezelése a CLI 2,0 használatával](../../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

Adja meg az egyszerű szolgáltatás titkos kódok és Sortörés az Azure CLI-vel elérésével létrehozott kulcsokat a következő paranccsal:
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-the-portal"></a>A Key Vault hozzáférési szabályzatának beállítása az Azure AD-alkalmazáshoz a portálon

1. Nyissa meg az erőforráscsoport a key vaultban.
2. Válassza ki a kulcstartót, nyissa meg a **hozzáférési szabályzatok**elemet, majd kattintson az **új hozzáadása**lehetőségre.
3. A **rendszerbiztonsági tag kiválasztása**területen keresse meg a létrehozott Azure ad-alkalmazást, és jelölje ki. 
4. A **legfontosabb engedélyek**esetében a **titkosítási műveletek**területen keresse meg a **wrap Key (betakarás) kulcsot** .
5. A **titkos engedélyek**esetében a **beállításnál** kattintson a **titkos felügyeleti műveletek**elemre.
6. A hozzáférési házirend mentéséhez kattintson **az OK** gombra. 

![Az Azure Key Vault titkosítási-műveletek - Wrap Key](../media/disk-encryption/keyvault-portal-fig3.png)

![Az Azure Key Vault titkos kulcsából engedélyek – beállítása](../media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="set-key-vault-advanced-access-policies"></a>A Key Vault speciális hozzáférési házirendjeinek beállítása
Az Azure platform a titkosítási kulcsok vagy titkos kódok, hogy elérhetők legyenek a rendszerindítást, és visszafejti a köteteket a virtuális géphez a key vaultban lévő hozzá kell férnie. A key vault vagy a központi telepítések lemez-titkosítás engedélyezése sikertelen lesz.  

### <a name="set-key-vault-advanced-access-policies-with-azure-powershell"></a>A Key Vault speciális hozzáférési házirendjeinek beállítása Azure PowerShell
 A Key Vault PowerShell-parancsmagjának [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) használatával engedélyezheti a lemez titkosítását.

  - **Key Vault engedélyezése a lemezes titkosításhoz:** Az Azure Disk Encryption EnabledForDiskEncryption szükséges.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Key Vault telepítésének engedélyezése, ha szükséges:** Engedélyezi a Microsoft. számítási erőforrás-szolgáltató számára, hogy a kulcstartóból beolvassa a titkos kulcsokat, amikor ez a kulcstartó az erőforrás-létrehozásban hivatkozik, például virtuális gép létrehozásakor.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Szükség esetén Key Vault engedélyezése a sablonok telepítéséhez:** Lehetővé teszi a Azure Resource Manager számára, hogy a kulcstartóból beolvassa a titkos kulcsokat, ha a kulcstároló a sablon központi telepítésében van hivatkozva.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="set-key-vault-advanced-access-policies-using-the-azure-cli"></a>A Key Vault speciális hozzáférési házirendjeinek beállítása az Azure CLI használatával
A Key Vault lemezes titkosításának engedélyezéséhez használja az [az kulcstartó frissítést](/cli/azure/keyvault#az-keyvault-update) . 

 - **Key Vault engedélyezése a lemezes titkosításhoz:** Engedélyezve van a-Disk-Encryption szükséges. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Key Vault telepítésének engedélyezése, ha szükséges:** Annak engedélyezése, Virtual Machines a titkos kulcsként tárolt tanúsítványok beolvasása a tárból.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Szükség esetén Key Vault engedélyezése a sablonok telepítéséhez:** Annak engedélyezése, hogy a Resource Manager beolvassa a titkos kulcsokat a tárból.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="set-key-vault-advanced-access-policies-through-the-azure-portal"></a>A Key Vault speciális hozzáférési házirendjeinek beállítása a Azure Portal

1. Válassza ki a kulcstartót, nyissa meg a **hozzáférési szabályzatokat**, és **kattintson ide a speciális hozzáférési szabályzatok megjelenítéséhez**.
2. Jelölje be a **Azure Disk Encryptionhoz való hozzáférés engedélyezése a kötetek titkosításához**jelölőnégyzetet.
3. Ha szükséges, jelölje be az **Azure Virtual Machines való hozzáférés engedélyezése az üzembe helyezéshez** és/vagy az **Azure Resource Manager hozzáférésének engedélyezése a sablonok telepítéséhez**lehetőséget. 
4. Kattintson a **Save** (Mentés) gombra.

![Az Azure key vault speciális hozzáférési szabályzatok](../media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-optional"></a>Kulcs titkosítási kulcsának beállítása (nem kötelező)
Egy további titkosítási kulcsok biztonsági szintet szeretne kulcstitkosítási kulcs-(KEK) használatára, ha egy KEK hozzáadása a key vaultban. Az [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) parancsmaggal hozzon létre egy kulcs titkosítási kulcsot a Key vaultban. A helyszíni kulcskezelő HSM is importálhat egy KEK. További információ: [Key Vault dokumentáció](../../key-vault/key-vault-hsm-protected-keys.md). Amikor egy kulcsalapú titkosítás kulcsa van megadva, az Azure Disk Encryption a kulcs segítségével burkolhatja a titkosítási titkos kulcsait a Key Vault írása előtt. 

* Kulcsok létrehozásakor használjon RSA-kulcs típusát. Azure Disk Encryption még nem támogatja az elliptikus görbe kulcsait.

* A key vault titkos kulcsából, és KEK URL-címek verziószámmal kell lennie. Az Azure ezt a korlátozást, versioning, érvénybe lépteti. Érvényes titkos kulcsot, és KEK URL-címeket tekintse meg az alábbi példák:

  * Érvényes titkos URL-cím – példa: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Érvényes KEK URL-cím – példa: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Az Azure Disk Encryption részeként a key vault titkos kódok és KEK URL-címek megadásához használt portszámok nem támogatja. Nem támogatott, és támogatott a key vault URL-címek példákért lásd az alábbi példákat:

  * Nem fogadható el a Key Vault URL-címe *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Elfogadható Key Vault URL-cím: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="set-up-a-key-encryption-key-with-azure-powershell"></a>Kulcs titkosítási kulcsának beállítása Azure PowerShell 
A PowerShell-parancsfájl használatával, mielőtt azokat a lépéseket mutatják a parancsfájl az Azure Disk Encryption Előfeltételek tisztában kell lennie. A példaszkript környezetében előfordulhat, hogy szükség módosításokra. Ez a szkript létrehoz az összes az Azure Disk Encryption előfeltétel, és meglévő IaaS virtuális gép, a lemez titkosítási kulcs alkalmazásburkoló kulcstitkosítási kulcs használatával titkosítja. 

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
Ha Tanúsítványalapú hitelesítés használatára, töltsön fel a kulcstartóba, és telepítheti az ügyfelet. A PowerShell-parancsfájl használatával, mielőtt azokat a lépéseket mutatják a parancsfájl az Azure Disk Encryption Előfeltételek tisztában kell lennie. A példaszkript környezetében előfordulhat, hogy szükség módosításokra.

     
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

## <a name="certificate-based-authentication-and-a-kek-optional"></a>Tanúsítványalapú hitelesítés és KEK (opcionális)

Ha szeretné a Tanúsítványalapú hitelesítés használatára, és a egy KEK-titkosítási kulcs becsomagolása, használhatja az alábbi parancsfájlok példaként. A PowerShell-parancsfájl használatával, mielőtt az összes korábbi Azure Disk Encryption előfeltétel azokat a lépéseket mutatják a szkriptben tisztában kell lennie. A példaszkript környezetében előfordulhat, hogy szükség módosításokra.

     
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

[Azure Disk Encryption engedélyezése az Azure AD-vel Windows rendszerű virtuális gépeken (korábbi kiadás)](disk-encryption-windows-aad.md)
