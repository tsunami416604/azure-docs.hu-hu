---
title: Az Azure AD alkalmazás előfeltételeknek (előző kiadás) az Azure Disk Encryption |} A Microsoft Docs
description: Ez a cikk ismerteti a Microsoft Azure Disk Encryption használatával IaaS-beli virtuális gépek előfeltételei.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 09/10/2018
ms.openlocfilehash: 510ca032f77da25238ec060d4122a25345c9fb90
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44346648"
---
# <a name="azure-disk-encryption-prerequisites-previous-release"></a>Az Azure Disk Encryption előfeltétel (előző kiadás)

**Az Azure Disk Encryption új kiadása így nem egy Azure AD-alkalmazás paramétert, amelyek biztosítják a virtuális gép lemez-titkosítás engedélyezéséhez. Az új kiadással, már nem kell az Azure AD hitelesítő adatok titkosítás engedélyezése lépés során. Valamennyi új virtuális gépeket titkosítani kell az Azure AD alkalmazás paramétereit az új kiadás használata nélkül. Ahhoz, hogy az új kiadással VM lemeztitkosítás utasítások megtekintése: [az Azure Disk Encryption előfeltétel](azure-security-disk-encryption-prerequisites.md). Az Azure AD-alkalmazás paraméterekkel már titkosított virtuális gépek továbbra is támogatottak, és továbbra is fenn kell tartani az AAD-szintaxissal.**

 Ez a cikk az Azure Disk Encryption titkosítási előfeltétel, biztosítani kell, mielőtt használhatná az Azure Disk Encryption igénylő elemeket ismerteti. Általános Előfeltételek, valamint az Azure Disk Encryption szolgáltatással integrált [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) és a hitelesítés kezeléséhez a titkosítási kulcsokat a key vault az Azure AD-alkalmazást használ. Előfordulhat, hogy is használni kívánt [Azure PowerShell-lel](/powershell/azure/overview) vagy a [Azure CLI-vel](/cli/azure/) beállítását, és állítsa be a Key Vault és az Azure AD-alkalmazás.

A támogatott forgatókönyveket, amelyek a aktorcsoportot tárgyalt számára az Azure IaaS virtuális gépekhez az Azure Disk Encryption engedélyezése előtt a [Azure Disk Encryption áttekintése](azure-security-disk-encryption-overview.md) című cikket, ellenőrizze, hogy rendelkezik az előfeltételek teljesülnek. 

> [!NOTE]
> Bizonyos ajánlások növelheti az adatok, hálózati vagy számítási erőforrás-használat, ami további licencek vagy előfizetések költségeit. Érvényes aktív Azure-előfizetést hozhat létre erőforrásokat az Azure-ban támogatott régiókban kell rendelkeznie.


## <a name="bkmk_OSs"></a> A támogatott operációs rendszerek
Az Azure Disk Encryption a következő operációs rendszereken támogatott:

- Windows Server-verzió: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 és Windows Server 2016-ban.
    - A Windows Server 2008 R2, a .NET-keretrendszer 4.5 telepítve van az Azure-ban titkosítás engedélyezése előtt kell rendelkeznie. Telepítse a Windows Update a választható frissítés a Microsoft .NET-keretrendszer 4.5.2-es verziója a Windows Server 2008 R2 x64 alapú rendszerekhez ([KB2901983](https://support.microsoft.com/kb/2901983)).    
- Windows-ügyfélverziókat: Windows 8-ügyfél és a Windows 10-ügyfeleknek.
- Az Azure Disk Encryption, csak a meghatározott Azure-katalógus-alapú Linux-kiszolgáló disztribúciók és verziók. A jelenleg támogatott verziók listájáért tekintse meg a [Azure Disk Encryption – gyakori kérdések](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport).
- Az Azure Disk Encryption megköveteli, hogy a kulcstartó és a virtuális gépek található az ugyanazon Azure-régióban és az előfizetés. Az erőforrások konfigurálását külön régiókban okoz a az Azure Disk Encryption engedélyezésével.

## <a name="bkmk_LinuxPrereq"></a> Linux rendszerű Iaas virtuális gépek további Előfeltételek 

- Az Azure Disk Encryption for Linux 7 GB RAM a virtuális gép operációs rendszer lemeztitkosítás engedélyezve a szükséges [által támogatott lemezképek](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport). Az operációs rendszer lemez titkosítási folyamat befejeződése után a virtuális gép futtatása kevesebb memóriával konfigurálható.
- Titkosítás engedélyezése előtt titkosítani az adatlemezek kell /etc/fstab megfelelően szerepel. Ez a bejegyzés állandó blokk eszköz nevét a "/ dev/sdX" formátumban nevek nem támaszkodnak társítani kell ugyanazon a lemezen újraindítások, különösen akkor, ha a rendszer titkosítást alkalmaz eszközként használja. Ezt a viselkedést a további részletekért lásd: [elhárítása Linux rendszerű virtuális gép eszköznév módosítása](../virtual-machines/linux/troubleshoot-device-names-problems.md)
- Ellenőrizze, hogy a /etc/fstab beállításokat a csatlakoztatáshoz szükséges megfelelően van beállítva. Ezeket a beállításokat, a mount - parancs futtatása vagy a virtuális gépet, és aktiválja a újracsatlakoztatást ezzel a módszerrel. Miután ez befejeződött, ellenőrizze, ellenőrizze, hogy a kívánt meghajtó is csatlakoztatva van a lsblk parancs kimenete. 
    - Ha a /etc/fstab fájl nem csatlakoztatja a meghajtó megfelelő titkosítás engedélyezése előtt, az Azure Disk Encryption nem lehet megfelelően csatlakoztatásához.
    - Az Azure Disk Encryption folyamat kerül át a csatlakoztatási adatokat /etc/fstab és a saját konfigurációs fájlba a titkosítási folyamat részeként. Nem lehet terekbe megtekintéséhez a data encryption meghajtó után a /etc/fstab hiányzó bejegyzés befejeződött.
    -  Az újraindítást követően, az Azure Disk Encryption folyamat csatlakoztathatja az újonnan titkosított lemezek időt vesz igénybe. Azonnal nem érhető el a rendszer újraindítása után. A folyamat elindításához, zárolásának feloldásához, és csatlakoztassa a legyenek elérhetők más folyamatokkal való hozzáférés előtt a titkosított meghajtók idő van szüksége. Ez a folyamat a rendszer jellegétől függően a rendszer újraindítása után több mint egy percet is igénybe vehet.

Parancsok, amelyek segítségével csatlakoztathatja az adatlemezeket és a szükséges/etc/fstab-bejegyzéseket létrehozni egy példát találhat [197-205 a parancsfájl vonalak](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L197-L205). 


## <a name="bkmk_GPO"></a> Hálózat és a csoportházirend

**Ahhoz, hogy az Azure Disk Encryption szolgáltatást, az IaaS virtuális gépek hálózati végpont a következő konfigurációs követelményeknek kell megfelelnie:**
  - Szeretne csatlakozni a key vault jogkivonat beszerzéséhez, IaaS virtuális Gépen kell lennie csatlakozni egy Azure Active Directory végpontján \[login.microsoftonline.com\].
  - A titkosítási kulcsok a key vault ír, az IaaS virtuális gép tud csatlakozni a key vault-végpontot kell lennie.
  - Az IaaS virtuális gépek csatlakozni egy Azure storage-végpont, amelyen az Azure-bővítményt adattárat és a egy Azure storage-fiókot, amelyen a VHD-fájlok képesnek kell lennie.
  -  Ha a biztonsági házirend korlátozza az Internet-hozzáférést az Azure virtuális gépekről, oldja meg az előző URI-t, és konfigurálja egy adott szabályt, amely engedélyezi a kimenő kapcsolat az IP-címekről. További információkért lásd: [Azure Key Vault tűzfal mögötti](../key-vault/key-vault-access-behind-firewall.md).    


**Csoportházirend:**
 - Az Azure Disk Encryption megoldás a BitLocker külső kulcsvédő Windows IaaS virtuális gépekhez használja. Tartományhoz csatlakozó virtuális gépek esetén nem leküldéses bármely csoportházirendek, amelyeket a TPM-védőt. "A BitLocker engedélyezése a kompatibilis TPM nélküli" a csoportházirenddel kapcsolatos információkért lásd: [a BitLocker csoportházirend-hivatkozás](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#a-href-idbkmk-unlockpol1arequire-additional-authentication-at-startup).

-  Egyéni csoportházirend-tartományhoz csatlakoztatott virtuális gépeken a BitLocker-házirendnek tartalmaznia kell a következő beállítást: [-> engedélyezése 256 bites helyreállítási kulcs felhasználói tárolás konfigurálása a bitlocker helyreállítási adatainak](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Az Azure Disk Encryption sikertelen lesz, ha egyéni csoportházirend-beállítások a BitLocker nem kompatibilis. Gépeken, amelyek nem rendelkeznek a megfelelő házirend-beállítást, az új szabályzat alkalmazásának frissítése (a gpupdate.exe/Force) az új szabályzat kényszerítése és indítsa újra lehet szükség.  


## <a name="bkmk_PSH"></a> Azure PowerShell-lel
[Az Azure PowerShell](/powershell/azure/overview) használó parancsmagok készletét nyújtja a [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) modell az Azure-erőforrások kezeléséhez. Használhatja a böngészőjében az [Azure Cloud Shell](../cloud-shell/overview.md), vagy használhatja bármely PowerShell-munkamenetben az alábbi utasítások segítségével a helyi gépen telepítheti. Ha már nincs telepítve a helyi számítógépen, győződjön meg arról, hogy a legújabb Azure PowerShell SDK-verzió használatával konfigurálja az Azure Disk Encryption. Töltse le a legújabb verzióját [Azure PowerShell kiadás](https://github.com/Azure/azure-powershell/releases).

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>Azure PowerShell telepítése (nem kötelező) a helyi gépen használatra: 
1. Kövesse a hivatkozásokat az operációs rendszer, majd folytassa, ha az alábbi lépéseket a többi.      
    - [Telepítse és konfigurálja a Windows Azure PowerShell](/powershell/azure/install-azurerm-ps). 
        - A PowerShellGet, Azure PowerShell telepítése és az AzureRM-modul betöltéséhez. 
    - [Telepítse és konfigurálja az Azure Powershell macOS és Linux rendszeren](/powershell/azure/install-azurermps-maclinux).
        -  A PowerShell Core, .NET Core, az Azure PowerShell telepítése és az AzureRM.Netcore modul betöltése.
2. Telepítse a [Azure Active Directory PowerShell-modul](/powershell/azure/active-directory/install-adv2#installing-the-azure-ad-module). 

     ```powershell
     Install-Module AzureAD
     ```

3. A modulok telepített verzióinak ellenőrzése.
      ```powershell
      Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path
      Get-Module AzureAD -ListAvailable | Select-Object -Property Name,Version,Path
      ```
4. Jelentkezzen be Azure-ban a [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) parancsmagot.
     
     ```powershell
     Connect-AzureRmAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzureRmSubscription and 
     specify it with Set-AzureRmContext.  #>
     Get-AzureRmSubscription
     Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

5.  Az Azure AD Connect [Connect-AzureAD](/powershell/module/azuread/connect-azuread).
     
     ```powershell
     Connect-AzureAD
     ```

6. Felülvizsgálat [Ismerkedés az Azure PowerShell-lel](/powershell/azure/get-started-azureps) és [AzureAD](/powershell/module/azuread), ha szükséges.

## <a name="bkmk_CLI"></a> Az Azure CLI

A [Azure CLI 2.0](/cli/azure) Azure-erőforrások kezelésére szolgáló parancssori eszköz. A parancssori felület célja rugalmasan kérdezhet le adatokat, nem blokkoló hosszú ideig futó műveleteket támogatja, és ellenőrizze, hogy megkönnyítse a parancsprogramok használatát. Használhatja a böngészőjében az [Azure Cloud Shell-lel](/cloud-shell/overview.md), vagy telepítheti a helyi gépen, és használhatja bármely PowerShell-munkamenetben.

1. [Azure CLI telepítése](/cli/azure/install-azure-cli) használatra (nem kötelező) a helyi gépen:

2. A telepített verzió ellenőrzéséhez.
     
     ```azurecli-interactive
     az --version
     ``` 

3. Jelentkezzen be Azure-bA [az bejelentkezési](/cli/azure/authenticate-azure-cli).
     
     ```azurecli
     az login
     
     # If you would like to select a tenant, use: 
     az login --tenant "<tenant>"

     # If you have multiple subscriptions, get your subscription list with az account list and specify with az account set.
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. Felülvizsgálat [Azure CLI 2.0 használatának első lépései](/cli/azure/get-started-with-azure-cli) szükség esetén. 


## <a name="prerequisite-workflow-for-key-vault-and-the-azure-ad-app"></a>A Key Vault és az Azure AD-alkalmazás előfeltétel munkafolyamat

Ha már ismeri az Azure Disk Encryption Key Vault és az Azure AD szükséges előfeltételeket, használhatja a [az Azure Disk Encryption előfeltétel PowerShell-parancsprogram](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Az előfeltétel-konfigurációs parancsprogram használatáról további információkért lásd: a [titkosítani a virtuális gépek gyors üzembe helyezés](quick-encrypt-vm-powershell.md) és a [Azure Disk Encryption függelék](azure-security-disk-encryption-appendix.md#bkmk_prereq-script). 

1. Kulcstartó létrehozása. 
2. Állítsa be az Azure AD-alkalmazást és egy szolgáltatásnevet.
3. Az Azure AD-alkalmazás számára a kulcstartó hozzáférési házirendjének beállítása.
4. Set a key vault speciális hozzáférési szabályzatok.
 
## <a name="bkmk_KeyVault"></a> Kulcstartó létrehozása 
Az Azure Disk Encryption integrálva van [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) segítségével vezérelheti és felügyelheti a lemeztitkosítási kulcsokat és titkos kulcsokat a key vault-előfizetés. Hozzon létre egy kulcstartót, vagy használjon egy meglévőt az Azure Disk Encryption. Kulcstartók kapcsolatos további információkért lásd: [első lépései az Azure Key Vault](../key-vault/key-vault-get-started.md) és [kulcstartó védelme](../key-vault/key-vault-secure-your-key-vault.md). Resource Manager-sablonnal, az Azure PowerShell vagy az Azure CLI használatával hozzon létre egy kulcstartót. 


>[!WARNING]
>Annak érdekében, hogy a titkosítási titkos kulcsok nem adatbázisközi regionális határokat, az Azure Disk Encryption a Key Vault és a virtuális gépek közös elhelyezése ugyanabban a régióban van szüksége. Létrehozhat és használhat egy Key Vaultot, amely ugyanabban a régióban, mint a virtuális gép titkosítását. 


### <a name="bkmk_KVPSH"></a> Key vault létrehozása a PowerShell-lel

Key vault az Azure PowerShell használatával is létrehozhat a [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/New-AzureRmKeyVault) parancsmagot. További Key Vault-parancsmagok, lásd: [AzureRM.KeyVault](/powershell/module/azurerm.keyvault/). 

1. Ha szükséges, [csatlakozhat az Azure-előfizetés](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH). 
2. Hozzon létre egy új erőforráscsoportot, ha szükséges, a [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup).  Data center helyek listázása, használja [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocationn). 
     
     ```azurepowershell-interactive
     # Get-AzureRmLocation 
     New-AzureRmResourceGroup –Name 'MySecureRG' –Location 'East US'
     ```

3. Hozzon létre egy új kulcstartót [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/New-AzureRmKeyVault)
    
      ```azurepowershell-interactive
     New-AzureRmKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -Location 'East US'
     ```

4. Megjegyzés: a **tároló neve**, **erőforráscsoport-nevet**, **erőforrás-azonosító**, **tároló URI-ja**, és a **objektumazonosító** amely visszaadja a későbbi használatra a lemezek titkosításakor. 


### <a name="bkmk_KVCLI"></a> Kulcstartó létrehozása az Azure CLI-vel
A key vaulttal és az Azure CLI használatával kezelheti a [az keyvault](/cli/azure/keyvault#commands) parancsokat. Hozzon létre egy kulcstartót, használja a [az keyvault létrehozása](/cli/azure/keyvault#az-keyvault-create).

1. Ha szükséges, [csatlakozhat az Azure-előfizetés](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Hozzon létre egy új erőforráscsoportot, ha szükséges, a [az csoport létrehozása](/cli/azure/groupt#az-group-create). Helyek listázása, használja [az fiók list-locations](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MySecureRG" -l "East US"
     ```

3. Hozzon létre egy új kulcstartót [az keyvault létrehozása](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MySecureRG" --location "East US"
     ```

4. Megjegyzés: a **a tároló neve** (név), **erőforráscsoport-név**, **erőforrás-azonosító** (azonosító) **Vault URI**, és a **Objektumazonosító** , amely visszaadja a használati később. 

### <a name="bkmk_KVRM"></a> Key vault létrehozása a Resource Manager-sablonnal

Key vault használatával is létrehozhat a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Kattintson az Azure gyorsindítási sablon **üzembe helyezés az Azure**.
2. Válassza ki az előfizetés, erőforráscsoport, erőforráscsoport helyét, a Key Vault neve, objektumazonosító:, jogi feltételek és szerződés, és kattintson a **beszerzési**. 


## <a name="bkmk_ADapp"></a> Állítsa be az Azure AD-alkalmazás és szolgáltatás egyszerű 
Engedélyezni kell az Azure-ban futó virtuális gépek titkosítási van szüksége, amikor az Azure Disk Encryption állít elő, és ír a titkosítási kulcsok a key vaultban. Az Azure AD-hitelesítés kezelése a titkosítási kulcsok a key vaultban lévő igényel. Hozzon létre egy Azure AD-alkalmazást erre a célra. Hitelesítési célra használhatja vagy titkos kulcs-alapú ügyfélhitelesítés vagy [az Azure AD ügyféltanúsítvány-alapú ügyfél-hitelesítés](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).


### <a name="bkmk_ADappPSH"></a> Állítsa be az Azure AD-alkalmazás és szolgáltatás egyszerű, az Azure PowerShell használatával 
Hajtsa végre a következő parancsokat, lekérése és használata a [Azure AD PowerShell-modul](/powershell/azure/active-directory/install-adv2). 

1. Ha szükséges, [csatlakozhat az Azure-előfizetés](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH).
2. Használja a [New-AzureRmADApplication](/powershell/module/azurerm.resources/new-azurermadapplication) PowerShell-parancsmag segítségével hozzon létre egy Azure AD-alkalmazást. MyApplicationHomePage és a MyApplicationUri lehet a kívánt értékeket.

     ```azurepowershell-interactive
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzureRmADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. A $azureAdApplication.ApplicationId az Azure AD ClientID, a $aadClientSecret pedig a titkos ügyfélkulcsot használó, később az Azure Disk Encryption engedélyezése. Megfelelő védelme érdekében az Azure AD ügyfél titkos kulcsát. Futó `$azureAdApplication.ApplicationId` jelennek meg, az ApplicationID.


### <a name="bkmk_ADappCLI"></a> Állítsa be az Azure AD-alkalmazás és szolgáltatás egyszerű, az Azure CLI-vel

A szolgáltatásnevek Azure CLI használatával kezelheti a [az ad sp](/cli/azure/ad/sp) parancsokat. További információkért lásd: [Azure-beli szolgáltatásnév létrehozása ](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Ha szükséges, [csatlakozhat az Azure-előfizetés](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Hozzon létre egy új egyszerű szolgáltatást.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  Az appId, az adott vissza az Azure AD ClientID használatban van más parancsok. Emellett akkor is az egyszerű Szolgáltatásnevet fogja használni az keyvault set-házirendet. A jelszó, a titkos ügyfélkulcsot, amely az Azure Disk Encryption engedélyezése később használjon. Megfelelő védelme érdekében az Azure AD ügyfél titkos kulcsát.
 
### <a name="bkmk_ADappRM"></a> Állítsa be az Azure AD-alkalmazás és szolgáltatás egyszerű, ha az Azure Portalon
Lépéssorral a [egy Azure Active Directory-alkalmazás és -erőforrások eléréséhez szolgáltatásnév létrehozása a portálon](../azure-resource-manager/resource-group-create-service-principal-portal.md) hozhat létre az Azure AD-alkalmazást. Az alábbiakban az egyes lépések léphet közvetlenül a következő cikkszakaszt befejezéséhez. 

1. [Szükséges engedélyek ellenőrzése](../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions)
2. [Az Azure Active Directory-alkalmazás létrehozása](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - Használhat bármilyen nevet és bejelentkezés URL-CÍMÉT szeretné az alkalmazás létrehozásakor.
3. [Az Alkalmazásazonosító és hitelesítési kulcs](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key). 
     - A hitelesítési kulcs a titkos ügyfélkulcsot és Set-azurermvmdiskencryptionextension parancs a AadClientSecret szolgál. 
        - A hitelesítési kulcs szolgál hitelesítő adatokat, az alkalmazás által jelentkezzen be Azure ad-ben. Az Azure Portalon a titkos kulcsok nevezzük, de nem rendelkezik kapcsolattal a kulcstartókhoz. A titkos kód megfelelően biztonságos. 
     - Az Alkalmazásazonosítót később fogja használni a Set-azurermvmdiskencryptionextension parancs AadClientId és a Set-azurermkeyvaultaccesspolicy ServicePrincipalName. 

## <a name="bkmk_KVAP"></a> A kulcstartó hozzáférési szabályzatot az Azure AD-alkalmazás beállítása
Titkosítási titkos kulcsok egy megadott Key Vault ír, az Azure Disk Encryption kell rendelkeznie az ügyfél-Azonosítót és az ügyfél titkos kulcsát az Azure Active Directory-alkalmazás titkos kulcsok a Key Vault írási engedélyekkel rendelkező. 

> [!NOTE]
> Az Azure Disk Encryption megköveteli, hogy az Azure AD-ügyfélalkalmazásnak a következő hozzáférési szabályzatok konfigurálhatók: _WrapKey_ és _beállítása_ engedélyeket.

### <a name="bkmk_KVAPPSH"></a> A kulcstartó hozzáférési szabályzat az Azure AD-alkalmazás az Azure PowerShell használatával
Az Azure AD-alkalmazást a és a tárolóban lévő titkos hozzáférési jogokkal kell. Használja a [Set-AzureKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) engedélyeket az alkalmazást, az ügyfél-azonosító (amely lett létrehozva, amikor az alkalmazás regisztrálva lett) használatával a parancsmag a _– ServicePrincipalName_ paraméter értéke. További tudnivalókért tekintse meg a következő blogbejegyzésben: [Azure Key Vault - lépésről lépésre](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). 

1. Ha szükséges, [csatlakozhat az Azure-előfizetés](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH).
2. Állítsa be a kulcstartó hozzáférési szabályzatot az AD-alkalmazás, a PowerShell használatával.

     ```azurepowershell-interactive
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $rgname = 'MySecureRG'
     Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname
     ```

### <a name="bkmk_KVAPCLI"></a> A kulcstartó hozzáférési szabályzat az Azure AD-alkalmazás az Azure CLI-vel
Használat [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault.md#az-keyvault-set-policy) hozzáférési házirend beállítása. További információkért lásd: [kezelése a Key Vault parancssori felület 2.0-val](../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

1. Ha szükséges, [csatlakozhat az Azure-előfizetés](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Adja meg az egyszerű szolgáltatás titkos kódok és Sortörés az Azure CLI-vel elérésével létrehozott kulcsokat a következő paranccsal:
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="bkmk_KVAPRM"></a> A kulcstartó hozzáférési szabályzat az Azure AD-alkalmazás a portállal

1. Nyissa meg az erőforráscsoport a key vaultban.
2. Válassza ki a key vaultban, ugorjon a **hozzáférési szabályzatok**, majd kattintson a **új hozzáadása**.
3. A **válassza egyszerű**, keresse meg a létrehozott Azure AD-alkalmazást, és válassza ki azt. 
4. A **Kulcsengedélyek**, ellenőrizze **Wrap Key** alatt **titkosítási műveletek**.
5. A **titkos kód engedélyei**, ellenőrizze **beállítása** alatt **titkos műveletek**.
6. Kattintson a **OK** a hozzáférési szabályzat mentéséhez. 

![Az Azure Key Vault titkosítási-műveletek - Wrap Key](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Az Azure Key Vault titkos kulcsából engedélyek – beállítása ](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

## <a name="bkmk_KVper"></a> Speciális hozzáférési szabályzatok beállítása a key vault
Az Azure platform a titkosítási kulcsok vagy titkos kódok, hogy elérhetők legyenek a rendszerindítást, és visszafejti a köteteket a virtuális géphez a key vaultban lévő hozzá kell férnie. A key vault vagy a központi telepítések lemez-titkosítás engedélyezése sikertelen lesz.  

### <a name="bkmk_KVperPSH"></a> Speciális hozzáférési szabályzatok az Azure PowerShell használatával a key vault beállítása
 A key vault PowerShell-parancsmag [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) lemez titkosítása a key vault engedélyezése.

  - **A Key Vault engedélyezése lemeztitkosítás:** EnabledForDiskEncryption szükség az Azure Disk encryption.
      
     ```azurepowershell-interactive 
     Set-AzureRmKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -EnabledForDiskEncryption
     ```

  - **A Key Vault engedélyezése a központi telepítéshez, szükség esetén:** lehetővé teszi, hogy a Microsoft.Compute erőforrás-szolgáltató titkos kódjainak beolvasására a kulcstartóban, amikor ez a key vault erőforrás-létrehozás, például egy virtuális gép létrehozásakor hivatkozik.

     ```azurepowershell-interactive
      Set-AzureRmKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -EnabledForDeployment
     ```

  - **A Key Vault engedélyezése a sablon üzembe helyezéshez, szükség esetén:** Azure Resource Manager lehetővé teszi, hogy a titkos kódok lekéréséhez a kulcstartóban, ha a kulcstartó hivatkozik egy sablon telepítése.

     ```azurepowershell-interactive             
     Set-AzureRmKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -EnabledForTemplateDeployment`
     ```

### <a name="bkmk_KVperCLI"></a> Set a key vault speciális hozzáférési szabályzatok az Azure CLI használatával
Használat [az keyvault update](/cli/azure/keyvault#az-keyvault-update) lemez titkosítása a key vault engedélyezése. 

 - **A Key Vault engedélyezése lemeztitkosítás:** engedélyezve a lemez titkosításra szükség. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MySecureRG" --enabled-for-disk-encryption "true"
     ```  

 - **A Key Vault engedélyezése a központi telepítéshez, szükség esetén:** lehetővé virtuális gépek a tárolóból titkos kódként tárolt tanúsítványok lekérése.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MySecureRG" --enabled-for-deployment "true"
     ``` 

 - **A Key Vault engedélyezése a sablon üzembe helyezéshez, szükség esetén:** Resource Manager lehetővé teszi a titkos kódok lekérése a tárolóból.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MySecureRG" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a> Speciális hozzáférési szabályzatok az Azure Portalon a key vault beállítása

1. Válassza ki a keyvault, ugorjon a **hozzáférési szabályzatok**, és **kattintson ide a speciális hozzáférési szabályzatok megjelenítéséhez**.
2. Jelölje be az **engedélyezze a hozzáférést az Azure Disk Encryption kötettitkosítást**.
3. Válassza ki **engedélyezze a hozzáférést az Azure Virtual Machines üzembe helyezési** és/vagy **hozzáférés engedélyezése az Azure Resource Manager-sablon üzembe helyezéshez**, ha szükséges. 
4. Kattintson a **Save** (Mentés) gombra.

![Az Azure key vault speciális hozzáférési szabályzatok](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a> Állítsa be a kulcsalapú titkosítás kulcsa (nem kötelező)
Egy további titkosítási kulcsok biztonsági szintet szeretne kulcstitkosítási kulcs-(KEK) használatára, ha egy KEK hozzáadása a key vaultban. Használja a [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) parancsmaggal hozzon létre egy fő titkosítási kulcsot a key vaultban. A helyszíni kulcskezelő HSM is importálhat egy KEK. További információkért lásd: [Key Vault-dokumentáció](../key-vault/key-vault-hsm-protected-keys.md). Amikor egy kulcsalapú titkosítás kulcsa van megadva, az Azure Disk Encryption a kulcs segítségével burkolhatja a titkosítási titkos kulcsait a Key Vault írása előtt. 

* A key vault titkos kulcsából, és KEK URL-címek verziószámmal kell lennie. Az Azure ezt a korlátozást, versioning, érvénybe lépteti. Érvényes titkos kulcsot, és KEK URL-címeket tekintse meg az alábbi példák:

  * Példa: egy érvényes titkos URL-címe:   *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Példa: egy érvényes KEK URL-cím:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Az Azure Disk Encryption részeként a key vault titkos kódok és KEK URL-címek megadásához használt portszámok nem támogatja. Nem támogatott, és támogatott a key vault URL-címek példákért lásd az alábbi példákat:

  * Érvénytelen a key vault URL-címe  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Elfogadható a key vault URL-címe:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="bkmk_KEKPSH"></a> Állítsa be a kulcsalapú titkosítás kulcsa az Azure PowerShell használatával 
A PowerShell-parancsfájl használatával, mielőtt azokat a lépéseket mutatják a parancsfájl az Azure Disk Encryption Előfeltételek tisztában kell lennie. A példaszkript környezetében előfordulhat, hogy szükség módosításokra. Ez a szkript létrehoz az összes az Azure Disk Encryption előfeltétel, és meglévő IaaS virtuális gép, a lemez titkosítási kulcs alkalmazásburkoló kulcstitkosítási kulcs használatával titkosítja. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MySecureRG', and 'MySecureVault' with your values.
     # Use Get-AzureRmLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzureRmResourceGroup
     
     $Loc = 'MyLocation';
     $rgname = 'MySecureRG';
     $KeyVaultName = 'MySecureVault'; 
     New-AzureRmResourceGroup –Name $rgname –Location $Loc;
     New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname -Location $Loc;
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $KeyVaultResourceId = (Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzureRmADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDiskEncryption;
     Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname;
     
 #Step 4: Create a new key in the key vault with the Add-AzureKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' with your value. 
     
     $VMName = 'MySecureVM';
     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="bkmk_Cert"></a> Tanúsítványalapú hitelesítés (nem kötelező)
Ha Tanúsítványalapú hitelesítés használatára, töltsön fel a kulcstartóba, és telepítheti az ügyfelet. A PowerShell-parancsfájl használatával, mielőtt azokat a lépéseket mutatják a parancsfájl az Azure Disk Encryption Előfeltételek tisztában kell lennie. A példaszkript környezetében előfordulhat, hogy szükség módosításokra.

     
 ```powershell

 # Fill in "MySecureRG", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $rgname = 'MySecureRG'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname -Location $Loc
   Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzureRmADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname
   
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
   Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' with your value.

   $VMName = 'MySecureVM'
   $CertUrl = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname).ResourceId
   $VM = Get-AzureRmVM -ResourceGroupName $rgname -Name $VMName 
   $VM = Add-AzureRmVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzureRmVM -VM $VM -ResourceGroupName $rgname 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="bkmk_CertKEK"></a> Tanúsítványalapú hitelesítés és a egy kek-KEL (nem kötelező)

Ha szeretné a Tanúsítványalapú hitelesítés használatára, és a egy KEK-titkosítási kulcs becsomagolása, használhatja az alábbi parancsfájlok példaként. A PowerShell-parancsfájl használatával, mielőtt az összes korábbi Azure Disk Encryption előfeltétel azokat a lépéseket mutatják a szkriptben tisztában kell lennie. A példaszkript környezetében előfordulhat, hogy szükség módosításokra.

> [!IMPORTANT]
> Az Azure AD-alapú hitelesítés jelenleg nem támogatott Linuxos virtuális gépeken.



     
 ```powershell
# Fill in 'MySecureRG', 'MySecureVault', and 'MyLocation' (if needed)

   $rgname = 'MySecureRG'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname -Location $Loc
   Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzureRmADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

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
   Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' with your value.

   $VMName = 'MySecureVM'
   $CertUrl = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname).ResourceId
   $VM = Get-AzureRmVM -ResourceGroupName $rgname -Name $VMName 
   $VM = Add-AzureRmVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzureRmVM -VM $VM -ResourceGroupName $rgname 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [A Windows Azure Disk Encryption engedélyezése](azure-security-disk-encryption-windows-aad.md)

> [!div class="nextstepaction"]
> [Linuxhoz készült Azure Disk Encryption engedélyezése](azure-security-disk-encryption-linux-aad.md)
