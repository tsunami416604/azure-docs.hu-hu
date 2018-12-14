---
title: Előfeltételek – IaaS virtuális gépekhez az Azure Disk Encryption |} A Microsoft Docs
description: Ez a cikk ismerteti a Microsoft Azure Disk Encryption használatával IaaS-beli virtuális gépek előfeltételei.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 12/13/2018
ms.custom: seodec18
ms.openlocfilehash: 116f1f0a93c09ed751f0720ae74a2c24df7541eb
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53342510"
---
# <a name="azure-disk-encryption-prerequisites"></a>Az Azure Disk Encryption előfeltételei

 Ez a cikk az Azure Disk Encryption titkosítási előfeltétel, biztosítani kell, mielőtt használhatná az Azure Disk Encryption igénylő elemeket ismerteti. Az Azure Disk Encryption integrálva van [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) titkosítási kulcsok kezeléséhez. Használhat [Azure PowerShell-lel](/powershell/azure/overview), [Azure CLI-vel](/cli/azure/), vagy a [az Azure portal](https://portal.azure.com) konfigurálása az Azure Disk Encryption.

A támogatott forgatókönyveket, amelyek a aktorcsoportot tárgyalt számára az Azure IaaS virtuális gépekhez az Azure Disk Encryption engedélyezése előtt a [Azure Disk Encryption áttekintése](azure-security-disk-encryption-overview.md) című cikket, ellenőrizze, hogy rendelkezik az előfeltételek teljesülnek. 

> [!NOTE]
> Bizonyos ajánlások növelheti az adatok, hálózati vagy számítási erőforrás-használat, ami további licencek vagy előfizetések költségeit. Érvényes aktív Azure-előfizetést hozhat létre erőforrásokat az Azure-ban támogatott régiókban kell rendelkeznie.


## <a name="bkmk_OSs"></a> A támogatott operációs rendszerek
Az Azure Disk Encryption a következő operációs rendszereken támogatott:

- A Windows Server következő verziói: A Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 és Windows Server 2016-ban.
    - A Windows Server 2008 R2, a .NET-keretrendszer 4.5 telepítve van az Azure-ban titkosítás engedélyezése előtt kell rendelkeznie. Telepítse a Windows Update a választható frissítés a Microsoft .NET-keretrendszer 4.5.2-es verziója a Windows Server 2008 R2 x64 alapú rendszerekhez ([KB2901983](https://support.microsoft.com/kb/2901983)).    
- Windows-ügyfélverziókat: Windows 8-ügyfél és a Windows 10-ügyfeleknek.
- Az Azure Disk Encryption, csak a meghatározott Azure-katalógus-alapú Linux-kiszolgáló disztribúciók és verziók. A jelenleg támogatott verziók listájáért tekintse meg a [Azure Disk Encryption – gyakori kérdések](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport).
- Az Azure Disk Encryption megköveteli, hogy a kulcstartó és a virtuális gépek található az ugyanazon Azure-régióban és az előfizetés. Az erőforrások konfigurálását külön régiókban okoz a az Azure Disk Encryption engedélyezésével.

## <a name="bkmk_LinuxPrereq"></a> Linux rendszerű Iaas virtuális gépek további Előfeltételek 

- Az Azure Disk Encryption for Linux 7 GB RAM a virtuális gép operációs rendszer lemeztitkosítás engedélyezve a szükséges [által támogatott lemezképek](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport). Az operációs rendszer lemez titkosítási folyamat befejeződése után a virtuális gép futtatása kevesebb memóriával konfigurálható.
- Titkosítás engedélyezése előtt titkosítani az adatlemezek kell /etc/fstab megfelelően szerepel. Ez a bejegyzés állandó blokk eszköz nevét a "/ dev/sdX" formátumban nevek nem támaszkodnak társítani kell ugyanazon a lemezen újraindítások, különösen akkor, ha a rendszer titkosítást alkalmaz eszközként használja. Ezt a viselkedést a további részletekért lásd: [Hibaelhárítás a Linux rendszerű virtuális gép eszköznév módosítása](../virtual-machines/linux/troubleshoot-device-names-problems.md)
- Ellenőrizze, hogy a /etc/fstab beállításokat a csatlakoztatáshoz szükséges megfelelően van beállítva. Ezeket a beállításokat, a mount - parancs futtatása vagy a virtuális gépet, és aktiválja a újracsatlakoztatást ezzel a módszerrel. Miután ez befejeződött, ellenőrizze, ellenőrizze, hogy a meghajtó is csatlakoztatva van a lsblk parancs kimenete. 
    - Ha a /etc/fstab fájl megfelelően titkosítás engedélyezése előtt nem csatlakoztatja a meghajtót, az Azure Disk Encryption nem lehet megfelelően csatlakoztatásához.
    - Az Azure Disk Encryption folyamat kerül át a csatlakoztatási adatokat /etc/fstab és a saját konfigurációs fájlba a titkosítási folyamat részeként. Nem lehet terekbe megtekintéséhez a data encryption meghajtó után a /etc/fstab hiányzó bejegyzés befejeződött.
    -  Az újraindítást követően, az Azure Disk Encryption folyamat csatlakoztathatja az újonnan titkosított lemezek időt vesz igénybe. Ezek nem érhető el azonnal a számítógép újraindítása után. A folyamat elindításához, zárolásának feloldásához, és csatlakoztassa a titkosított meghajtók legyenek elérhetők más folyamatokkal való hozzáférés előtt időt kell. Ez a folyamat a rendszer jellegétől függően a rendszer újraindítása után több mint egy percet is igénybe vehet.

Parancsok, amelyek segítségével csatlakoztathatja az adatlemezeket és a szükséges/etc/fstab-bejegyzéseket létrehozni egy példát találhat [244-248 a parancsfájl vonalak](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L244-L248). 


## <a name="bkmk_GPO"></a> Hálózat és a csoportházirend

**Ahhoz, hogy az Azure Disk Encryption szolgáltatást, az IaaS virtuális gépek hálózati végpont a következő konfigurációs követelményeknek kell megfelelnie:**
  - Szeretne csatlakozni a key vault jogkivonat beszerzéséhez, IaaS virtuális Gépen kell lennie csatlakozni egy Azure Active Directory végpontján \[login.microsoftonline.com\].
  - A titkosítási kulcsok a key vault ír, az IaaS virtuális gép tud csatlakozni a key vault-végpontot kell lennie.
  - Az IaaS virtuális gépek csatlakozni egy Azure storage-végpont, amelyen az Azure-bővítményt adattárat és a egy Azure storage-fiókot, amelyen a VHD-fájlok képesnek kell lennie.
  -  Ha a biztonsági házirend korlátozza az Internet-hozzáférést az Azure virtuális gépekről, oldja meg az előző URI-t, és konfigurálja egy adott szabályt, amely engedélyezi a kimenő kapcsolat az IP-címekről. További információkért lásd: [Azure Key Vault tűzfal mögötti](../key-vault/key-vault-access-behind-firewall.md).    


**Csoportházirend:**
 - Az Azure Disk Encryption megoldás a BitLocker külső kulcsvédő Windows IaaS virtuális gépekhez használja. Tartományhoz csatlakozó virtuális gépek esetén nem leküldéses bármely csoportházirendek, amelyeket a TPM-védőt. "A BitLocker engedélyezése a kompatibilis TPM nélküli" a csoportházirenddel kapcsolatos információkért lásd: [a BitLocker csoportházirend-hivatkozás](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#a-href-idbkmk-unlockpol1arequire-additional-authentication-at-startup).

-  Egyéni csoportházirend-tartományhoz csatlakoztatott virtuális gépeken a BitLocker-házirendnek tartalmaznia kell a következő beállítást: [Felhasználói tárolás konfigurálása a bitlocker helyreállítási adatainak engedélyezése 256 bites helyreállítási kulcs ->](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Az Azure Disk Encryption sikertelen lesz, ha egyéni csoportházirend-beállítások a BitLocker nem kompatibilis. Gépeken, amelyek nem rendelkeznek a megfelelő házirend-beállítást, az új szabályzat alkalmazásának frissítése (a gpupdate.exe/Force) az új szabályzat kényszerítése és indítsa újra lehet szükség.  


## <a name="bkmk_PSH"></a> Azure PowerShell-lel
[Az Azure PowerShell](/powershell/azure/overview) használó parancsmagok készletét nyújtja a [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) modell az Azure-erőforrások kezeléséhez. Használhatja a böngészőjében az [Azure Cloud Shell](../cloud-shell/overview.md), vagy használhatja bármely PowerShell-munkamenetben az alábbi utasítások segítségével a helyi gépen telepítheti. Ha már nincs telepítve a helyi számítógépen, győződjön meg arról, hogy a legújabb Azure PowerShell SDK-verzió használatával konfigurálja az Azure Disk Encryption. Töltse le a legújabb verzióját [Azure PowerShell kiadás](https://github.com/Azure/azure-powershell/releases).

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>Azure PowerShell telepítése (nem kötelező) a helyi gépen használatra: 
1. Kövesse a hivatkozásokat az operációs rendszer, majd folytassa, ha az alábbi lépéseket a többi.      
    - [Telepítse és konfigurálja a Windows Azure PowerShell](/powershell/azure/install-azurerm-ps). 
        - A PowerShellGet, Azure PowerShell telepítése és az AzureRM-modul betöltéséhez. 
    - [Telepítse és konfigurálja az Azure Powershell macOS és Linux rendszeren](/powershell/azure/install-azurermps-maclinux).
        -  A PowerShell Core, .NET Core, az Azure PowerShell telepítése és a Az modul betöltésére.

2. Az AzureRM-modul telepített verzióinak ellenőrzése. Ha szükséges, [frissítése az Azure PowerShell-modul](/powershell/azure/install-azurerm-ps#update-the-azure-powershell-module).
    -  Az AzureRM-modul verziószáma 6.0.0 vagy annál magasabb legyen.
    - Az AzureRM modul legújabb verziója használata javasolt.

     ```powershell
     Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path
     ```

3. Jelentkezzen be Azure-ban a [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) parancsmagot.
     
     ```azurepowershell-interactive
     Connect-AzureRmAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzureRmSubscription and 
     specify it with Set-AzureRmContext.  #>
     Get-AzureRmSubscription
     Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

4.  Ha szükséges, tekintse át a [Ismerkedés az Azure PowerShell-lel](/powershell/azure/get-started-azureps).

## <a name="bkmk_CLI"></a> Használható az Azure CLI telepítése (nem kötelező) a helyi gépen

A [Azure CLI 2.0](/cli/azure) Azure-erőforrások kezelésére szolgáló parancssori eszköz. A parancssori felület célja rugalmasan kérdezhet le adatokat, nem blokkoló hosszú ideig futó műveleteket támogatja, és ellenőrizze, hogy megkönnyítse a parancsprogramok használatát. Használhatja a böngészőjében az [Azure Cloud Shell-lel](../cloud-shell/overview.md), vagy telepítheti a helyi gépen, és használhatja bármely PowerShell-munkamenetben.

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


## <a name="prerequisite-workflow-for-key-vault"></a>A Key vault előfeltétel munkafolyamat
Ha már ismeri az Azure Disk Encryption Key Vault és az Azure AD szükséges előfeltételeket, használhatja a [az Azure Disk Encryption előfeltétel PowerShell-parancsprogram](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Az előfeltétel-konfigurációs parancsprogram használatáról további információkért lásd: a [titkosítani a virtuális gépek gyors üzembe helyezés](quick-encrypt-vm-powershell.md) és a [Azure Disk Encryption függelék](azure-security-disk-encryption-appendix.md#bkmk_prereq-script). 

1. Ha szükséges, hozzon létre egy erőforráscsoportot.
2. Kulcstartó létrehozása. 
3. Set a key vault speciális hozzáférési szabályzatok.

>[!WARNING]
>Mielőtt törölné a key vault, győződjön meg arról, hogy olyan meglévő virtuális gépek, nem fejeződött titkosítja. A tároló védelmére véletlen törlés [helyreállítható Törlés engedélyezése](../key-vault/key-vault-soft-delete-powershell.md#enabling-soft-delete) és a egy [erőforrászárat](../azure-resource-manager/resource-group-lock-resources.md) a táron. 
 
## <a name="bkmk_KeyVault"></a> Kulcstartó létrehozása 
Az Azure Disk Encryption integrálva van [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) segítségével vezérelheti és felügyelheti a lemeztitkosítási kulcsokat és titkos kulcsokat a key vault-előfizetés. Hozzon létre egy kulcstartót, vagy használjon egy meglévőt az Azure Disk Encryption. Kulcstartók kapcsolatos további információkért lásd: [első lépései az Azure Key Vault](../key-vault/key-vault-get-started.md) és [kulcstartó védelme](../key-vault/key-vault-secure-your-key-vault.md). Resource Manager-sablonnal, az Azure PowerShell vagy az Azure CLI használatával hozzon létre egy kulcstartót. 


>[!WARNING]
>Annak érdekében, hogy a titkosítási titkos kulcsok nem adatbázisközi regionális határokat, az Azure Disk Encryption a Key Vault és a virtuális gépek közös elhelyezése ugyanabban a régióban van szüksége. Létrehozhat és használhat egy Key Vaultot, amely ugyanabban a régióban, mint a virtuális gép titkosítását. 


### <a name="bkmk_KVPSH"></a> Key vault létrehozása a PowerShell-lel

Key vault az Azure PowerShell használatával is létrehozhat a [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/New-AzureRmKeyVault) parancsmagot. További Key Vault-parancsmagok, lásd: [AzureRM.KeyVault](/powershell/module/azurerm.keyvault/). 

1. Ha szükséges, [csatlakozhat az Azure-előfizetés](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH). 
2. Hozzon létre egy új erőforráscsoportot, ha szükséges, a [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup).  Data center helyek listázása, használja [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation). 
     
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
2. Hozzon létre egy új erőforráscsoportot, ha szükséges, a [az csoport létrehozása](/cli/azure/group#az-group-create). Helyek listázása, használja [az fiók list-locations](/cli/azure/account#az-account-list) 
     
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


## <a name="bkmk_KVper"></a> Speciális hozzáférési szabályzatok beállítása a key vault
Az Azure platform a titkosítási kulcsok vagy titkos kódok, hogy elérhetők legyenek a rendszerindítást, és visszafejti a köteteket a virtuális géphez a key vaultban lévő hozzá kell férnie. A key vault vagy a központi telepítések lemez-titkosítás engedélyezése sikertelen lesz.  

### <a name="bkmk_KVperPSH"></a> Speciális hozzáférési szabályzatok az Azure PowerShell használatával a key vault beállítása
 A key vault PowerShell-parancsmag [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) lemez titkosítása a key vault engedélyezése.

  - **Lemeztitkosítás Key Vault engedélyezése:** EnabledForDiskEncryption szükség az Azure Disk encryption.
      
     ```azurepowershell-interactive 
     Set-AzureRmKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -EnabledForDiskEncryption
     ```

  - **Szükség esetén engedélyezze a Key Vault üzembe helyezés:** Lehetővé teszi a Microsoft.Compute erőforrás-szolgáltató titkos kódjainak beolvasására a kulcstartóban, amikor ez a key vault erőforrás-létrehozás, például egy virtuális gép létrehozásakor hivatkozik.

     ```azurepowershell-interactive
      Set-AzureRmKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -EnabledForDeployment
     ```

  - **A Key Vault engedélyezése központi telepítési sablont, szükség esetén:** Lehetővé teszi, hogy a kulcstartóban titkos kódok lekéréséhez, ha a kulcstartó hivatkozik egy sablon telepítése Azure Resource Managerrel.

     ```azurepowershell-interactive             
     Set-AzureRmKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -EnabledForTemplateDeployment
     ```

### <a name="bkmk_KVperCLI"></a> Set a key vault speciális hozzáférési szabályzatok az Azure CLI használatával
Használat [az keyvault update](/cli/azure/keyvault#az-keyvault-update) lemez titkosítása a key vault engedélyezése. 

 - **Lemeztitkosítás Key Vault engedélyezése:** Engedélyezve van-az-lemez-titkosításra szükség. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MySecureRG" --enabled-for-disk-encryption "true"
     ```  

 - **Szükség esetén engedélyezze a Key Vault üzembe helyezés:** Lehetővé teszi a Microsoft.Compute erőforrás-szolgáltató titkos kódjainak beolvasására a kulcstartóban, amikor ez a key vault erőforrás-létrehozás, például egy virtuális gép létrehozásakor hivatkozik.

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MySecureRG" --enabled-for-deployment "true"
     ``` 

 - **A Key Vault engedélyezése központi telepítési sablont, szükség esetén:** Lehetővé teszi a Resource Manager beolvasni a titkos kulcsok a tárolóból.
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
     
 #Step 2: Enable the vault for disk encryption.
     Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDiskEncryption;
      
 #Step 3: Create a new key in the key vault with the Add-AzureKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 4: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' with your value. 
     
     $VMName = 'MySecureVM';
     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```


 
## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [A Windows Azure Disk Encryption engedélyezése](azure-security-disk-encryption-windows.md)

> [!div class="nextstepaction"]
> [Linuxhoz készült Azure Disk Encryption engedélyezése](azure-security-disk-encryption-linux.md)

