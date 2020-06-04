---
title: AD DS hitelesítés engedélyezése az Azure-fájlmegosztás számára
description: Megtudhatja, hogyan engedélyezheti Active Directory tartományi szolgáltatások hitelesítést az SMB-en keresztül az Azure-fájlmegosztás esetében. A tartományhoz csatlakoztatott Windows-alapú virtuális gépek ezután AD DS hitelesítő adatok használatával érhetik el az Azure-fájlmegosztást.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: rogarana
ms.openlocfilehash: 4423067fde70728a5449485434cc40c5c3d3ee8f
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84324096"
---
# <a name="part-one-enable-ad-ds-authentication-for-your-azure-file-shares"></a>Első rész: az Azure-fájlmegosztás AD DS hitelesítésének engedélyezése 

A Active Directory tartományi szolgáltatások (AD DS) hitelesítés engedélyezése előtt olvassa el az [áttekintő cikket](storage-files-identity-auth-active-directory-enable.md) a támogatott forgatókönyvek és követelmények megismeréséhez.

Ez a cikk a Active Directory tartományi szolgáltatások (AD DS) hitelesítésének engedélyezéséhez szükséges folyamatot ismerteti a Storage-fiókban. A szolgáltatás engedélyezése után konfigurálnia kell a Storage-fiókot és a AD DS, hogy AD DS hitelesítő adatokat lehessen használni az Azure-fájlmegosztás hitelesítéséhez. Az Azure-fájlmegosztás SMB-en keresztüli AD DS hitelesítésének engedélyezéséhez regisztrálnia kell a Storage-fiókját a AD DS, majd be kell állítania a szükséges tartományi tulajdonságokat a Storage-fiókban. Ha a szolgáltatás engedélyezve van a Storage-fiókon, akkor az a fiók összes új és meglévő fájljára érvényes lesz.

## <a name="option-one-recommended-use-the-script"></a>1. lehetőség (ajánlott): használja a parancsfájlt.

A cikkben található szkript végrehajtja a szükséges módosításokat, és lehetővé teszi a funkció használatát. Mivel a szkript egyes részei a helyszíni AD DSkal együttműködnek, elmagyarázjuk, mi a szkript, így megállapíthatja, hogy a módosítások összhangban vannak-e a megfelelőségi és biztonsági házirendekkel, és hogy rendelkezik-e a megfelelő engedélyekkel a szkript végrehajtásához. Bár a szkript használatát javasoljuk, ha nem tudja megtenni ezt, a lépéseket manuálisan is végrehajthatja.

### <a name="script-prerequisites"></a>Parancsfájlok előfeltételei

- [Töltse le és csomagolja ki a AzFilesHybrid modult](https://github.com/Azure-Samples/azure-files-samples/releases)
- Telepítse és hajtsa végre a modult egy olyan eszközön, amely tartományhoz van csatlakoztatva a helyszíni AD DS olyan AD DS hitelesítő adatokkal, amelyek engedéllyel rendelkeznek a szolgáltatás bejelentkezési fiókjának vagy a célszámítógép fiókjának létrehozásához a cél AD-ben.
-  Futtassa a parancsfájlt egy helyszíni AD DS hitelesítő adat használatával, amely szinkronizálva van az Azure AD-vel. A helyszíni AD DS hitelesítő adatnak rendelkeznie kell a Storage-fiók tulajdonosával vagy a közreműködő RBAC szerepkör-jogosultságokkal.

### <a name="offline-domain-join"></a>Kapcsolat nélküli tartományhoz való csatlakozás

A `Join-AzStorageAccountForAuth` parancsmag egy kapcsolat nélküli tartományhoz való csatlakozást hajt végre a megadott Storage-fiók nevében. A szkript a parancsmag használatával hoz létre egy fiókot az AD-tartományban, vagy egy [számítógépfiók](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (alapértelmezett) vagy egy [szolgáltatás bejelentkezési fiókja](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). Ha a parancs manuális futtatását választja, válassza ki a környezete számára legmegfelelőbb fiókot.

A parancsmag által létrehozott AD DS fiók a Storage-fiókot jelöli. Ha a AD DS fiók egy szervezeti egység (OU) alatt jön létre, amely kikényszeríti a jelszó lejáratát, akkor a jelszó maximális kora előtt frissítenie kell a jelszót. Sikertelen volt a fiók jelszavának frissítése, mielőtt a kapu hitelesítési hibát eredményez az Azure-fájlmegosztás elérésekor. A jelszó frissítésével kapcsolatos további információkért lásd: [AD DS fiók jelszavának frissítése](storage-files-identity-ad-ds-update-password.md).

### <a name="use-the-script-to-enable-ad-ds-authentication"></a>AD DS hitelesítés engedélyezése a parancsfájl használatával

Ne felejtse el lecserélni a helyőrző értékeket az alábbi paraméterekkel, mielőtt végrehajtja a PowerShellben.
> [!IMPORTANT]
> A tartományhoz való csatlakozás parancsmag létrehoz egy AD-fiókot, amely a Storage-fiókot (fájlmegosztást) az AD-ben fogja ábrázolni. Megadhatja, hogy számítógép-fiókként vagy szolgáltatás-bejelentkezési fiókként regisztrálja a részleteket a [Gyakori kérdések](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) részben. Számítógépfiókok esetében az alapértelmezett jelszó lejárati ideje 30 nap múlva az AD-ben van beállítva. Hasonlóképpen, előfordulhat, hogy a szolgáltatás bejelentkezési fiókja az AD-tartományon vagy a szervezeti egységen (OU) beállított alapértelmezett jelszó-lejárati kort tartalmaz.
> Mindkét fióktípus esetében javasoljuk, hogy tekintse meg az AD-környezetben konfigurált jelszó lejárati idejét, és tervezze meg, hogy a jelszó maximális kora előtt [frissítse a Storage-fiók identitásának jelszavát](storage-files-identity-ad-ds-update-password.md) . Érdemes lehet [új ad szervezeti egységet (OU-t) létrehozni az ad-ben](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) , és ennek megfelelően letiltani a jelszó lejárati házirendjét a [számítógép fiókjain](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN) vagy a szolgáltatás bejelentkezési fiókjain. 

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Define parameters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account, depends on the AD permission you have and preference. 
# Run Get-Help Join-AzStorageAccountForAuth for more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -Name $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" ` #Default set to "ComputerAccount" if parameter is omitted
        -OrganizationalUnitName "<ou-name-here>" #You can also use -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" instead. If you don't provide the OU name as an input parameter, the AD identity that represents the storage account will be created under the root directory.

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, see Azure Files Windows troubleshooting guide.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="option-2-manually-perform-the-script-actions"></a>2. lehetőség: a parancsfájl műveleteinek manuális végrehajtása

Ha a `Join-AzStorageAccountForAuth` fenti szkriptet már sikeresen végrehajtotta, lépjen a [funkció megerősítése lehetőségre](#confirm-the-feature-is-enabled) . A következő kézi lépéseket nem kell végrehajtania.

### <a name="checking-environment"></a>Környezet ellenőrzése

Először ellenőriznie kell a környezet állapotát. Pontosabban ellenőriznie kell, hogy telepítve van-e a [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) , és hogy a rendszerhéj rendszergazdai jogosultságokkal van-e végrehajtva. Ezután ellenőrizze, hogy az az [. Storage 2,0 modul](https://www.powershellgallery.com/packages/Az.Storage/2.0.0) telepítve van-e, és ha nem, akkor telepítse. Az ellenőrzések elvégzése után ellenőrizze a AD DS, hogy van-e olyan [számítógépfiók](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (alapértelmezett) vagy [szolgáltatás-bejelentkezési fiók](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) , amely már létrehozva van SPN/UPN néven "CIFS/a-Storage-Account-Name-here. file. Core. Windows. net". Ha a fiók nem létezik, hozzon létre egyet a következő szakaszban leírtak szerint.

### <a name="creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>Az AD-ben manuálisan létrehozott Storage-fiókot jelölő identitás létrehozása

Ha manuálisan szeretné létrehozni ezt a fiókot, hozzon létre egy új Kerberos-kulcsot a Storage-fiókjához. Ezután használja a Kerberos-kulcsot a fiókjához tartozó jelszóként az alábbi PowerShell-parancsmagokkal. Ezt a kulcsot csak a telepítés során használja a rendszer, és nem használható semmilyen vezérlési vagy adatsík-művelethez a Storage-fiókon. 

```PowerShell
# Create the Kerberos key on the storage account and get the Kerb1 key as the password for the AD identity to represent the storage account
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

New-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -KeyName kerb1
Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -ListKerbKey | where-object{$_.Keyname -contains "kerb1"}
```

Ha ezzel a kulccsal rendelkezik, hozzon létre egy szolgáltatás-vagy számítógépfiókot a szervezeti egység alatt. Használja a következő specifikációt (ne felejtse el lecserélni a példában szereplő szöveget a Storage-fiók nevére):

SPN: "CIFS/a-Storage-Account-Name-here. file. Core. Windows. net" password: Kerberos-kulcs a Storage-fiókhoz.

Ha a szervezeti egysége kikényszeríti a jelszó lejáratát, akkor a jelszó maximális kora előtt frissítenie kell a jelszót, hogy megakadályozza a hitelesítési hibákat az Azure-fájlmegosztás elérésekor. További részletekért lásd: a [Storage-fiók identitásának jelszavának frissítése az ad-ben](storage-files-identity-ad-ds-update-password.md) .

Tartsa meg az újonnan létrehozott identitás biztonsági azonosítóját, szüksége lesz rá a következő lépéshez. A Storage-fiókot képviselő létrehozott identitást nem kell szinkronizálni az Azure AD-vel.

### <a name="enable-the-feature-on-your-storage-account"></a>A szolgáltatás engedélyezése a Storage-fiókban

Most engedélyezheti a szolgáltatást a Storage-fiókjában. Adja meg a tartományi tulajdonságok konfigurációs adatait a következő parancsban, majd futtassa. A következő parancsban a Storage-fiók SID azonosítója az [előző szakaszban](#creating-an-identity-representing-the-storage-account-in-your-ad-manually)a AD DS létrehozott identitás SID-azonosítója.

```PowerShell
# Set the feature flag on the target storage account and provide the required AD domain information
Set-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>" `
        -EnableActiveDirectoryDomainServicesForFile $true `
        -ActiveDirectoryDomainName "<your-domain-name-here>" `
        -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" `
        -ActiveDirectoryForestName "<your-forest-name-here>" `
        -ActiveDirectoryDomainGuid "<your-guid-here>" `
        -ActiveDirectoryDomainsid "<your-domain-sid-here>" `
        -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```

### <a name="debugging"></a>Hibakeresés

A Debug-AzStorageAccountAuth parancsmag futtatásával alapszintű ellenőrzéseket hajthat végre az AD-konfigurációban a bejelentkezett AD-felhasználóval. Az AzFilesHybrid v0.1.2+ verziója támogatja ezt a parancsmagot. Az ebben a parancsmagban végrehajtott ellenőrzésekkel kapcsolatos további információkért tekintse meg a Windows hibaelhárítási útmutatójában, az [ad hitelesítő adatokkal nem csatlakoztatható Azure Files](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) .

```PowerShell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="confirm-the-feature-is-enabled"></a>A szolgáltatás engedélyezésének megerősítése

A következő parancsfájllal ellenőrizheti, hogy a szolgáltatás engedélyezve van-e a Storage-fiókban:

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD DS authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

## <a name="next-steps"></a>Következő lépések

Ezzel a ponttal sikeresen engedélyezte a szolgáltatást a Storage-fiókjában. A szolgáltatás használatához konfigurálnia és módosítania kell a szolgáltatást. Folytassa a következő szakasszal.

[Második rész: megosztási szintű engedélyek kiosztása identitáshoz](storage-files-identity-ad-ds-assign-permissions.md)
