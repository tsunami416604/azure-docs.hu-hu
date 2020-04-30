---
title: Az SMB protokollon keresztüli Active Directory hitelesítés engedélyezése Azure Files
description: Megtudhatja, hogyan engedélyezheti az Azure-fájlmegosztás SMB-n keresztüli identitás-alapú hitelesítését Active Directoryon keresztül. A tartományhoz csatlakoztatott Windows-alapú virtuális gépek (VM-EK) az AD hitelesítő adatok használatával érhetik el az Azure-fájlmegosztást.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: rogarana
ms.openlocfilehash: b2dd501344e1ea799db58ea749395aaed05d05f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82106550"
---
# <a name="enable-on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Helyszíni Active Directory tartományi szolgáltatások hitelesítés engedélyezése SMB-en keresztül az Azure-fájlmegosztás esetében

[Azure Files](storage-files-introduction.md) támogatja az identitás-alapú hitelesítést a Server Message Block (SMB) protokollon keresztül a következő két típusú tartományi szolgáltatáson keresztül: Azure Active Directory Domain Services (Azure AD DS) és a helyszíni Active Directory tartományi szolgáltatások (AD DS) (előzetes verzió). Ez a cikk az Azure-fájlmegosztás hitelesítésének Active Directory-tartomány szolgáltatásának az újonnan bevezetett (előzetes verzió) támogatását ismerteti. Ha szeretné engedélyezni az Azure AD DS (GA) hitelesítését az Azure-fájlmegosztás számára, tekintse [meg a tárgyat ismertető cikket](storage-files-identity-auth-active-directory-domain-service-enable.md).

> [!NOTE]
> Az Azure-fájlmegosztás csak az egyik tartományi szolgáltatás, Azure Active Directory tartományi szolgáltatás (Azure AD DS) vagy a helyszíni Active Directory tartományi szolgáltatások (AD DS) hitelesítését támogatja. 
>
> AD DS az Azure-fájlmegosztás hitelesítéséhez használt identitásokat szinkronizálni kell az Azure AD-vel. A jelszó-kivonat szinkronizálása nem kötelező. 
> 
> A helyszíni AD DS hitelesítés nem támogatja a AD DSban létrehozott számítógépfiókok hitelesítését. 
> 
> A helyszíni AD DS hitelesítés csak egy AD-erdőben támogatott, ahol a Storage-fiók regisztrálva van. Alapértelmezés szerint csak egyetlen erdő AD DS hitelesítő adataival érheti el az Azure-fájlmegosztást. Ha egy másik erdőből kell hozzáférnie az Azure-fájlmegosztás számára, győződjön meg arról, hogy a megfelelő erdőszintű megbízhatósági kapcsolat van konfigurálva. a részletekért tekintse meg a [Gyakori kérdések](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) című részt.  
> 
> AD DS hitelesítés az SMB-hozzáféréshez és az ACL-megőrzéshez Azure File Sync által felügyelt Azure-fájlmegosztás esetén támogatott.
>
> Azure Files támogatja a Kerberos-hitelesítést az AD-vel az RC4-HMAC titkosítással. Az AES Kerberos-titkosítás még nem támogatott.

Ha engedélyezi AD DS az Azure-fájlmegosztás SMB protokollon keresztül történő engedélyezését, a AD DS csatlakoztatott gépek az Azure-fájlmegosztást meglévő AD-hitelesítő adataival csatlakoztathatók. Ez a funkció AD DS környezettel engedélyezhető, amely a helyszíni gépeken vagy az Azure-ban üzemeltethető.

Az Azure-fájlmegosztás eléréséhez használt identitásokat szinkronizálni kell az Azure AD-vel, hogy a [szerepköralapú hozzáférés-vezérlési (RBAC)](../../role-based-access-control/overview.md) modell használatával kényszerítse a megosztási szintű fájlok engedélyeit. A meglévő fájlkiszolgálók által átvitt fájlok/könyvtárak [Windows-stílusú DACL-listái](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) megmaradnak és érvényben lesznek. Ez a szolgáltatás zökkenőmentes integrációt biztosít a vállalati AD DS környezettel. Amikor az Azure-fájlmegosztás használatával cseréli le a helyszíni fájlkiszolgálók szolgáltatást, a meglévő felhasználók az Azure-fájlmegosztást az aktuális ügyfelektől egyszeri bejelentkezéssel érhetik el, a használatban lévő hitelesítő adatok módosítása nélkül.  

> [!NOTE]
> A Azure Files AD-hitelesítés néhány gyakori felhasználási esethez való beállításának elősegítése érdekében [két videót](https://docs.microsoft.com/azure/storage/files/storage-files-introduction#videos) tettünk közzé részletes útmutatóval:
> - Helyszíni fájlkiszolgálók cseréje Azure Files (beleértve a fájlokhoz és az AD-hitelesítéshez tartozó magánhálózati kapcsolaton keresztül történő telepítést)
> - A Azure Files használata a Windows rendszerű virtuális asztali profil tárolójának használatával (beleértve az AD-hitelesítés és a FsLogix-konfiguráció beállítását is)

## <a name="prerequisites"></a>Előfeltételek 

Az Azure-fájlmegosztás AD DS hitelesítésének engedélyezése előtt győződjön meg arról, hogy végrehajtotta a következő előfeltételeket: 

- Válassza ki vagy hozza létre AD DS-környezetét, és [szinkronizálja az Azure ad](../../active-directory/hybrid/how-to-connect-install-roadmap.md)-vel. 

    A szolgáltatást egy új vagy meglévő helyszíni AD DS környezetben is engedélyezheti. A hozzáféréshez használt identitásokat szinkronizálni kell az Azure AD-vel. Az Azure AD-bérlőt és az elérni kívánt fájlmegosztást ugyanahhoz az előfizetéshez kell társítani. 

    AD tartományi környezet beállításához tekintse meg az [Active Directory tartományi szolgáltatások áttekintése című témakört](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Ha nem szinkronizálta az AD-t az Azure AD-val, kövesse az [Azure ad Connect és Azure ad Connect Health telepítési ütemtervének](../../active-directory/hybrid/how-to-connect-install-roadmap.md) útmutatását a Azure ad Connect konfigurálásához és beállításához. 

- Tartomány – helyszíni vagy Azure-beli virtuális gép csatlakoztatása helyszíni AD DShoz. 

    Ha egy számítógépről vagy virtuális gépről származó AD-hitelesítő adatok használatával szeretne hozzáférni egy fájlmegosztáshoz, az eszköznek tartományhoz kell tartoznia AD DShoz. További információ a tartományhoz való csatlakozásról: [számítógép csatlakoztatása tartományhoz](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain). 

- Válasszon ki vagy hozzon létre egy Azure Storage-fiókot [egy támogatott régióban](#regional-availability). 

    Győződjön meg arról, hogy a fájlmegosztást tartalmazó Storage-fiók még nincs konfigurálva az Azure AD DS-hitelesítéshez. Ha Azure Files Azure AD DS hitelesítés engedélyezve van a Storage-fiókon, akkor azt a helyszíni AD DS használata előtt le kell tiltani. Ez azt jelenti, hogy az Azure AD DS-környezetben konfigurált meglévő ACL-eket újra kell konfigurálni a megfelelő engedélyek kényszerítéséhez.
    
    További információ az új fájlmegosztás létrehozásáról: [fájlmegosztás létrehozása Azure Filesban](storage-how-to-create-file-share.md).
    
    Az optimális teljesítmény érdekében javasoljuk, hogy telepítse a Storage-fiókot ugyanabban a régióban, mint azt a virtuális gépet, amelyről el szeretné érni a megosztást. 

- Ellenőrizze a kapcsolatot az Azure-fájlmegosztás a Storage-fiók kulcsa használatával történő csatlakoztatásával. 

    Annak ellenőrzéséhez, hogy az eszköz és a fájlmegosztás megfelelően van-e konfigurálva, próbálja meg [csatlakoztatni a fájlmegosztást](storage-how-to-use-files-windows.md) a Storage-fiók kulcsa alapján. Ha a Azure Fileshoz való csatlakozással kapcsolatos problémákat tapasztal, tekintse meg [a hibaelhárítási eszközt, amelyet Azure Files csatlakoztatási hibákhoz tettünk közzé a Windows](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)rendszeren. Az 445-as port blokkolása esetén [útmutatást](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access) is biztosítunk a forgatókönyvek megoldásához. 

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

A AD DS (előzetes verzió) Azure Files hitelesítés az [összes nyilvános régióban és az Azure gov-régiókban](https://azure.microsoft.com/global-infrastructure/locations/)érhető el.

## <a name="workflow-overview"></a>A munkafolyamat áttekintése

Az Azure-fájlmegosztás SMB-en keresztüli AD DS hitelesítésének engedélyezése előtt javasoljuk, hogy olvassa el és fejezze be az [Előfeltételek](#prerequisites) szakaszt. Az előfeltételek ellenőrzik, hogy az AD, az Azure AD és az Azure Storage-környezet megfelelően van-e konfigurálva. 

Ha azt tervezi, hogy engedélyezi a fájlmegosztás hálózati konfigurációját, javasoljuk, hogy az AD DS hitelesítés engedélyezése előtt először ellenőrizze a [hálózati megfontolást](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) , és végezze el a kapcsolódó konfigurációt.

Ezután kövesse az alábbi lépéseket az Active Directory-hitelesítés Azure Files beállításához: 

1. Azure Files AD DS hitelesítés engedélyezése a Storage-fiókban. 

2. Rendeljen hozzá egy megosztáshoz hozzáférési engedélyeket az Azure AD-identitáshoz (egy felhasználóhoz, csoporthoz vagy egyszerű szolgáltatáshoz), amely szinkronban van a cél AD-identitással. 

3. ACL-ek konfigurálása az SMB protokollal a címtárakhoz és a fájlokhoz. 
 
4. Csatlakoztassa az Azure-fájlmegosztást a AD DShoz csatlakoztatott virtuális géphez. 

5. Frissítse a Storage-fiók identitásának jelszavát AD DSban.

Az alábbi ábra a teljes munkafolyamatot mutatja be, amely lehetővé teszi az Azure AD-hitelesítés használatát az SMB-en keresztül az Azure-fájlmegosztás számára. 

![Fájlok AD munkafolyamat-diagramja](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> Az Azure-fájlmegosztás SMB protokollon keresztüli hitelesítésének AD DS csak a Windows 7 vagy Windows Server 2008 R2 rendszernél újabb verziójú operációs rendszert futtató gépeken vagy virtuális gépeken támogatott. 

## <a name="1-enable-ad-authentication-for-your-account"></a>1. az AD-hitelesítés engedélyezése a fiókhoz 

Az Azure-fájlmegosztás SMB-en keresztüli AD DS hitelesítésének engedélyezéséhez először regisztrálnia kell a Storage-fiókot a AD DS, majd be kell állítania a szükséges tartományi tulajdonságokat a Storage-fiókban. Ha a szolgáltatás engedélyezve van a Storage-fiókon, akkor az a fiók összes új és meglévő fájljára érvényes lesz. A `join-AzStorageAccountForAuth` funkció engedélyezéséhez használja a következőt:. Az ebben a szakaszban található parancsfájlban megtalálhatja a végpontok közötti munkafolyamat részletes leírását. 

> [!IMPORTANT]
> A `Join-AzStorageAccountForAuth` parancsmag módosításokat hajt végre az ad-környezetben. Olvassa el a következő magyarázatot, hogy jobban megértse, mit csinál, hogy megfelelő engedélyekkel rendelkezzen a parancs végrehajtásához, és hogy az alkalmazott módosítások összhangban legyenek a megfelelőségi és biztonsági szabályzatokkal. 

A `Join-AzStorageAccountForAuth` parancsmag a megadott Storage-fiók nevében a kapcsolat nélküli tartományhoz való csatlakozást fogja végezni. A szkript a parancsmag használatával hoz létre egy fiókot az AD-tartományban, vagy egy [számítógépfiók](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (alapértelmezett) vagy egy [szolgáltatás bejelentkezési fiókja](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). Ha ezt manuálisan választja, válassza ki a környezete számára legmegfelelőbb fiókot.

A parancsmag által létrehozott AD DS fiók a Storage-fiókot jelöli az AD-tartományban. Ha a AD DS fiók egy szervezeti egység (OU) alatt jön létre, amely kikényszeríti a jelszó lejáratát, akkor a jelszó maximális kora előtt frissítenie kell a jelszót. Ha nem sikerül frissíteni a fiók jelszavát, az Azure-fájlmegosztás elérésekor a rendszer hitelesítési hibákat eredményez. A jelszó frissítésével kapcsolatos további információkért lásd: [AD DS fiók jelszavának frissítése](#5-update-the-password-of-your-storage-account-identity-in-ad-ds).

A következő szkripttel végezheti el a regisztrációt, és engedélyezheti a funkciót, vagy manuálisan is végrehajthatja a parancsfájl által végrehajtott műveleteket. Ezeket a műveleteket a parancsfájlt követő szakasz ismerteti. Mindkettőt nem kell elvégeznie.

### <a name="11-script-prerequisites"></a>1,1 parancsfájl előfeltételei
- [Töltse le és csomagolja ki a AzFilesHybrid modult](https://github.com/Azure-Samples/azure-files-samples/releases)
- Telepítse és hajtsa végre a modult egy olyan eszközön, amely tartományhoz van csatlakoztatva a helyszíni AD DS olyan AD DS hitelesítő adatokkal, amelyek engedéllyel rendelkeznek a szolgáltatás bejelentkezési fiókjának vagy a célszámítógép fiókjának létrehozásához a cél AD-ben.
-  Futtassa a parancsfájlt egy helyszíni AD DS hitelesítő adat használatával, amely szinkronizálva van az Azure AD-vel. A helyszíni AD DS hitelesítő adatnak rendelkeznie kell a Storage-fiók tulajdonosával vagy a közreműködő RBAC szerepkör-jogosultságokkal.
- Győződjön meg arról, hogy a Storage-fiók egy [támogatott régióban](#regional-availability)található.

### <a name="12-domain-join-your-storage-account"></a>1,2 tartomány csatlakoztatása a Storage-fiókhoz
Ne felejtse el lecserélni a helyőrző értékeket az alábbi paraméterekkel, mielőtt végrehajtja a PowerShellben.
> [!IMPORTANT]
> A tartományhoz való csatlakozás parancsmag létrehoz egy AD-fiókot, amely a Storage-fiókot (fájlmegosztást) az AD-ben fogja ábrázolni. Megadhatja, hogy számítógép-fiókként vagy szolgáltatás-bejelentkezési fiókként regisztrálja a részleteket a [Gyakori kérdések](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) részben. Számítógépfiókok esetében az alapértelmezett jelszó lejárati ideje 30 nap múlva az AD-ben van beállítva. Hasonlóképpen, előfordulhat, hogy a szolgáltatás bejelentkezési fiókja az AD-tartományon vagy a szervezeti egységen (OU) beállított alapértelmezett jelszó-lejárati kort tartalmaz.
> Mindkét fióktípus esetében javasoljuk, hogy vizsgálja meg, hogy mi a jelszó lejárati ideje az AD-környezetben, és tervezze meg, hogy [frissítse a Storage-fiók identitásának jelszavát](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) az alábbi ad-fiókban a jelszó maximális kora előtt. Ha nem sikerül frissíteni az AD-fiók jelszavát, az Azure-fájlmegosztás elérésekor a rendszer hitelesítési hibákat eredményez. Érdemes lehet [új ad szervezeti egységet (OU-t) létrehozni az ad-ben](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) , és ennek megfelelően letiltani a jelszó lejárati házirendjét a [számítógép fiókjain](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN) vagy a szolgáltatás bejelentkezési fiókjain. 

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
> Ha a `Join-AzStorageAccountForAuth` fenti szkriptet már végrehajtotta, lépjen a következő szakaszra: "1,3 Ellenőrizze, hogy a funkció engedélyezve van-e". Az alábbi műveleteket nem kell újra végrehajtania.

#### <a name="a-checking-environment"></a>a. Környezet ellenőrzése

Először a parancsfájl ellenőrzi a környezetet. Pontosan ellenőrzi, hogy telepítve van-e [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) , és hogy a rendszerhéj rendszergazdai jogosultságokkal van-e végrehajtva. Ezután ellenőrzi, hogy telepítve van-e az az [. Storage 1.11.1 – Preview modul](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) , és ha nem, akkor telepíti. Ha ezek az ellenőrzések bekerülnek, akkor a AD DS ellenőrzi, hogy van-e olyan [számítógépfiók](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (alapértelmezett) vagy [szolgáltatás-bejelentkezési fiók](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) , amely már az SPN/UPN néven lett létrehozva "CIFS/a-Storage-Account-Name-here. file. Core. Windows. net". Ha a fiók nem létezik, a rendszer létrehoz egyet az alábbi b szakaszban leírtak szerint.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Az AD-ben manuálisan létrehozott Storage-fiókot jelölő identitás létrehozása

Ha manuálisan szeretné létrehozni a fiókot, hozzon létre egy új Kerberos-kulcsot a `New-AzStorageAccountKey -KeyName kerb1`Storage-fiókjához a használatával. Ezt követően használja a Kerberos-kulcsot a fiókjához tartozó jelszóként. Ez a kulcs csak a beállítás során használatos, és nem használható a Storage-fiókhoz tartozó vezérlési vagy adatsík műveletekhez.

Ha ezzel a kulccsal rendelkezik, hozzon létre egy szolgáltatás-vagy számítógépfiókot a szervezeti egység alatt. Használja a következő specifikációt: SPN: "CIFS/a-Storage-Account-Name-itt. file. Core. Windows. net" password: Kerberos-kulcs a Storage-fiókhoz.

Ha a szervezeti egysége kikényszeríti a jelszó lejáratát, akkor a jelszó maximális kora előtt frissítenie kell a jelszót, hogy megakadályozza a hitelesítési hibákat az Azure-fájlmegosztás elérésekor. A részletekért lásd: [a Storage-fiók identitásának jelszavának frissítése AD DS](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) .

Tartsa meg az újonnan létrehozott fiók biztonsági azonosítóját, a következő lépéshez szüksége lesz rá. A Storage-fiókot képviselő létrehozott identitást nem kell szinkronizálni az Azure AD-vel.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. A szolgáltatás engedélyezése a Storage-fiókban

A szkript ezután engedélyezi a szolgáltatást a Storage-fiókjában. Ha manuálisan szeretné elvégezni a telepítést, adja meg a tartományi tulajdonságok konfigurációs adatait a következő parancsban, majd futtassa. A következő parancsban a Storage-fiók SID azonosítója az [előző szakaszban](#b-creating-an-identity-representing-the-storage-account-in-your-ad-manually)a AD DS létrehozott identitás SID-azonosítója.

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


### <a name="13-confirm-that-the-feature-is-enabled"></a>1,3 Ellenőrizze, hogy a szolgáltatás engedélyezve van-e

A következő szkripttel ellenőrizheti, hogy a szolgáltatás engedélyezve van-e a Storage-fiókban:

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

Ezzel sikeresen engedélyezte a szolgáltatást a Storage-fiókjában. Most, hogy a funkció engedélyezve van, további lépéseket kell végrehajtania a funkció használatához.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Mostantól sikeresen engedélyezte AD DS hitelesítést az SMB protokollon keresztül, és hozzárendelt egy egyéni szerepkört, amely hozzáférést biztosít egy Azure-fájlmegosztás számára egy AD DS identitással. Ha további felhasználóknak szeretne hozzáférést adni a fájlmegosztás eléréséhez, kövesse a [hozzáférési engedélyek hozzárendelése](#2-assign-access-permissions-to-an-identity) az identitás használatához és az NTFS- [engedélyek SMB-szakaszokon keresztüli konfigurálásához](#3-configure-ntfs-permissions-over-smb) című témakör utasításait.

## <a name="5-update-the-password-of-your-storage-account-identity-in-ad-ds"></a>5. frissítse a Storage-fiók identitásának jelszavát AD DS

Ha regisztrálta AD DS a Storage-fiókját egy olyan szervezeti egységben, amely a jelszó lejárati idejét érvényesíti, akkor a jelszó maximális kora előtt el kell forgatni a jelszót. A AD DS fiók jelszavának frissítése sikertelen lesz az Azure-fájlmegosztás eléréséhez szükséges hitelesítési hibák miatt.  

A jelszó elforgatásának elindításához futtassa `Update-AzStorageAccountADObjectPassword` a parancsot a AzFilesHybrid modulból. A parancsmag a Storage-fiók kulcsának elforgatásához hasonló műveleteket hajt végre. Beolvassa a Storage-fiók második Kerberos-kulcsát, és felhasználja a regisztrált fiók jelszavának frissítésére AD DSban. Ezután újralétrehozza a Storage-fiók cél Kerberos-kulcsát, és frissíti a regisztrált fiók jelszavát AD DSban. Ezt a parancsmagot egy helyszíni AD DS tartományhoz csatlakoztatott környezetben kell futtatnia.

```PowerShell
# Update the password of the AD DS account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>További lépések

További információ a Azure Filesről és az AD-t használó SMB használatával kapcsolatban:

- [Az SMB-hozzáférésre vonatkozó Azure Files identitás-alapú hitelesítés támogatásának áttekintése](storage-files-active-directory-overview.md)
- [GYIK](storage-files-faq.md)
