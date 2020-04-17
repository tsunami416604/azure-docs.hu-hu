---
title: Active Directory-hitelesítés engedélyezése SMB-n keresztül az Azure Files számára
description: Megtudhatja, hogy miként engedélyezheti az identitásalapú hitelesítést az SMB-n keresztül az Azure-fájlmegosztások számára az Active Directoryn keresztül. A tartományhoz csatlakozó Windows virtuális gépek (VM-ek) ezután a D hitelesítő adatok használatával hozzáférhetnek az Azure-fájlmegosztásokhoz.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: rogarana
ms.openlocfilehash: 8d1e1262c592f0120b191e18a5c16b97b887a6a2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536530"
---
# <a name="enable-on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Helyszíni Active Directory tartományi szolgáltatások hitelesítésének engedélyezése SMB-n keresztül az Azure-fájlmegosztások számára

[Az Azure Files](storage-files-introduction.md) kétféle tartományi szolgáltatáson keresztül támogatja az identitásalapú hitelesítést a kiszolgálói üzenetblokkon (SMB) keresztül: az Azure Active Directory tartományi szolgáltatások (Azure AD DS) és a helyszíni Active Directory tartományi szolgáltatások (AD DS) (előzetes verzió). Ez a cikk az Active Directory tartományi szolgáltatás Azure-fájlmegosztások hitelesítéséhez az újonnan bevezetett (előzetes verziójú) támogatására összpontosít. Ha engedélyezni szeretné az Azure AD DS (GA) hitelesítését az Azure-fájlmegosztásokhoz, olvassa el [a témáról szóló cikkünket.](storage-files-identity-auth-active-directory-domain-service-enable.md)

> [!NOTE]
> Az Azure fájlmegosztások csak egy tartományi szolgáltatás, vagy az Azure Active Directory tartományi szolgáltatás (Azure AD DS) vagy a helyszíni Active Directory tartományi szolgáltatások (AD DS) hitelesítését támogatja. 
>
> Az Azure-fájlmegosztás-hitelesítéshez használt AD DS-identitásokat szinkronizálni kell az Azure AD-vel. A jelszókivonat-szinkronizálás megadása nem kötelező. 
> 
> A helyszíni AD DS-hitelesítés nem támogatja az AD DS-ben létrehozott számítógépfiókok elleni hitelesítést. 
> 
> A helyszíni AD DS-hitelesítés csak egy Olyan AD-erdőn támogatott, ahol a tárfiók regisztrálva van. Az Azure-fájlmegosztások alapértelmezés szerint csak egyetlen erdőből érhetők el az AD DS hitelesítő adataival. Ha egy másik erdőből szeretné elérni az Azure-fájlmegosztást, győződjön meg arról, hogy a megfelelő erdőszintű megbízhatósági kapcsolat konfigurálva van, olvassa el a [gyakori kérdések](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) című témakört.  
> 
> Az SMB-hozzáférés és az ACL-adatmegőrzés AD DS-hitelesítése az Azure File Sync által kezelt Azure-fájlmegosztások esetében támogatott.
>
> Az Azure Files támogatja a Kerberos-hitelesítést RC4-HMAC titkosítással. Az AES Kerberos titkosítás még nem támogatott.

Ha engedélyezi az AD DS-t az Azure-fájlmegosztásokhoz SMB-n keresztül, az AD DS-hez csatlakoztatott gépek a meglévő AD-hitelesítő adatok használatával csatlakoztathatják az Azure-fájlmegosztásokat. Ez a funkció engedélyezhető egy AD DS-környezetben üzemeltetett vagy a prem gépeken vagy az Azure-ban üzemeltetett.

Az Azure-fájlmegosztások eléréséhez használt identitásokat szinkronizálni kell az Azure AD-vel a megosztási szintű fájlengedélyek kényszerítéséhez a [szerepköralapú hozzáférés-vezérlési (RBAC)](../../role-based-access-control/overview.md) modellen keresztül. A meglévő fájlkiszolgálókról átvitt fájlokon/könyvtárakon lévő [Windows-stílusú DAK-ok](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) megmaradnak és érvénybe lépnek. Ez a funkció zökkenőmentes integrációt biztosít a vállalati AD DS-környezettel. Ahogy lecseréli a helyszíni fájlkiszolgálókat az Azure-fájlmegosztásokra, a meglévő felhasználók egyetlen bejelentkezési felülettel érhetik el az Azure-fájlmegosztásokat a jelenlegi ügyfeleikből, a használatban lévő hitelesítő adatok módosítása nélkül.  

> [!NOTE]
> Az Azure Files AD-hitelesítés beállításának elősegítése érdekében [két videót](https://docs.microsoft.com/azure/storage/files/storage-files-introduction#videos) tettünk közzé lépésről lépésre:
> - Helyszíni fájlkiszolgálók cseréje Azure Files szolgáltatással (beleértve a fájlok privát hivatkozásának beállítását és az AD-hitelesítést)
> - Az Azure Files használata a Windows virtuális asztal profiltárolójaként (beleértve az AD-hitelesítés és az FsLogix-konfiguráció beállítását is)

## <a name="prerequisites"></a>Előfeltételek 

Mielőtt engedélyezne AD DS-hitelesítést az Azure-fájlmegosztásokhoz, győződjön meg arról, hogy teljesítette az alábbi előfeltételeket: 

- Válassza ki vagy hozza létre az AD DS-környezetet, és [szinkronizálja azt az Azure AD-vel.](../../active-directory/hybrid/how-to-connect-install-roadmap.md) 

    A szolgáltatás egy új vagy meglévő helyszíni AD DS-környezetben engedélyezheti. A hozzáféréshez használt identitásokat szinkronizálni kell az Azure AD-vel. Az Azure AD-bérlő és a fájlmegosztás, amely elérése korban, ugyanahhoz az előfizetéshez kell társítható. 

    AD tartományi környezet beállításához olvassa el az [Active Directory tartományi szolgáltatások – áttekintés című témakört.](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) Ha még nem szinkronizálta az AD-t az Azure AD-vel, kövesse az Azure AD Connect és az [Azure AD Connect Health telepítési ütemtervútmutatását](../../active-directory/hybrid/how-to-connect-install-roadmap.md) az Azure AD Connect konfigurálásához és beállításához. 

- Tartományhoz csatlakozhat egy helyszíni géphez vagy egy Azure-virtuális géphez a helyszíni AD DS-hez. 

    Ha egy fájlmegosztást egy számítógépről vagy virtuális gépről származó AD-hitelesítő adatokkal szeretne elérni, az eszközt tartományhoz kell csatlakoztatni az AD DS-hez. A tartományhoz való csatlakozásról a [Csatlakozás tartományhoz](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)című dokumentumban talál további információt. 

- Válasszon vagy hozzon létre egy Azure-tárfiókot [egy támogatott régióban.](#regional-availability) 

    Győződjön meg arról, hogy a fájlmegosztásokat tartalmazó tárfiók még nincs konfigurálva az Azure AD DS-hitelesítéshez. Ha az Azure Files Azure AD DS-hitelesítés engedélyezve van a tárfiókban, le kell tiltani, mielőtt a helyszíni AD DS használatára váltana. Ez azt jelenti, hogy az Azure AD DS-környezetben konfigurált meglévő Hozzáférés-hozzáférés-hozzáférés-engedélyeket újra kell konfigurálni a megfelelő engedély-kényszerítéshez.
    
    Az új fájlmegosztás létrehozásáról a Fájlmegosztás létrehozása az Azure Files alkalmazásban című [témakörben talál.](storage-how-to-create-file-share.md)
    
    Az optimális teljesítmény érdekében azt javasoljuk, hogy a tárfiókot ugyanabban a régióban telepítse, mint a virtuális gép, amelyből a megosztás elérését tervezi. 

- Ellenőrizze a kapcsolatot az Azure-fájlmegosztások csatlakoztatásával a tárfiók kulcsával. 

    Annak ellenőrzéséhez, hogy az eszköz és a fájlmegosztás megfelelően van-e konfigurálva, próbálja meg a fájlmegosztást a tárfiók kulcsával felállítani. További információt az [Azure-fájlmegosztás használata a Windows rendszerrel](storage-how-to-use-files-windows.md)című témakörben talál.

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

Az Azure Files hitelesítése az AD DS szolgáltatással (előzetes verzió) a [legtöbb nyilvános régióban](https://azure.microsoft.com/global-infrastructure/regions/)elérhető.

Az Azure Files helyszíni AD DS szolgáltatással való hitelesítése nem érhető el:
- USA nyugati régiója


## <a name="workflow-overview"></a>Munkafolyamat – áttekintés

Mielőtt engedélyezne AD DS-hitelesítést az SMB-n keresztül az Azure-fájlmegosztások számára, azt javasoljuk, hogy olvassa el és töltse ki az [előfeltétel szakaszt.](#prerequisites) Az előfeltételek ellenőrzik, hogy az AD, az Azure AD és az Azure Storage-környezetek megfelelően vannak konfigurálva. 

Ha azt tervezi, hogy engedélyezi a hálózati konfigurációkat a fájlmegosztáson, javasoljuk, hogy az AD DS-hitelesítés engedélyezése előtt értékelje ki a [hálózati szempontot,](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) és végezze el a kapcsolódó konfigurációt.

Ezután kövesse az alábbi lépéseket az Azure Files for AD-hitelesítés beállításához: 

1. Engedélyezze az Azure Files AD DS-hitelesítést a tárfiókon. 

1. Hozzáférési engedélyek hozzárendelése egy megosztáshoz az Azure AD-identitás (egy felhasználó, csoport vagy egyszerű szolgáltatás), amely összhangban van a cél AD-identitás. 

1. Konfigurálja az ACL-eket az SMB-n keresztül könyvtárakhoz és fájlokhoz. 
 
1. Csatlakoztasson egy Azure-fájlmegosztást az AD DS-hez csatlakoztatott virtuális gépre. 

1. Frissítse a tárfiók identitásának jelszavát az AD DS-ben.

Az alábbi ábra bemutatja a teljes körű munkafolyamat ot az Azure AD-hitelesítés smb-n keresztül i.Azure fájlmegosztások engedélyezéséhez. 

![Fájlok AD munkafolyamat-diagramja](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> Az AD DS-hitelesítés Az Azure-fájlmegosztások SMB-n keresztüli hitelesítése csak a Windows 7 vagy Windows Server 2008 R2 operációs rendszernél újabb operációs rendszeren futó gépeken vagy virtuális gépeken támogatott. 

## <a name="1-enable-ad-authentication-for-your-account"></a>1. AD-hitelesítés engedélyezése a fiókjához 

Az AD DS-hitelesítés engedélyezéséhez Az Azure-fájlmegosztások SMB-n keresztül, először regisztrálnia kell a tárfiókot az AD DS-ben, majd be kell állítania a szükséges tartományi tulajdonságokat a tárfiókban. Ha a szolgáltatás engedélyezve van a tárfiókban, akkor a fiók összes új és meglévő fájlmegosztására vonatkozik. A `join-AzStorageAccountForAuth` szolgáltatás engedélyezéséhez használható. A teljes körű munkafolyamat részletes leírását ebben a szakaszban található parancsfájlban találja. 

> [!IMPORTANT]
> A `Join-AzStorageAccountForAuth` parancsmag módosítja az AD-környezetet. Olvassa el az alábbi magyarázatot, hogy jobban megértse, mit tesz annak érdekében, hogy megfelelő engedélyekkel rendelkezzen a parancs végrehajtásához, és hogy az alkalmazott módosítások igazodjanak a megfelelőségi és biztonsági házirendekhez. 

A `Join-AzStorageAccountForAuth` parancsmag a megadott tárfiók nevében egy kapcsolat nélküli tartományhoz való csatlakozással egyenértékű műveletet hajt végre. A parancsfájl a parancsmag segítségével hoz létre fiókot az AD-tartományban, vagy egy [számítógépfiókot](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (alapértelmezett) vagy egy [szolgáltatás bejelentkezési fiókját.](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) Ha ezt manuálisan választja, válassza ki a környezetének leginkább megfelelő fiókot.

A parancsmag által létrehozott AD DS-fiók az AD tartomány tárfiókját jelöli. Ha az AD DS-fiók egy olyan szervezeti egység (OU) alatt jön létre, amely a jelszó lejáratát kényszeríti, a jelszó maximális életkora előtt frissítenie kell a jelszót. Ha nem frissíti a fiók jelszavát, az Azure-fájlmegosztások elérésekor hitelesítési hibákat eredményez. A jelszó frissítésének módjáról az [AD DS-fiók jelszavának frissítése](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)című témakörben olvashat.

A következő parancsfájl segítségével hajthatja végre a regisztrációt, és engedélyezheti a szolgáltatást, vagy manuálisan is végrehajthatja a parancsfájl által végrehajtandó műveleteket. Ezeket a műveleteket a parancsfájlt követő szakasz ismerteti. Nem kell mindkettőt csinálnod.

### <a name="11-script-prerequisites"></a>1.1 Parancsfájlok előfeltételei
- [Töltse le és csomagolja ki az AzFilesHybrid modult](https://github.com/Azure-Samples/azure-files-samples/releases)
- Telepítse és hajtsa végre a modult egy olyan eszközben, amely tartományhoz csatlakozik a helyszíni AD DS-hez olyan AD DS hitelesítő adatokkal, amelyek rendelkeznek egy szolgáltatás bejelentkezési fiók vagy egy számítógépfiók létrehozásához szükséges engedélyekkel a cél AD-ben.
-  Futtassa a parancsfájlt egy helyszíni AD DS-hitelesítő adatokhasználatával, amely szinkronizálva van az Azure AD-vel. A helyszíni AD DS hitelesítő adatoknak rendelkezniük kell a tárfiók tulajdonosával vagy a közreműködő RBAC szerepkör engedélyeivel.
- Győződjön meg arról, hogy a tárfiók [támogatott régióban](#regional-availability)van.

### <a name="12-domain-join-your-storage-account"></a>1.2 Tartomány csatlakozás tárfiókjához
Ne felejtse el lecserélni a helyőrző értékeket a saját az alábbi paramétereket, mielőtt végrehajtaná azt a PowerShellben.
> [!IMPORTANT]
> A tartományillesztési parancsmag létrehoz egy AD-fiókot, amely az AD tárfiókját (fájlmegosztását) jelöli. Dönthet úgy, hogy számítógépfiókként vagy szolgáltatásbejelentkezési fiókként regisztrál, a gyakori kérdések et a részletekért olvassa [el.](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) A számítógépfiókok esetében az AD-ben 30 napig beállított alapértelmezett jelszólejárati életkor van beállítva. Hasonlóképpen előfordulhat, hogy a szolgáltatás bejelentkezési fiókjában az AD-tartományban vagy szervezeti egységben beállított alapértelmezett jelszólejárati korvan beállítva.
> Mindkét fióktípus esetében javasoljuk, hogy ellenőrizze, hogy mi a jelszó lejárati kora az AD-környezetben konfigurálva, és tervezze [meg a tárfiók identitásának jelszavának frissítését az Alábbi AD-fiók AD-fiókjában](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) a maximális jelszókor előtt. Az AD-fiók jelszavának frissítése hitelesítési hibákat eredményez az Azure-fájlmegosztások elérésekor. Fontolóra [veheti, hogy új AD szervezeti egységet hoz létre az AD-ben,](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) és ennek megfelelően letiltja a jelszó elévülési házirendet a [számítógépfiókokban](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN) vagy a szolgáltatásbejelentkezési fiókokban. 

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
#You can run Get-Help Join-AzStorageAccountForAuth to find more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -Name $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" ` #Default set to "ComputerAccount"
        -OrganizationalUnitName "<ou-name-here>" #You can also use -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" instead. If you don't provide the OU name as an input parameter, the AD identity that represents the storage account will be created under the root directory.

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, go to Azure Files FAQ.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose

```

A következő leírás összefoglalja a `Join-AzStorageAccountForAuth` parancsmag végrehajtásakor végrehajtott összes műveletet. Ezeket a lépéseket manuálisan is végrehajthatja, ha nem szeretné használni a parancsot:

> [!NOTE]
> Ha már sikeresen `Join-AzStorageAccountForAuth` végrehajtotta a fenti parancsfájlt, lépjen a következő szakaszba: "1.3 A funkció engedélyezésének ellenőrzése". Nem kell újra végrehajtania az alábbi műveleteket.

#### <a name="a-checking-environment"></a>a. Környezet ellenőrzése

Először is, a szkript ellenőrzi a környezetet. Pontosabban ellenőrzi, hogy az [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) telepítve van-e, és hogy a rendszerhéj rendszergazdai jogosultságokkal történik-e. Ezután ellenőrzi, hogy az [Az.Storage 1.11.1-preview modul](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) telepítve van-e, és telepíti, ha nem. Ha ezek az ellenőrzések megfelelnek, akkor ellenőrzi az AD DS-t, hogy van-e olyan [számítógépfiók](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (alapértelmezett) vagy [szolgáltatásbejelentkezési fiók,](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) amelyet az SPN/UPN-nel már létrehoztak "cifs/your-storage-account-name-here.core.windows.net" néven. Ha a fiók nem létezik, létrehoz egyet az alábbi b szakaszban leírtak szerint.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Az AD-ben lévő tárfiókot képviselő identitás manuális létrehozása

A fiók manuális létrehozásához hozzon létre egy új `New-AzStorageAccountKey -KeyName kerb1`Kerberos-kulcsot a tárfiókjához a használatával. Ezután használja ezt a Kerberos-kulcsot a fiókjához használt jelszóként. Ez a kulcs csak a beállítás során használatos, és nem használható semmilyen vezérlő- vagy adatsík-művelethez a tárfiókkal szemben.

Miután rendelkezik ezzel a kulccsal, hozzon létre egy szolgáltatást vagy számítógépfiókot a szervezeti egység alatt. Használja a következő specifikációt: SPN: "cifs/your-storage-account-name-here.file.core.windows.net" Jelszó: Kerberos kulcs a tárfiókhoz.

Ha a szervezeti egység kényszeríti a jelszó lejáratát, frissítenie kell a jelszót a maximális jelszókor előtt, hogy megakadályozza a hitelesítési hibákat az Azure-fájlmegosztások elérésekor. A részleteket [a tárfiók identitásának frissítése az AD DS-ben](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) című témakörben találja.

Tartsa meg az újonnan létrehozott fiók biztonsági azonosítóját, szüksége lesz rá a következő lépéshez. A tárfiókot képviselő létrehozott identitást nem kell szinkronizálni az Azure AD-vel.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. A funkció engedélyezése a tárfiókban

A parancsfájl ezután engedélyezi a funkciót a tárfiókban. A telepítés manuális végrehajtásához adja meg a következő parancsban a tartomány tulajdonságainak konfigurációs adatait, majd futtassa azt. A következő parancsban szükséges tárfiók-biztonsági azonosító az [előző szakaszban az](#b-creating-an-identity-representing-the-storage-account-in-your-ad-manually)AD DS-ben létrehozott identitás biztonsági azonosítója.

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


### <a name="13-confirm-that-the-feature-is-enabled"></a>1.3 Annak ellenőrzése, hogy a funkció engedélyezve van-e

Ellenőrizheti, hogy a szolgáltatás engedélyezve van-e a tárfiókban, a következő parancsfájlt használhatja:

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

Most már sikeresen engedélyezte a funkciót a tárfiókban. Most, hogy a szolgáltatás engedélyezve van, további lépéseket kell végrehajtania a szolgáltatás használatához.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Most már sikeresen engedélyezte az AD DS-hitelesítést az SMB-n keresztül, és olyan egyéni szerepkört rendelt, amely hozzáférést biztosít egy AD DS-identitással rendelkező Azure-fájlmegosztáshoz. Ha további felhasználóknak szeretne hozzáférést adni a fájlmegosztáshoz, kövesse a [Hozzáférési engedélyek hozzárendelése](#2-assign-access-permissions-to-an-identity) az identitás használatához és az [NTFS-engedélyek konfigurálásához az SMB szakaszokban című](#3-configure-ntfs-permissions-over-smb) útmutató utasításait.

## <a name="5-update-the-password-of-your-storage-account-identity-in-ad-ds"></a>5. Frissítse a tárfiók identitásának jelszavát az AD DS-ben

Ha regisztrálta az AD DS-identitást/-fiókot, amely a tárfiókot egy olyan szervezeti egység alatt képviseli, amely a jelszó lejárati idejét kényszeríti ki, a jelszó maximális élettartama előtt el kell forgatnia a jelszót. Ha nem frissíti az AD DS-fiók jelszavát, az Azure-fájlmegosztások elérésének hitelesítési hibáit eredményezi.  

A jelszó elforgatásának elindításához futtathatja a `Update-AzStorageAccountADObjectPassword` parancsot az AzFilesHybrid modulból. A parancsmag a tárfiók kulcsának elforgatásához hasonló műveleteket hajt végre. Leveszi a tárfiók második Kerberos-kulcsát, és arra használja, hogy frissítse a regisztrált fiók jelszavát az AD DS-ben. Ezután újragenerálja a tárfiók cél Kerberos kulcsát, és frissíti a regisztrált fiók jelszavát az AD DS-ben. Ezt a parancsmamot helyszíni AD DS-tartományhoz csatlakozott környezetben kell futtatnia.

```PowerShell
# Update the password of the AD DS account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>További lépések

Az Azure Files szolgáltatásról és az AD SMB-n keresztüli használatáról az alábbi forrásokban talál további információt:

- [Az Azure Files identitásalapú hitelesítési támogatásának áttekintése az SMB-hozzáféréshez](storage-files-active-directory-overview.md)
- [GYIK](storage-files-faq.md)
