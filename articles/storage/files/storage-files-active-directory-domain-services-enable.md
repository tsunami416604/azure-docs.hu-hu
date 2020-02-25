---
title: Az SMB protokollon keresztüli Active Directory hitelesítés engedélyezése Azure Files
description: Megtudhatja, hogyan engedélyezheti az Azure-fájlmegosztás SMB-n keresztüli identitás-alapú hitelesítését Active Directoryon keresztül. A tartományhoz csatlakoztatott Windows-alapú virtuális gépek (VM-EK) az AD hitelesítő adatok használatával érhetik el az Azure-fájlmegosztást.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: rogarana
ms.openlocfilehash: 4a80565df10b470fe6d3fe590578681f85a3bdd9
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566282"
---
# <a name="enable-active-directory-authentication-over-smb-for-azure-file-shares"></a>Az Azure-fájlmegosztás SMB-en keresztüli Active Directory hitelesítésének engedélyezése

[Azure Files](storage-files-introduction.md) az támogatja a kiszolgálói üzenetblokk (SMB) protokollon keresztüli identitás-alapú hitelesítést a következő két típusú tartományi szolgáltatáson keresztül: Azure Active Directory Domain Services (Azure AD DS) (GA) és Active Directory (ad) (előzetes verzió). Ez a cikk az Azure-fájlmegosztás hitelesítésének Active Directory-tartomány szolgáltatásának az újonnan bevezetett (előzetes verzió) támogatását ismerteti. Ha szeretné engedélyezni, hogy az Azure AD DS (GA) hitelesítése az Azure-fájlmegosztás esetében, tekintse [meg a tárgyat tartalmazó cikket](storage-files-active-directory-enable.md). 

> [!NOTE]
> Az Azure-fájlmegosztás csak az egyik tartományi szolgáltatás, Azure Active Directory tartományi szolgáltatás (Azure AD DS) vagy Active Directory (AD) közötti hitelesítést támogatja. 
>
> Az Azure-fájlmegosztás hitelesítéséhez használt AD-identitásokat szinkronizálni kell az Azure AD-vel. A jelszó-kivonat szinkronizálása nem kötelező. 
> 
> Az AD-hitelesítés nem támogatja az Azure AD DSban létrehozott számítógépfiókok hitelesítését. 
> 
> Az AD-hitelesítés csak egy olyan AD-erdőben támogatott, amelyben a Storage-fiók regisztrálva van. Alapértelmezés szerint csak egyetlen AD-erdő Active Directory-beli hitelesítő adataival érheti el az Azure-fájlmegosztást. Ha egy másik erdőből kell hozzáférnie az Azure-fájlmegosztás számára, győződjön meg arról, hogy a megfelelő erdőszintű megbízhatósági kapcsolat van konfigurálva.  
> 
> Az AD-hitelesítés az SMB-hozzáféréshez és az ACL-megőrzéshez Azure File Sync által felügyelt Azure-fájlmegosztás esetén támogatott.

Ha engedélyezi az AD-t az Azure-fájlmegosztás SMB-kapcsolaton keresztül, az AD-tartományhoz csatlakoztatott gépek az Azure-fájlmegosztást meglévő AD-hitelesítő adataival csatlakoztathatja. Ez a funkció engedélyezhető egy helyszíni gépeken vagy az Azure-ban üzemeltetett AD-környezettel.

Az Azure-fájlmegosztás eléréséhez használt AD-identitásokat szinkronizálni kell az Azure AD-vel, hogy a standard [szerepköralapú hozzáférés-vezérlési (RBAC)](../../role-based-access-control/overview.md) modell használatával kényszerítse a megosztási szintű fájlokra vonatkozó engedélyeket. A meglévő fájlkiszolgálók által átvitt fájlok/könyvtárak [Windows-stílusú DACL-listái](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) megmaradnak és érvényben lesznek. Ez zökkenőmentesen integrálható a vállalati AD tartományi infrastruktúrával. Amikor az Azure-fájlmegosztás használatával cseréli le a helyszíni fájlkiszolgálók szolgáltatást, a meglévő felhasználók az Azure-fájlmegosztást az aktuális ügyfelektől egyszeri bejelentkezéssel érhetik el, a használatban lévő hitelesítő adatok módosítása nélkül.  
 
## <a name="prerequisites"></a>Előfeltételek 

Az Azure-fájlmegosztás AD-hitelesítésének engedélyezése előtt győződjön meg arról, hogy végrehajtotta a következő előfeltételeket: 

- Válassza ki vagy hozza létre az AD-környezetet, és szinkronizálja az Azure AD-vel. 

    A funkciót új vagy meglévő AD-környezetben is engedélyezheti. A hozzáféréshez használt identitásokat szinkronizálni kell az Azure AD-vel. Az Azure AD-bérlőt és az elérni kívánt fájlmegosztást ugyanahhoz az előfizetéshez kell társítani. 

    AD tartományi környezet beállításához tekintse meg az [Active Directory tartományi szolgáltatások áttekintése című témakört](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Ha nem szinkronizálta az AD-t az Azure AD-val, kövesse a [Mi az a hibrid identitás a Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md) -mel című témakör útmutatását, hogy meghatározza az előnyben részesített hitelesítési módszert és Azure ad Connect telepítési lehetőséget. 

- Tartomány – egy helyszíni vagy egy Azure-beli virtuális gép csatlakoztatása AD DS vagy AD használatával. 

    Ha egy számítógépről vagy virtuális gépről származó AD-hitelesítő adatok használatával szeretne hozzáférni egy fájlmegosztáshoz, az eszköznek tartományhoz kell tartoznia AD DShoz. További információ az AD-vel való tartományhoz való csatlakozásról: [számítógép csatlakoztatása tartományhoz](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain). 

- Válasszon ki vagy hozzon létre egy Azure Storage-fiókot [egy támogatott régióban](#regional-availability). 

    Győződjön meg arról, hogy a fájlmegosztást tartalmazó Storage-fiók még nincs konfigurálva az Azure AD DS-hitelesítéshez. Ha Azure Files Azure AD DS hitelesítés engedélyezve van a Storage-fiókon, akkor azt le kell tiltani az AD használata előtt. Ez azt jelenti, hogy az Azure AD DS-környezetben konfigurált meglévő ACL-eket újra kell konfigurálni a megfelelő engedélyek kényszerítéséhez.
    
    További információ az új fájlmegosztás létrehozásáról: [fájlmegosztás létrehozása Azure Filesban](storage-how-to-create-file-share.md).
    
    Az optimális teljesítmény érdekében javasoljuk, hogy a Storage-fiók ugyanabban a régióban legyen, mint azt a virtuális gépet, amelyről el szeretné érni a megosztást. 

- Ellenőrizze a kapcsolatot az Azure-fájlmegosztás a Storage-fiók kulcsa használatával történő csatlakoztatásával. 

    Annak ellenőrzéséhez, hogy az eszköz és a fájlmegosztás megfelelően van-e konfigurálva, próbálja meg csatlakoztatni a fájlmegosztást a Storage-fiók kulcsa alapján. További információ: Azure- [fájlmegosztás használata Windows rendszeren](storage-how-to-use-files-windows.md).

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

Azure Files AD-hitelesítés a [legtöbb nyilvános régióban](https://azure.microsoft.com/global-infrastructure/regions/)elérhető, a régiók azon részhalmaza, amely még nem érhető el a-ben:

- USA nyugati régiója
- USA nyugati régiója, 2.
- USA keleti régiója
- USA 2. keleti régiója
- Nyugat-Európa
- Észak-Európa

## <a name="workflow-overview"></a>A munkafolyamat áttekintése

Az Azure-fájlmegosztás SMB-n keresztüli AD-hitelesítésének engedélyezése előtt javasoljuk, hogy az [előfeltételeket](#prerequisites) , és győződjön meg arról, hogy végrehajtotta az összes lépést. Az előfeltételek ellenőrzik, hogy az AD, az Azure AD és az Azure Storage-környezet megfelelően van-e konfigurálva. 

Ezután adjon hozzáférést Azure Files erőforrásokhoz az AD hitelesítő adatokkal: 

- Engedélyezze Azure Files AD-hitelesítést a Storage-fiókjában.  

- Rendeljen hozzá egy megosztáshoz hozzáférési engedélyeket az Azure AD-identitáshoz (egy felhasználóhoz, csoporthoz vagy egyszerű szolgáltatáshoz), amely szinkronban van a cél AD-identitással. 

- ACL-ek konfigurálása az SMB protokollal a címtárakhoz és a fájlokhoz. 

- Azure-fájlmegosztás csatlakoztatása AD-tartományhoz csatlakoztatott virtuális gépről. 

Az alábbi ábra a teljes munkafolyamatot mutatja be, amely lehetővé teszi az Azure AD-hitelesítés használatát az SMB-en keresztül az Azure-fájlmegosztás számára. 

![Fájlok AD munkafolyamat-diagramja](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> Az Azure-fájlmegosztás SMB protokollon keresztüli AD-hitelesítése csak a Windows 7 vagy Windows Server 2008 R2 rendszernél újabb verziójú operációs rendszert futtató gépeken vagy virtuális gépeken támogatott. 

## <a name="enable-ad-authentication-for-your-account"></a>AD-hitelesítés engedélyezése a fiókhoz 

Az Azure-fájlmegosztás SMB-alapú hitelesítésének engedélyezéséhez először regisztrálnia kell a Storage-fiókot az AD-ben, majd be kell állítania a szükséges tartományi tulajdonságokat a Storage-fiókban. Ha a szolgáltatás engedélyezve van a Storage-fiókon, akkor az a fiók összes új és meglévő fájljára érvényes lesz. A szolgáltatás engedélyezéséhez használja a `join-AzStorageAccountForAuth`. A végpontok közötti munkafolyamat részletes leírását az alábbi szakaszban találja. 

> [!IMPORTANT]
> Az `join-AzStorageAccountForAuth` parancsmag módosításokat hajt végre az AD-környezetben. Olvassa el a következő magyarázatot, hogy jobban megértse, mit csinál, hogy megfelelő engedélyekkel rendelkezzen a parancs végrehajtásához, és hogy az alkalmazott módosítások összhangban legyenek a megfelelőségi és biztonsági szabályzatokkal. 

A `join-AzStorageAccountForAuth` parancsmag a megadott Storage-fiók nevében a kapcsolat nélküli tartományhoz való csatlakozást fogja végezni. Létrehoz egy fiókot az AD-tartományban, vagy egy [számítógépfiók](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) vagy egy [szolgáltatás bejelentkezési fiókja](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). A létrehozott AD-fiók a Storage-fiókot jelöli az AD-tartományban. Ha az AD-fiók egy olyan AD szervezeti egység (OU) alatt jön létre, amely kikényszeríti a jelszó lejáratát, akkor a jelszó maximális kora előtt frissítenie kell a jelszót. Ha nem sikerül frissíteni az AD-fiók jelszavát, az Azure-fájlmegosztás elérésekor a rendszer hitelesítési hibákat eredményez. A jelszó frissítésével kapcsolatos további információkért lásd: az [ad-fiók jelszavának frissítése](#update-ad-account-password).

A következő szkripttel végezheti el a regisztrációt, és engedélyezheti a funkciót, vagy manuálisan is végrehajthatja a parancsfájl által végrehajtott műveleteket. Ezeket a műveleteket a parancsfájlt követő szakasz ismerteti. Mindkettőt nem kell elvégeznie.

### <a name="script-prerequisites"></a>Parancsfájlok előfeltételei

- [Töltse le és csomagolja ki a AzFilesHybrid modult](https://github.com/Azure-Samples/azure-files-samples/releases)
- Telepítse és hajtsa végre a modult olyan eszközön, amely tartományhoz csatlakozik az ad hitelesítő adataival, amelyek rendelkeznek a szolgáltatás bejelentkezési fiókjának vagy a cél AD-beli számítógépfiók létrehozásához szükséges engedélyekkel.
-  Futtassa a szkriptet az Azure AD-vel szinkronizált AD-hitelesítő adatok használatával. Az AD-hitelesítő adatoknak rendelkeznie kell a Storage-fiók tulajdonosával vagy a közreműködői RBAC szerepkör engedélyeivel.
- Győződjön meg arról, hogy a Storage-fiók egy [támogatott régióban](#regional-availability)található.

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope Currentuser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Select the target subscription for the current session
Select-AzureSubscription -SubscriptionId "<yourSubscriptionIdHere>"

#Register the target storage account with your active directory environment under the target OU
join-AzStorageAccountForAuth -ResourceGroupName "<resource-group-name-here>" -Name "<storage-account-name-here>" -DomainAccountType "<ServiceLogonAccount|ComputerAccount>" -OrganizationUnitName "<ou-name-here>"
```


Az alábbiakban a `join-AzStorageAccountForAuth` parancs használatakor végrehajtott műveletek leírását ismertetjük. Ezeket a lépéseket manuálisan is végrehajthatja, ha nem szeretné használni a parancsot:

### <a name="checking-environment"></a>Környezet ellenőrzése

Először ellenőrzi a környezetet. Különösen ellenőrzi, hogy telepítve van-e a [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) , és hogy a rendszerhéj rendszergazdai jogosultságokkal van-e végrehajtva. Ezután ellenőrzi, hogy telepítve van-e az az [. Storage 1.11.1 – Preview modul](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) , és ha nem, akkor telepíti. Ha ezek az ellenőrzések bejelentkeznek, akkor a rendszer ellenőrzi, hogy van-e olyan [számítógépfiók](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (alapértelmezett) vagy [szolgáltatás-bejelentkezési fiók](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) , amely már létrehozva van SPN/UPN néven (CIFS/a-Storage-Account-Name-here. file. Core. Windows. net), és ha nem létezik, hozzon létre egyet.

### <a name="creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>Az AD-ben manuálisan létrehozott Storage-fiókot jelölő identitás létrehozása

Ha manuálisan szeretné létrehozni a fiókot, hozzon létre egy új Kerberos-kulcsot a Storage-fiókjához `New-AzStorageAccountKey -Keynam "yourKeyName"`használatával. Ezt követően használja a Kerberos-kulcsot a fiókjához tartozó jelszóként. Ezt a kulcsot csak a telepítés során használja a rendszer, és nem használható semmilyen vezérlési vagy adatsík-művelethez a Storage-fiókon.

Ha ezzel a kulccsal rendelkezik, hozzon létre egy szolgáltatás-vagy számítógépfiókot a szervezeti egység alatt. Használja a következő specifikációt: SPN: "CIFS/a-Storage-Account-Name-itt. file. Core. Windows. net" password: Kerberos-kulcs a Storage-fiókhoz.

Ha a szervezeti egysége kikényszeríti a jelszó lejáratát, akkor a jelszó maximális kora előtt frissítenie kell a jelszót, hogy megakadályozza a hitelesítési hibákat az Azure-fájlmegosztás elérésekor. További részletek: az [ad-fiók jelszavának frissítése](#update-ad-account-password) .

Tartsa meg az újonnan létrehozott fiók biztonsági azonosítóját, a következő lépéshez szüksége lesz rá.

### <a name="enable-the-feature-on-your-storage-account"></a>A szolgáltatás engedélyezése a Storage-fiókban

A szkript ezután engedélyezi a szolgáltatást a Storage-fiókjában. Ha ezt manuálisan szeretné elvégezni, adja meg a tartományi tulajdonságok konfigurációs adatait a következő parancsban, majd futtassa. A következő parancsban a Storage-fiók biztonsági azonosítója az AD-ben létrehozott identitás SID-azonosítója.

```PowerShell
#Set the feature flag on the target storage account and provide the required AD domain information

Set-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>" -EnableActiveDirectoryDomainServiesForFile $true -ActiveDirectoryDomainName "<your-domain-name-here>" -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" -ActiveDirectoryForestName "<your-forest-name-here>" -ActiveDirectoryDomainGuid "<your-guid-here>" -ActiveDirectoryDomainsid "<your-domain-sid-here>" -ActiveDirectoryAzureStirageSid "<your-storage-account-sid>"
```


### <a name="check-if-the-feature-is-enabled"></a>Ellenőrizze, hogy a szolgáltatás engedélyezve van-e

Ha szeretné megtekinteni, hogy engedélyezve van-e a szolgáltatás a Storage-fiókban, használja a következő parancsfájlt:

```PowerShell
#Get the target storage account
$storageaccount = Get-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>"

#List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

#List the directory domain information if the storage account has enabled AD authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Ezzel sikeresen engedélyezte a szolgáltatást a Storage-fiókjában. Annak ellenére, hogy a szolgáltatás engedélyezve van, továbbra is végre kell hajtania további műveleteket, hogy megfelelően használhassa a szolgáltatást.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Mostantól sikeresen engedélyezte az Azure AD-hitelesítést az SMB protokollon keresztül, és hozzárendelt egy egyéni szerepkört, amely hozzáférést biztosít egy Azure-fájlmegosztás számára egy AD-identitással. Ha további felhasználóknak szeretne hozzáférést adni a fájlmegosztás eléréséhez, kövesse a [hozzáférési engedélyek hozzárendelése](#assign-access-permissions-to-an-identity) az identitás használatához és az NTFS- [engedélyek SMB-szakaszokon keresztüli konfigurálásához](#configure-ntfs-permissions-over-smb) című témakör utasításait.

## <a name="update-ad-account-password"></a>AD-fiók jelszavának frissítése

Ha regisztrálta a Storage-fiókját egy olyan szervezeti egységben, amely kikényszeríti a jelszó lejárati idejét, el kell forgatni a jelszót a jelszó maximális kora előtt. Az Active Directory-fiók jelszavának frissítése sikertelen lesz az Azure-fájlmegosztás eléréséhez szükséges hitelesítési hibák miatt.  

A jelszó elforgatásának elindításához futtassa a `Update-AzStorageAccountADObjectPassword` parancsot a [AzFilesHybrid modulból](#script-prerequisites). A parancsmag a Storage-fiók kulcsának elforgatásához hasonló műveleteket hajt végre. Beolvassa a Storage-fiók második Kerberos-kulcsát, és felhasználja a regisztrált fiók jelszavának frissítésére az AD-ben. Ezután újra létrehozza a Storage-fiók cél Kerberos-kulcsát, és frissíti a regisztrált fiók jelszavát az AD-ben. Ezt a parancsmagot egy AD-tartományhoz csatlakoztatott környezetben kell futtatnia.

```PowerShell
#Update the password of the AD account registered for the storage account
Update-AzStorageAccountADObjectPassword -RotateToKerbKey kerb2 -ResourceGroupName "your-resource-group-name-here" -StorageAccountName "your-storage-account-name-here"
```

## <a name="next-steps"></a>További lépések

További információ a Azure Filesről és az AD-t használó SMB használatával kapcsolatban:

- [Az SMB-hozzáférésre vonatkozó Azure Files identitás-alapú hitelesítés támogatásának áttekintése](storage-files-active-directory-overview.md)
- [GYIK](storage-files-faq.md)