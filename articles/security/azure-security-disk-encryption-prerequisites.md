---
title: Előfeltételek – IaaS virtuális gépekhez az Azure Disk Encryption |} A Microsoft Docs
description: Ez a cikk ismerteti a Microsoft Azure Disk Encryption használatával IaaS-beli virtuális gépek előfeltételei.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 09/30/2019
ms.custom: seodec18
ms.openlocfilehash: 551918373f8292d798980600d6e0d43add55bd18
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828272"
---
# <a name="azure-disk-encryption-prerequisites"></a>Az Azure Disk Encryption előfeltételei

Ez a cikk az Azure Disk Encryption titkosítási előfeltétel, biztosítani kell, mielőtt használhatná az Azure Disk Encryption igénylő elemeket ismerteti. Az Azure Disk Encryption integrálva van [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) titkosítási kulcsok kezeléséhez. Használhat [Azure PowerShell-lel](/powershell/azure/overview), [Azure CLI-vel](/cli/azure/), vagy a [az Azure portal](https://portal.azure.com) konfigurálása az Azure Disk Encryption.

A támogatott forgatókönyveket, amelyek a aktorcsoportot tárgyalt számára az Azure IaaS virtuális gépekhez az Azure Disk Encryption engedélyezése előtt a [Azure Disk Encryption áttekintése](azure-security-disk-encryption-overview.md) című cikket, ellenőrizze, hogy rendelkezik az előfeltételek teljesülnek. 

> [!WARNING]
> - Ha korábban már használta [Azure Disk Encryption az Azure ad-alkalmazással](azure-security-disk-encryption-prerequisites-aad.md) a virtuális gép titkosításához, akkor továbbra is ezt a beállítást kell használnia a virtuális gép titkosításához. Ezen a titkosított virtuális gépen nem használható [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) , mivel ez nem támogatott forgatókönyv, ami azt jelenti, hogy a titkosított virtuális gép esetében a HRE alkalmazásból való átállás még nem támogatott.
> - Bizonyos ajánlások növelheti az adatok, hálózati vagy számítási erőforrás-használat, ami további licencek vagy előfizetések költségeit. Érvényes aktív Azure-előfizetést hozhat létre erőforrásokat az Azure-ban támogatott régiókban kell rendelkeznie.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-vm-sizes"></a>Támogatott virtuálisgép-méretek

Azure Disk Encryption nem érhető el az [alapszintű, a sorozatú virtuális gépeken](https://azure.microsoft.com/pricing/details/virtual-machines/series/). Azure Disk Encryption a minimális memória követelményeinek megfelelő más virtuális gépeken érhető el:

| Virtuális gép | Minimális memória követelménye |
|--|--|
| Windows rendszerű virtuális gépek | 2 GB |
| Linux rendszerű virtuális gépek, ha csak az adatkötetek titkosítását| 2 GB |
| Linux rendszerű virtuális gépek az adatok és az operációsrendszer-kötetek titkosításakor, valamint a gyökér (/) fájlrendszer használatának helye 4 GB vagy kevesebb | 8 GB |
| Linux rendszerű virtuális gépek az adatok és az operációsrendszer-kötetek titkosítása esetén, valamint a gyökér (/) fájlrendszer használatának helye meghaladja a 4GB-ot | A rendszerindító fájlrendszer használata * 2. Például egy 16 GB-os rendszerszintű fájlrendszer-használat legalább 32GB RAM memóriát igényel |

Miután az operációsrendszer-lemez titkosítási folyamata befejeződött a Linux rendszerű virtuális gépeken, a virtuális gép beállítható úgy, hogy kevesebb memóriával fusson. 

> [!NOTE]
> A Linux operációsrendszer-lemez titkosítása [Virtual Machine Scale sets](../virtual-machine-scale-sets/index.yml)esetén nem érhető el.

A Premium Storage szolgáltatással rendelkező virtuális gépek esetében Azure Disk Encryption is elérhető. 

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

### <a name="windows"></a>Windows

- Windows-ügyfél: Windows 8 és újabb verziók.
- Windows Server: Windows Server 2008 R2 és újabb verziók.  
 
> [!NOTE]
> A Windows Server 2008 R2 megköveteli, hogy a .NET-keretrendszer 4,5 legyen telepítve a titkosításhoz; telepítse azt Windows Update a Windows Server 2008 R2 x64-alapú rendszerek ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)) választható frissítés Microsoft .net keretrendszere 4.5.2-es verziójával.  
>  
> A Windows Server 2012 R2 Core és a Windows Server 2016 Core megköveteli, hogy a bdehdcfg összetevőt a virtuális gépre telepítse a titkosításhoz.


### <a name="linux"></a>Linux 

A Azure Disk Encryption az [Azure által támogatott Linux-disztribúciók](../virtual-machines/linux/endorsed-distros.md)egy részhalmazán támogatott, amely az összes Linux-kiszolgáló lehetséges disztribúciójának részhalmaza.

![A Azure Disk Encryptiont támogató Linux Server-disztribúciók Venn ábrája](./media/azure-security-disk-encryption-faq/ade-supported-distros.png)

Az Azure által nem támogatott Linux Server-disztribúciók nem támogatják Azure Disk Encryptiont, és a támogatottak közül csak a következő disztribúciók és verziók támogatják a Azure Disk Encryption:

| Linux-disztribúció | Verzió | A kötettípus titkosítás támogatott|
| --- | --- |--- |
| Ubuntu | 18,04| Operációsrendszer- és lemez |
| Ubuntu | 16.04| Operációsrendszer- és lemez |
| Ubuntu | 14.04.5</br>[Az Azure-ban beállított kernel 4,15-es vagy újabb verzióra frissült](azure-security-disk-encryption-tsg.md#bkmk_Ubuntu14) | Operációsrendszer- és lemez |
| RHEL | 7,7 | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 7,6 | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 7.5 | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 7.4 | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 7.3 | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 7.2 | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 6.8 | Adatlemez (lásd az alábbi megjegyzést) |
| RHEL | 6.7 | Adatlemez (lásd az alábbi megjegyzést) |
| CentOS | 7,7 | Operációsrendszer- és lemez |
| CentOS | 7,6 | Operációsrendszer- és lemez |
| CentOS | 7.5 | Operációsrendszer- és lemez |
| CentOS | 7.4 | Operációsrendszer- és lemez |
| CentOS | 7.3 | Operációsrendszer- és lemez |
| CentOS | 7.2n | Operációsrendszer- és lemez |
| CentOS | 6.8 | Adatlemez |
| openSUSE | 42,3 | Adatlemez |
| SLES | 12-SP4 | Adatlemez |
| SLES | 12-SP3 | Adatlemez |

> [!NOTE]
> Az új ADE-implementáció a RHEL operációs rendszer és az adatlemez RHEL7-elszámolású lemezképekhez való használata esetén támogatott. Az ADE jelenleg nem támogatott a RHEL saját előfizetéses (BYOS) lemezképek esetében. További információ: [Azure Disk Encryption for Linux](azure-security-disk-encryption-linux.md) .

- Az Azure Disk Encryption megköveteli, hogy a kulcstartó és a virtuális gépek található az ugyanazon Azure-régióban és az előfizetés. Az erőforrások konfigurálását külön régiókban okoz a az Azure Disk Encryption engedélyezésével.

#### <a name="additional-prerequisites-for-linux-iaas-vms"></a>A Linux IaaS virtuális gépekre vonatkozó további előfeltételek 

- Azure Disk Encryption megköveteli, hogy a dm-crypt és a VFAT modulok jelen legyenek a rendszeren. A VFAT eltávolítása vagy letiltása az alapértelmezett rendszerképből megakadályozza, hogy a rendszer beolvassa a kulcs kötetét, és beszerezze a lemezek zárolásának feloldásához szükséges kulcsot a későbbi újraindítások során. A VFAT modul rendszerből való eltávolítására szolgáló rendszer-megerősítési lépések nem kompatibilisek a Azure Disk Encryptionokkal. 
- Titkosítás engedélyezése előtt titkosítani az adatlemezek kell /etc/fstab megfelelően szerepel. Ez a bejegyzés állandó blokk eszköz nevét a "/ dev/sdX" formátumban nevek nem támaszkodnak társítani kell ugyanazon a lemezen újraindítások, különösen akkor, ha a rendszer titkosítást alkalmaz eszközként használja. A viselkedésről további részleteket a következő témakörben talál: [A Linux rendszerű virtuális gép eszköz nevének módosításainak megoldása](../virtual-machines/linux/troubleshoot-device-names-problems.md)
- Ellenőrizze, hogy a /etc/fstab beállításokat a csatlakoztatáshoz szükséges megfelelően van beállítva. Ezeket a beállításokat, a mount - parancs futtatása vagy a virtuális gépet, és aktiválja a újracsatlakoztatást ezzel a módszerrel. Miután ez befejeződött, ellenőrizze, ellenőrizze, hogy a meghajtó is csatlakoztatva van a lsblk parancs kimenete. 
  - Ha a /etc/fstab fájl megfelelően titkosítás engedélyezése előtt nem csatlakoztatja a meghajtót, az Azure Disk Encryption nem lehet megfelelően csatlakoztatásához.
  - Az Azure Disk Encryption folyamat kerül át a csatlakoztatási adatokat /etc/fstab és a saját konfigurációs fájlba a titkosítási folyamat részeként. Nem lehet terekbe megtekintéséhez a data encryption meghajtó után a /etc/fstab hiányzó bejegyzés befejeződött.
  - A titkosítás megkezdése előtt mindenképpen állítsa le az összes olyan szolgáltatást és folyamatot, amely a csatlakoztatott adatlemezekre írhat, és tiltsa le őket, hogy újraindítás után ne induljon el automatikusan. Ezek megtarthatják a fájlok megnyitását ezeken a partíciókon, így megakadályozva a titkosítási eljárás újracsatlakoztatását, ami hibát okoz a titkosításban. 
  - Az újraindítást követően, az Azure Disk Encryption folyamat csatlakoztathatja az újonnan titkosított lemezek időt vesz igénybe. Ezek nem érhető el azonnal a számítógép újraindítása után. A folyamat elindításához, zárolásának feloldásához, és csatlakoztassa a titkosított meghajtók legyenek elérhetők más folyamatokkal való hozzáférés előtt időt kell. Ez a folyamat a rendszer jellegétől függően a rendszer újraindítása után több mint egy percet is igénybe vehet.

Parancsok, amelyek segítségével csatlakoztathatja az adatlemezeket és a szükséges/etc/fstab-bejegyzéseket létrehozni egy példát találhat [244-248 a parancsfájl vonalak](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L244-L248). 

## <a name="bkmk_GPO"></a> Hálózat és a csoportházirend

**Ahhoz, hogy az Azure Disk Encryption szolgáltatást, az IaaS virtuális gépek hálózati végpont a következő konfigurációs követelményeknek kell megfelelnie:**
  - Szeretne csatlakozni a key vault jogkivonat beszerzéséhez, IaaS virtuális Gépen kell lennie csatlakozni egy Azure Active Directory végpontján \[login.microsoftonline.com\].
  - A titkosítási kulcsok a key vault ír, az IaaS virtuális gép tud csatlakozni a key vault-végpontot kell lennie.
  - Az IaaS virtuális gépek csatlakozni egy Azure storage-végpont, amelyen az Azure-bővítményt adattárat és a egy Azure storage-fiókot, amelyen a VHD-fájlok képesnek kell lennie.
  -  Ha a biztonsági házirend korlátozza az Internet-hozzáférést az Azure virtuális gépekről, oldja meg az előző URI-t, és konfigurálja egy adott szabályt, amely engedélyezi a kimenő kapcsolat az IP-címekről. További információkért lásd: [Azure Key Vault tűzfal mögötti](../key-vault/key-vault-access-behind-firewall.md).    


**Csoportházirend:**
 - Az Azure Disk Encryption megoldás a BitLocker külső kulcsvédő Windows IaaS virtuális gépekhez használja. Tartományhoz csatlakozó virtuális gépek esetén nem leküldéses bármely csoportházirendek, amelyeket a TPM-védőt. "A BitLocker engedélyezése a kompatibilis TPM nélküli" a csoportházirenddel kapcsolatos információkért lásd: [a BitLocker csoportházirend-hivatkozás](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  A tartományhoz csatlakoztatott, egyéni csoportházirendtel rendelkező virtuális gépekre vonatkozó BitLocker-házirendnek a következő beállítást kell tartalmaznia: [Konfigurálja a BitLocker helyreállítási adatok felhasználói tárolóját – > engedélyezze a 256 bites helyreállítási kulcsot](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). A Azure Disk Encryption sikertelen lesz, ha a BitLocker egyéni csoportházirend-beállításai nem kompatibilisek. Gépeken, amelyek nem rendelkeznek a megfelelő házirend-beállítást, az új szabályzat alkalmazásának frissítése (a gpupdate.exe/Force) az új szabályzat kényszerítése és indítsa újra lehet szükség.

- A Azure Disk Encryption sikertelen lesz, ha a tartományi szintű csoportházirend letiltja a BitLocker által használt AES-CBC algoritmust.


## <a name="bkmk_PSH"></a> Azure PowerShell-lel
[Az Azure PowerShell](/powershell/azure/overview) használó parancsmagok készletét nyújtja a [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) modell az Azure-erőforrások kezeléséhez. Használhatja a böngészőjében az [Azure Cloud Shell](../cloud-shell/overview.md), vagy használhatja bármely PowerShell-munkamenetben az alábbi utasítások segítségével a helyi gépen telepítheti. Ha már nincs telepítve a helyi számítógépen, győződjön meg arról, hogy a legújabb Azure PowerShell SDK-verzió használatával konfigurálja az Azure Disk Encryption. Töltse le a legújabb verzióját [Azure PowerShell kiadás](https://github.com/Azure/azure-powershell/releases).

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>Azure PowerShell telepítése (nem kötelező) a helyi gépen használatra: 
1. Kövesse a hivatkozásokat az operációs rendszer, majd folytassa, ha az alábbi lépéseket a többi.      
   - [Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-az-ps). 
     - Telepítse a PowerShellGet, Azure PowerShell és töltse be az az modult. 

2. Ellenőrizze az az modul telepített verzióit. Ha szükséges, [frissítése az Azure PowerShell-modul](/powershell/azure/install-az-ps#update-the-azure-powershell-module).
    Az az modul legújabb verziójának használata ajánlott.

     ```powershell
     Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
     ```

3. Jelentkezzen be az Azure-ba a [Kapcsolódás-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag használatával.
     
     ```azurepowershell-interactive
     Connect-AzAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzAccount –Environment (Get-AzEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzSubscription and 
     specify it with Set-AzContext.  #>
     Get-AzSubscription
     Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
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
Ha már ismeri az Azure Disk Encryption Key Vault és az Azure AD szükséges előfeltételeket, használhatja a [az Azure Disk Encryption előfeltétel PowerShell-parancsprogram](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Az előfeltétel-konfigurációs parancsprogram használatáról további információkért lásd: a [titkosítani a virtuális gépek gyors üzembe helyezés](../virtual-machines/linux/disk-encryption-powershell-quickstart.md) és a [Azure Disk Encryption függelék](azure-security-disk-encryption-appendix.md#bkmk_prereq-script). 

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

A [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) parancsmag használatával Azure PowerShell kulcstartót hozhat létre. További parancsmagok a Key Vaulthoz: [az.](/powershell/module/az.keyvault/)kulcstartó. 

1. Ha szükséges, [csatlakozhat az Azure-előfizetés](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH). 
2. Szükség esetén hozzon létre egy új erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Az adatközpont helyeinek listázásához használja a [Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

3. Új kulcstartó létrehozása a [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) használatával
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Megjegyzés: a **tároló neve**, **erőforráscsoport-nevet**, **erőforrás-azonosító**, **tároló URI-ja**, és a **objektumazonosító** amely visszaadja a későbbi használatra a lemezek titkosításakor. 


### <a name="bkmk_KVCLI"></a> Kulcstartó létrehozása az Azure CLI-vel
A key vaulttal és az Azure CLI használatával kezelheti a [az keyvault](/cli/azure/keyvault#commands) parancsokat. Hozzon létre egy kulcstartót, használja a [az keyvault létrehozása](/cli/azure/keyvault#az-keyvault-create).

1. Ha szükséges, [csatlakozhat az Azure-előfizetés](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Hozzon létre egy új erőforráscsoportot, ha szükséges, a [az csoport létrehozása](/cli/azure/group#az-group-create). Helyek listázása, használja [az fiók list-locations](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Hozzon létre egy új kulcstartót [az keyvault létrehozása](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Megjegyzés: a **a tároló neve** (név), **erőforráscsoport-név**, **erőforrás-azonosító** (azonosító) **Vault URI**, és a **Objektumazonosító** , amely visszaadja a használati később. 

### <a name="bkmk_KVRM"></a> Key vault létrehozása a Resource Manager-sablonnal

Key vault használatával is létrehozhat a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Kattintson az Azure gyorsindítási sablon **üzembe helyezés az Azure**.
2. Válassza ki az előfizetés, erőforráscsoport, erőforráscsoport helyét, a Key Vault neve, objektumazonosító:, jogi feltételek és szerződés, és kattintson a **beszerzési**. 


## <a name="bkmk_KVper"></a> Speciális hozzáférési szabályzatok beállítása a key vault
Az Azure platform a titkosítási kulcsok vagy titkos kódok, hogy elérhetők legyenek a rendszerindítást, és visszafejti a köteteket a virtuális géphez a key vaultban lévő hozzá kell férnie. A key vault vagy a központi telepítések lemez-titkosítás engedélyezése sikertelen lesz.  

### <a name="bkmk_KVperPSH"></a> Speciális hozzáférési szabályzatok az Azure PowerShell használatával a key vault beállítása
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

### <a name="bkmk_KVperCLI"></a> Set a key vault speciális hozzáférési szabályzatok az Azure CLI használatával
Használat [az keyvault update](/cli/azure/keyvault#az-keyvault-update) lemez titkosítása a key vault engedélyezése. 

 - **Key Vault engedélyezése a lemezes titkosításhoz:** Engedélyezve van a-Disk-Encryption szükséges. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Key Vault telepítésének engedélyezése, ha szükséges:** Engedélyezi a Microsoft. számítási erőforrás-szolgáltató számára, hogy a kulcstartóból beolvassa a titkos kulcsokat, amikor ez a kulcstartó az erőforrás-létrehozásban hivatkozik, például virtuális gép létrehozásakor.

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Szükség esetén Key Vault engedélyezése a sablonok telepítéséhez:** Annak engedélyezése, hogy a Resource Manager beolvassa a titkos kulcsokat a tárból.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a> Speciális hozzáférési szabályzatok az Azure Portalon a key vault beállítása

1. Válassza ki a keyvault, ugorjon a **hozzáférési szabályzatok**, és **kattintson ide a speciális hozzáférési szabályzatok megjelenítéséhez**.
2. Jelölje be az **engedélyezze a hozzáférést az Azure Disk Encryption kötettitkosítást**.
3. Válassza ki **engedélyezze a hozzáférést az Azure Virtual Machines üzembe helyezési** és/vagy **hozzáférés engedélyezése az Azure Resource Manager-sablon üzembe helyezéshez**, ha szükséges. 
4. Kattintson a **Save** (Mentés) gombra.

    ![Az Azure key vault speciális hozzáférési szabályzatok](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a> Állítsa be a kulcsalapú titkosítás kulcsa (nem kötelező)
Egy további titkosítási kulcsok biztonsági szintet szeretne kulcstitkosítási kulcs-(KEK) használatára, ha egy KEK hozzáadása a key vaultban. Az [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) parancsmaggal hozzon létre egy kulcs titkosítási kulcsot a Key vaultban. A helyszíni kulcskezelő HSM is importálhat egy KEK. További információkért lásd: [Key Vault-dokumentáció](../key-vault/key-vault-hsm-protected-keys.md). Amikor egy kulcsalapú titkosítás kulcsa van megadva, az Azure Disk Encryption a kulcs segítségével burkolhatja a titkosítási titkos kulcsait a Key Vault írása előtt.

* Kulcsok létrehozásakor használjon RSA-kulcs típusát. Azure Disk Encryption még nem támogatja az elliptikus görbe kulcsait.

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
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname).VaultUri;
     
 #Step 2: Enable the vault for disk encryption.
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
      
 #Step 3: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'HSM';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 4: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```


 
## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [A Windows Azure Disk Encryption engedélyezése](azure-security-disk-encryption-windows.md)

> [!div class="nextstepaction"]
> [Linuxhoz készült Azure Disk Encryption engedélyezése](azure-security-disk-encryption-linux.md)

