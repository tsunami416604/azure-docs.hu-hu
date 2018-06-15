---
title: A Windows Azure-ban lévő lemezek titkosítása |} Microsoft Docs
description: A fokozott biztonság Azure PowerShell használatával Windows virtuális gép virtuális lemezek titkosítása
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/07/2018
ms.author: iainfou
ms.openlocfilehash: 442ff942150af8a8dec89164fbc017a9e6f360e8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31603742"
---
# <a name="how-to-encrypt-virtual-disks-on-a-windows-vm"></a>Virtuális lemezek a Windows virtuális gép titkosítása
A bővített virtuális gép (VM) biztonsági és megfelelőségi az Azure-ban virtuális lemezek titkosíthatók. Lemezek titkosítása egy Azure Key Vault a titkosított titkosítási kulcsok használatával. Szabályozhatja a titkosítási kulcsokat, és a használatukat naplózhatók. Ez a cikk részletesen a Azure PowerShell használatával Windows virtuális gép virtuális lemezek titkosítása. Emellett [Linux virtuális gépet az Azure CLI 2.0 titkosítása](../linux/encrypt-disks.md).

## <a name="overview-of-disk-encryption"></a>Lemeztitkosítás áttekintése
A Windows virtuális gépek virtuális lemezek vannak titkosítása a Bitlocker használatával. Nincs nem kell fizetni az Azure-ban virtuális lemezek titkosítására. Titkosítási kulcsok Azure Key Vault szoftver-védelemmel vannak tárolva, vagy importálhat vagy a tanúsított FIPS 140-2 2. szint szabványok hardveres biztonsági modulokkal (HSM) a kulcsok létrehozásához. Ezek a titkosítási kulcsok titkosítására és visszafejtésére a virtuális Géphez csatolt virtuális lemezek segítségével. A titkosítási kulcsokat a felügyeletet, és naplózhatja a használatukat. Egy egyszerű Azure Active Directory szolgáltatás lehetővé teszi a biztonságos kiadása a titkosítási kulcsokat, a virtuális gépek vannak kapcsolva, és ki.

A virtuális gépek titkosításához a folyamat a következőképpen történik:

1. Hozzon létre egy Azure Key Vault egy titkosítási kulcsot.
2. Állítsa be a titkosítási kulccsal, hogy a lemezek titkosítására használható.
3. Az Azure Key Vault beolvasni a titkosítási kulcsot, hozzon létre egy Azure Active Directory szolgáltatás egyszerű a megfelelő engedélyekkel.
4. Adja ki a parancsot, a virtuális lemezek, adja meg az Azure Active Directory szolgáltatás egyszerű és megfelelő titkosítási használandó kulcs titkosításához.
5. Az Azure Active Directory szolgáltatás egyszerű kér az Azure Key Vault a szükséges titkosítási kulcs.
6. A virtuális lemezek vannak titkosítva, a megadott titkosítási kulcs használatával.

## <a name="encryption-process"></a>Titkosítási folyamat
Adatok titkosítása a következő további összetevők támaszkodik:

* **Az Azure Key Vault** – a lemez titkosítási/visszafejtési folyamathoz használt titkosítási kulcsok és titkos biztosításához használt. 
  * Ha van ilyen, használhat egy meglévő Azure Key Vault. Nem jelölt ki a kulcstároló, a lemezek titkosító rendelkeznek.
  * Külön felügyeleti határokat és a kulcs látható, a dedikált kulcstároló is létrehozhat.
* **Az Azure Active Directory** -kezeli a szükséges titkosítási kulcsokat és a kért műveletek hitelesítési biztonságos cseréjét. 
  * Az alkalmazás elhelyezésére szolgáló használhat meglévő Azure Active Directory-példány általában.
  * Az egyszerű szolgáltatás lehetővé teszi a biztonságos le tudja kérni, és adja ki a megfelelő titkosítási kulcsokat. Nem fejleszt, amely az Azure Active Directory tényleges kérelmet.

## <a name="requirements-and-limitations"></a>Követelmények és korlátozások
Támogatott esetek és lemez titkosítására vonatkozó követelményekkel kapcsolatos:

* Az Azure piactéren elérhető rendszerkép vagy egyéni Virtuálismerevlemez-kép új Windows virtuális gépeken futó titkosítás engedélyezése.
* A meglévő Windows virtuális gépeken, az Azure-titkosítás engedélyezése.
* A Windows virtuális gépeken, a tárolóhelyek szolgáltatással konfigurált titkosítás engedélyezése.
* Az operációs rendszer és az adatok titkosításának letiltása meghajtók Windows virtuális gépek esetén.
* Az azonos Azure-régió, és az előfizetés összes erőforrások (például a Key Vault, a tárfiók és a virtuális gép) kell lennie.
* Standard szint virtuális gépeket, például egy, virtuális gépek D, DS, G és GS adatsorozat.

Lemeztitkosítás jelenleg nem támogatott a következő esetekben:

* Az alapszintű csomag virtuális gépeket.
* A virtuális gépek létrehozása a klasszikus telepítési modell használatával.
* A titkosítási kulcsok egy már titkosított virtuális gép frissítése.
* Integráció a helyszíni kulcskezelő szolgáltatást.

## <a name="create-azure-key-vault-and-keys"></a>Az Azure Key Vault és kulcsok létrehozása
A kezdés előtt győződjön meg arról, hogy az Azure PowerShell-modul legújabb verziója telepítve van. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál. Egész a parancspéldákban cserélje le minden példa paraméter a saját nevét, helyét és kulcsértékek. Az alábbi példák a szabályt használ *myResourceGroup*, *myKeyVault*, *myVM*stb.

Az első lépés, ha az Azure Key Vault a kriptográfiai kulcsok tárolásához. Az Azure Key Vault kulcsok, a titkos kulcsok és a jelszót, amely engedélyezi, hogy biztonságosan végrehajtja az alkalmazások és szolgáltatások a tárolhatja. A virtuális lemez titkosításához hozzon létre egy Key Vault titkosítására vagy visszafejtésére a virtuális lemezek használt kriptográfiai kulcs tárolása. 

Engedélyezze az Azure Key Vault-szolgáltató az Azure-előfizetése belül [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider), majd hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Az alábbi példa létrehoz egy erőforráscsoport neve *myResourceGroup* a a *USA keleti régiója* helye:

```powershell
$rgName = "myResourceGroup"
$location = "East US"

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzureRmResourceGroup -Location $location -Name $rgName
```

Az Azure Key Vault a kriptográfiai kulcsokat és tartozó számítási erőforrásokat, például a tároló és a virtuális gépért tartalmazó ugyanabban a régióban kell lennie. Hozzon létre egy Azure Key Vault a [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) , és engedélyezze a Key Vault lemeztitkosítás való használatra. Adjon meg egy egyedi kulcstároló nevet *keyVaultName* az alábbiak szerint:

```powershell
$keyVaultName = "myUniqueKeyVaultName"
New-AzureRmKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

A szoftver vagy a biztonsági modell (HSM) védelmi kriptográfiai kulcsokat is tárolhatja. A Key Vault prémium HSM használata szükséges. Nincs a prémium szabványos Key Vault szoftveresen védett tároló helyett kulcstároló létrehozásához további költségek. A prémium kulcstároló létrehozásához az előző lépésben adja hozzá a *- Sku "Prémium"* paraméterek. Az alábbi példában szoftveresen védett azt a szabványos kulcstároló létrehozása óta. 

Mindkét védelmi modellek esetében az Azure platformon kell hozzáférést kérni a titkosítási kulcsokat a virtuális lemezek visszafejtése a virtuális gép indításakor. A kulcstároló, a titkosítási kulcs létrehozására [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey). Az alábbi példakód létrehozza nevű kulcs *SajátKulcs*:

```powershell
Add-AzureKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```


## <a name="create-the-azure-active-directory-service-principal"></a>Az Azure Active Directory szolgáltatás egyszerű létrehozása
Ha a virtuális lemezek vannak titkosított vagy visszafejtett, meg kell adnia egy fiókot a hitelesítési és titkosítási kulcsok a Key Vault cseréjét. Ezt a fiókot, egy Azure Active Directory szolgáltatás egyszerű, lehetővé teszi, hogy az Azure platformon, kérje a megfelelő titkosítási kulcsokat a virtuális gép nevében. Azure Active Directory alapértelmezett példányán érhető előfizetését, bár számos szervezet Azure Active Directory-könyvtárak dedikált.

Egy egyszerű szolgáltatás létrehozása az Azure Active Directoryban [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal). Biztonságos jelszó megadásához kövesse a [jelszóházirendek és -korlátozások az Azure Active Directory](../../active-directory/authentication/concept-sspr-policy.md):

```powershell
$appName = "My App"
$securePassword = ConvertTo-SecureString -String "P@ssw0rd!" -AsPlainText -Force
$app = New-AzureRmADApplication -DisplayName $appName `
    -HomePage "https://myapp.contoso.com" `
    -IdentifierUris "https://contoso.com/myapp" `
    -Password $securePassword
New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
```

Sikeresen vagy titkosítására vagy visszafejtésére virtuális lemezek, a Key Vault tárolt titkosítási kulcs engedélyei lehetővé teszik az Azure Active Directory szolgáltatás egyszerű az a kulcsok beolvasása a értékre kell állítani. A kulcstároló, az engedélyeket [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy):

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyvaultName `
    -ServicePrincipalName $app.ApplicationId `
    -PermissionsToKeys "WrapKey" `
    -PermissionsToSecrets "Set"
```


## <a name="create-virtual-machine"></a>Virtuális gép létrehozása
A titkosítási folyamat teszteléséhez a virtuális gép létrehozása az [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm). Az alábbi példakód létrehozza a virtuális gépek nevű *myVM* használatával egy *Windows Server 2016 Datacenter* kép. Amikor a hitelesítő adatok megadását kéri, adja meg a felhasználónevet és jelszót a virtuális Géphez használandó:

```powershell
$cred = Get-Credential

New-AzureRmVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-virtual-machine"></a>Virtuális gép titkosítása
A virtuális lemez titkosításához, kapcsolása együtt minden az előző összetevők működését:

1. Adja meg az Azure Active Directory szolgáltatás egyszerű és a jelszót.
2. Adja meg a Key Vault a titkosított lemezek metaadatait tárolja.
3. Adja meg a titkosítási kulcsok használandó tényleges titkosításához és visszafejtéséhez.
4. Adja meg, hogy az operációsrendszer-lemezképet, az adatlemezek vagy az összes titkosításához.

Az a virtuális gép titkosítása [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) használata az Azure Key Vault-kulcsot és az Azure Active Directory szolgáltatás egyszerű hitelesítő adatait. Az alábbi példa lekéri a kulccsal kapcsolatos adatokat, majd titkosítja a virtuális gép nevű *myVM*:

```powershell
$keyVault = Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -AadClientID $app.ApplicationId `
    -AadClientSecret (New-Object PSCredential "user",$securePassword).GetNetworkCredential().Password `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Fogadja el a rendszer a virtuális gép titkosítási folytatásához. A folyamat során a virtuális gép újraindul. Miután a titkosítási folyamat befejeződött, és a virtuális gép újraindítása, tekintse át a titkosítás állapotát az [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus):

```powershell
Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

A kimenet a következő példához hasonló:

```powershell
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>További lépések
* További információ az Azure Key Vault kezeléséhez: [Key Vault beállítása a virtuális gépek](key-vault-setup.md).
* Lemeztitkosítás, például egy titkosított egyéni virtuális Gépre az Azure-ba, a feltöltendő előkészítése kapcsolatos további információk: [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
