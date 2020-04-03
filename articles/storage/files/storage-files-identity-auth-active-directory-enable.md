---
title: Active Directory-hitelesítés engedélyezése SMB-n keresztül az Azure Files számára
description: Megtudhatja, hogy miként engedélyezheti az identitásalapú hitelesítést az SMB-n keresztül az Azure-fájlmegosztások számára az Active Directoryn keresztül. A tartományhoz csatlakozó Windows virtuális gépek (VM-ek) ezután a D hitelesítő adatok használatával hozzáférhetnek az Azure-fájlmegosztásokhoz.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: rogarana
ms.openlocfilehash: 0bf8960f1e97de45d5369f69c698311d0b4e3dbb
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584517"
---
# <a name="enable-active-directory-authentication-over-smb-for-azure-file-shares"></a>Active Directory-hitelesítés engedélyezése SMB-n keresztül az Azure-fájlmegosztások számára

[Az Azure Files](storage-files-introduction.md) kétféle tartományi szolgáltatáson keresztül támogatja az identitásalapú hitelesítést a kiszolgálói üzenetblokkon (SMB) keresztül: az Azure Active Directory tartományi szolgáltatások (Azure AD DS) (GA) és az Active Directory (AD) (előzetes verzió). Ez a cikk az Active Directory tartományi szolgáltatás Azure-fájlmegosztások hitelesítéséhez az újonnan bevezetett (előzetes verziójú) támogatására összpontosít. Ha engedélyezni szeretné az Azure AD DS (GA) hitelesítését az Azure-fájlmegosztásokhoz, olvassa el [a témáról szóló cikkünket.](storage-files-identity-auth-active-directory-domain-service-enable.md)

> [!NOTE]
> Az Azure fájlmegosztások csak egy tartományi szolgáltatás, vagy az Azure Active Directory tartományi szolgáltatás (Azure AD DS) vagy az Active Directory (AD) hitelesítését támogatja. 
>
> Az Azure-fájlmegosztás-hitelesítéshez használt AD-identitásokat szinkronizálni kell az Azure AD-vel. A jelszókivonat-szinkronizálás megadása nem kötelező. 
> 
> Az AD-hitelesítés nem támogatja az AD-ben létrehozott számítógépfiókok elleni hitelesítést. 
> 
> AD-hitelesítés csak egy Olyan AD-erdőn támogatott, ahol a tárfiók regisztrálva van. Az Azure-fájlmegosztások alapértelmezés szerint csak egyetlen AD-erdőből érhetők el az AD-hitelesítő adatokkal. Ha egy másik erdőből szeretné elérni az Azure-fájlmegosztást, győződjön meg arról, hogy a megfelelő erdőszintű megbízhatósági kapcsolat konfigurálva van, olvassa el a [gyakori kérdések](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) című témakört.  
> 
> Az SMB-hozzáférés és az ACL-adatmegőrzés AD-hitelesítése az Azure File Sync által kezelt Azure-fájlmegosztások esetében támogatott.
>
> Az Azure Files támogatja a Kerberos-hitelesítést RC4-HMAC titkosítással. Az AES Kerberos titkosítás még nem támogatott.

Ha engedélyezi az AD-t az Azure-fájlmegosztásokhoz SMB-n keresztül, az AD-tartományhoz csatlakoztatott gépek a meglévő AD-hitelesítő adatok használatával csatlakoztathatják az Azure-fájlmegosztásokat. Ez a funkció engedélyezhető egy AD-környezetben üzemeltetett vagy a prem gépeken vagy az Azure-ban üzemeltetett.

Az Azure-fájlmegosztások eléréséhez használt AD-identitásokat szinkronizálni kell az Azure AD-vel a megosztási szintű fájlengedélyek kényszerítéséhez a szabványos [szerepköralapú hozzáférés-vezérlési (RBAC)](../../role-based-access-control/overview.md) modellen keresztül. A meglévő fájlkiszolgálókról átvitt fájlokon/könyvtárakon lévő [Windows-stílusú DAK-ok](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) megmaradnak és érvénybe lépnek. Ez a funkció zökkenőmentes integrációt biztosít a vállalati AD tartományinfrastruktúrával. Ahogy lecseréli a helyszíni fájlkiszolgálókat az Azure-fájlmegosztásokra, a meglévő felhasználók egyetlen bejelentkezési felülettel érhetik el az Azure-fájlmegosztásokat a jelenlegi ügyfeleikből, a használatban lévő hitelesítő adatok módosítása nélkül.  
 
## <a name="prerequisites"></a>Előfeltételek 

Mielőtt engedélyezne AD-hitelesítést az Azure-fájlmegosztásokhoz, győződjön meg arról, hogy teljesítette az alábbi előfeltételeket: 

- Válassza ki vagy hozza létre az AD-környezetet, és szinkronizálja azt az Azure AD-vel. 

    A funkciót új vagy meglévő AD-környezetben engedélyezheti. A hozzáféréshez használt identitásokat szinkronizálni kell az Azure AD-vel. Az Azure AD-bérlő és a fájlmegosztás, amely elérése korban, ugyanahhoz az előfizetéshez kell társítható. 

    AD tartományi környezet beállításához olvassa el az [Active Directory tartományi szolgáltatások – áttekintés című témakört.](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) Ha még nem szinkronizálta az AD-t az Azure AD-vel, kövesse a Mi a [hibrid identitás az Azure Active Directoryval című](../../active-directory/hybrid/whatis-hybrid-identity.md) útmutatóban található útmutatást az előnyben részesített hitelesítési módszer és az Azure AD Connect beállítási beállítási lehetőség meghatározásához. 

- Tartományhoz való csatlakozás hoz egy helyszíni gép vagy egy Azure virtuális gép aD (más néven AD DS). 

    Ahhoz, hogy egy fájlmegosztást egy számítógépről vagy virtuális gépről származó AD-hitelesítő adatok használatával férjen hozzá, az eszköznek tartományhoz kell csatlakoznia az AD-hez. Az AD tartományhoz való csatlakozásáról a [Számítógép csatlakoztatása tartományhoz](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)című dokumentum című dokumentumban talál további információt. 

- Válasszon vagy hozzon létre egy Azure-tárfiókot [egy támogatott régióban.](#regional-availability) 

    Győződjön meg arról, hogy a fájlmegosztásokat tartalmazó tárfiók még nincs konfigurálva az Azure AD DS-hitelesítéshez. Ha az Azure Files Azure AD DS-hitelesítés engedélyezve van a tárfiókban, le kell tiltani, mielőtt az AD-t használná. Ez azt jelenti, hogy az Azure AD DS-környezetben konfigurált meglévő Hozzáférés-hozzáférés-hozzáférés-engedélyeket újra kell konfigurálni a megfelelő engedély-kényszerítéshez.
    
    Az új fájlmegosztás létrehozásáról a Fájlmegosztás létrehozása az Azure Files alkalmazásban című [témakörben talál.](storage-how-to-create-file-share.md)
    
    Az optimális teljesítmény érdekében azt javasoljuk, hogy a tárfiókot ugyanabban a régióban telepítse, mint a virtuális gép, amelyből a megosztás elérését tervezi. 

- Ellenőrizze a kapcsolatot az Azure-fájlmegosztások csatlakoztatásával a tárfiók kulcsával. 

    Annak ellenőrzéséhez, hogy az eszköz és a fájlmegosztás megfelelően van-e konfigurálva, próbálja meg a fájlmegosztást a tárfiók kulcsával felállítani. További információt az [Azure-fájlmegosztás használata a Windows rendszerrel](storage-how-to-use-files-windows.md)című témakörben talál.

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

Az Azure Files AD-hitelesítés (előzetes verzió) a [nyilvános felhő minden régiójában](https://azure.microsoft.com/global-infrastructure/regions/)elérhető.

## <a name="workflow-overview"></a>Munkafolyamat – áttekintés

Mielőtt engedélyezne AD-hitelesítést Az Azure-fájlmegosztások SMB-n keresztül, azt javasoljuk, hogy kövesse végig az [előfeltételeket,](#prerequisites) és győződjön meg arról, hogy elvégezte az összes lépést. Az előfeltételek ellenőrzik, hogy az AD, az Azure AD és az Azure Storage-környezetek megfelelően vannak konfigurálva. 

Ezután adjon hozzáférést az Azure Files-erőforrásokhoz AD-hitelesítő adatokkal: 

- Engedélyezze az Azure Files AD-hitelesítést a tárfiókon.  

- Hozzáférési engedélyek hozzárendelése egy megosztáshoz az Azure AD-identitás (egy felhasználó, csoport vagy egyszerű szolgáltatás), amely összhangban van a cél AD-identitás. 

- Konfigurálja az ACL-eket az SMB-n keresztül könyvtárakhoz és fájlokhoz. 

- Azure-fájlmegosztás csatlakoztatása egy AD-tartományhoz csatlakoztatott virtuális gépről. 

Az alábbi ábra bemutatja a teljes körű munkafolyamat ot az Azure AD-hitelesítés smb-n keresztül i.Azure fájlmegosztások engedélyezéséhez. 

![Fájlok AD munkafolyamat-diagramja](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> Az SMB-n keresztüli AD-hitelesítés az Azure fájlmegosztásokhoz csak a Windows 7 vagy Windows Server 2008 R2 operációs rendszernél újabb operációs rendszeren futó gépeken vagy virtuális gépeken támogatott. 

## <a name="enable-ad-authentication-for-your-account"></a>AD-hitelesítés engedélyezése a fiókjához 

Az AD-hitelesítés engedélyezéséhez SMB-n keresztül az Azure-fájlmegosztások, először regisztrálnia kell a tárfiókot az AD-vel, majd állítsa be a szükséges tartományi tulajdonságokat a tárfiókban. Ha a szolgáltatás engedélyezve van a tárfiókban, akkor a fiók összes új és meglévő fájlmegosztására vonatkozik. A `join-AzStorageAccountForAuth` szolgáltatás engedélyezéséhez használható. A teljes körű munkafolyamat részletes leírását az alábbi szakaszban találja. 

> [!IMPORTANT]
> A `Join-AzStorageAccountForAuth` parancsmag módosítja az AD-környezetet. Olvassa el az alábbi magyarázatot, hogy jobban megértse, mit tesz annak érdekében, hogy megfelelő engedélyekkel rendelkezzen a parancs végrehajtásához, és hogy az alkalmazott módosítások igazodjanak a megfelelőségi és biztonsági házirendekhez. 

A `Join-AzStorageAccountForAuth` parancsmag a megadott tárfiók nevében egy kapcsolat nélküli tartományhoz való csatlakozással egyenértékű műveletet hajt végre. Létrehoz egy fiókot az AD tartományban, akár [egy számítógépfiókot,](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) akár egy [szolgáltatás bejelentkezési fiókját.](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) A létrehozott AD-fiók az AD tartománytárfiókját jelöli. Ha az AD-fiók egy jelszó lejáratát kikényszerítő AD szervezeti egység (OU) alatt jön létre, a jelszó maximális életkora előtt frissítenie kell a jelszót. Az AD-fiók jelszavának frissítése hitelesítési hibákat eredményez az Azure-fájlmegosztások elérésekor. A jelszó frissítésének módjáról az [AD-fiók jelszavának frissítése](#update-ad-account-password)című témakörben olvashat.

A következő parancsfájl segítségével hajthatja végre a regisztrációt, és engedélyezheti a szolgáltatást, vagy manuálisan is végrehajthatja a parancsfájl által végrehajtandó műveleteket. Ezeket a műveleteket a parancsfájlt követő szakasz ismerteti. Nem kell mindkettőt csinálnod.

### <a name="1-check-prerequisites"></a>1. Ellenőrizze az előfeltételeket
- [Töltse le és csomagolja ki az AzFilesHybrid modult](https://github.com/Azure-Samples/azure-files-samples/releases)
- Telepítse és hajtsa végre a modult egy olyan eszközben, amely tartománycsatlakozik az AD-hez olyan AD hitelesítő adatokkal, amelyek rendelkeznek egy szolgáltatás bejelentkezési fiók vagy egy számítógépfiók létrehozásához szükséges engedéllyel a cél AD-ben.
-  Futtassa a parancsfájlt az Azure AD-vel szinkronizált AD-hitelesítő adatok használatával. Az AD hitelesítő adatoknak rendelkezniük kell a tárfiók tulajdonosával vagy a közreműködő RBAC szerepkör engedélyeivel.
- Győződjön meg arról, hogy a tárfiók [támogatott régióban](#regional-availability)van.

### <a name="2-domain-join-your-storage-account"></a>2. Tartomány csatlakozás a tárfiókhoz
Ne felejtse el lecserélni a helyőrző értékeket a saját az alábbi paramétereket, mielőtt végrehajtaná azt a PowerShellben.

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId "<your-subscription-id-here>"

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account, depends on the AD permission you have and preference. 
Join-AzStorageAccountForAuth `
        -ResourceGroupName "<resource-group-name-here>" `
        -Name "<storage-account-name-here>" `
        -DomainAccountType "ComputerAccount" `
        -OrganizationalUnitName "<ou-name-here>"
```

A következő leírás összefoglalja a `Join-AzStorageAccountForAuth` parancsmag végrehajtásakor végrehajtott összes műveletet. Ezeket a lépéseket manuálisan is végrehajthatja, ha nem szeretné használni a parancsot:

> [!NOTE]
> Ha már sikeresen `Join-AzStorageAccountForAuth` végrehajtotta a fenti parancsfájlt, lépjen a következő "3. Ellenőrizze, hogy a funkció engedélyezve van-e". Nem kell újra végrehajtania az alábbi műveleteket.

#### <a name="a-checking-environment"></a>a. Környezet ellenőrzése

Először is, ez ellenőrzi a környezetet. Pontosabban ellenőrzi, hogy az [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) telepítve van-e, és hogy a rendszerhéj rendszergazdai jogosultságokkal van-e végrehajtva. Ezután ellenőrzi, hogy az [Az.Storage 1.11.1-preview modul](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) telepítve van-e, és telepíti, ha nem. Ha ezek az ellenőrzések átmennek, akkor ellenőrzi az AD-t, hogy van-e olyan [számítógépfiók](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (alapértelmezett) vagy [szolgáltatásbejelentkezési fiók,](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) amelyet az SPN/UPN-nel már létrehoztak "cifs/your-storage-account-name-here.file.core.windows.net" néven. Ha a fiók nem létezik, létrehoz egyet az alábbi b szakaszban leírtak szerint.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Az AD-ben lévő tárfiókot képviselő identitás manuális létrehozása

A fiók manuális létrehozásához hozzon létre egy új `New-AzStorageAccountKey -KeyName kerb1`kerberos kulcsot a tárfiókhoz a használatával. Ezután használja ezt a kerberos kulcsot a fiókjához. Ez a kulcs csak a beállítás során használatos, és nem használható semmilyen vezérlő- vagy adatsík-művelethez a tárfiókkal szemben.

Miután rendelkezik ezzel a kulccsal, hozzon létre egy szolgáltatást vagy számítógépfiókot a szervezeti egység alatt. Használja a következő specifikációt: SPN: "cifs/your-storage-account-name-here.file.core.windows.net" Jelszó: Kerberos kulcs a tárfiókhoz.

Ha a szervezeti egység kényszeríti a jelszó lejáratát, frissítenie kell a jelszót a maximális jelszókor előtt, hogy megakadályozza a hitelesítési hibákat az Azure-fájlmegosztások elérésekor. A részleteket az [AD-fiók jelszavának frissítése](#update-ad-account-password) című témakörben találja.

Tartsa meg az újonnan létrehozott fiók biztonsági azonosítóját, szüksége lesz rá a következő lépéshez. Az imént létrehozott AD-identitás, amely a tárfiókot nem kell szinkronizálni az Azure AD-vel.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. A funkció engedélyezése a tárfiókban

A parancsfájl ezután engedélyezi a funkciót a tárfiókban. A telepítés manuális végrehajtásához adja meg a következő parancsban a tartomány tulajdonságainak konfigurációs adatait, majd futtassa azt. A következő parancsban szükséges tárfiók-biztonsági azonosító az AD-ben létrehozott identitás sid-je (a fenti b szakasz).

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


### <a name="3-confirm-that-the-feature-is-enabled"></a>3. Annak ellenőrzése, hogy a funkció engedélyezve van-e

Ellenőrizheti, hogy a szolgáltatás engedélyezve van-e a tárfiókban, a következő parancsfájlt használhatja:

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Most már sikeresen engedélyezte a funkciót a tárfiókban. Annak ellenére, hogy a funkció engedélyezve van, további műveleteket kell végrehajtania a funkció megfelelő használatához.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Most már sikeresen engedélyezte az AD-hitelesítést az SMB-n keresztül, és olyan egyéni szerepkört rendelt, amely hozzáférést biztosít egy AD-identitással rendelkező Azure-fájlmegosztáshoz. Ha további felhasználóknak szeretne hozzáférést adni a fájlmegosztáshoz, kövesse a [Hozzáférési engedélyek hozzárendelése](#assign-access-permissions-to-an-identity) az identitás használatához és az [NTFS-engedélyek konfigurálásához az SMB szakaszokban című](#configure-ntfs-permissions-over-smb) útmutató utasításait.

## <a name="update-ad-account-password"></a>Az AD-fiók jelszavának frissítése

Ha regisztrálta a tárfiókot képviselő AD-identitást/fiókot egy olyan szervezeti egység alatt, amely a jelszó lejárati idejét kényszeríti, a jelszó maximális élettartama előtt el kell forgatnia a jelszót. Ha nem frissíti az AD-fiók jelszavát, az Azure-fájlmegosztások elérésének hitelesítési hibáit eredményezi.  

A jelszó elforgatásának elindításához futtathatja a `Update-AzStorageAccountADObjectPassword` parancsot az AzFilesHybrid modulból. A parancsmag a tárfiók kulcsának elforgatásához hasonló műveleteket hajt végre. Leveszi a tárfiók második Kerberos-kulcsát, és a regisztrált fiók jelszavának frissítésére használja az AD-ben. Ezután újragenerálja a tárfiók cél Kerberos kulcsát, és frissíti a regisztrált fiók jelszavát az AD-ben. Ezt a parancsmamot AD-tartományhoz csatlakozott környezetben kell futtatnia.

```PowerShell
#Update the password of the AD account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>További lépések

Az Azure Files szolgáltatásról és az AD SMB-n keresztüli használatáról az alábbi forrásokban talál további információt:

- [Az Azure Files identitásalapú hitelesítési támogatásának áttekintése az SMB-hozzáféréshez](storage-files-active-directory-overview.md)
- [GYIK](storage-files-faq.md)
