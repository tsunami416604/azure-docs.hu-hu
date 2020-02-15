---
title: A Azure AD Domain Services használata az SMB-en keresztüli fájlok elérésének engedélyezéséhez
description: Megtudhatja, hogyan engedélyezheti az identitás-alapú hitelesítést a Server Message Block (SMB) protokollon keresztül Azure Files a Azure Active Directory Domain Services használatával. A tartományhoz csatlakoztatott Windows-alapú virtuális gépek (VM-EK) az Azure AD-beli hitelesítő adatok használatával érhetik el az Azure-fájlmegosztást.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 06ff14b23057755a643e5a57fbaf711798cca00e
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210482"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Azure Active Directory Domain Services hitelesítés engedélyezése Azure Files

[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

A Azure Files által támogatott identitás-alapú hitelesítés áttekintését lásd: [az SMB protokollon keresztüli Azure Active Directory hitelesítés áttekintése Azure Files](storage-files-active-directory-overview.md). Ebből a cikkből megtudhatja, hogyan engedélyezheti a (z) Azure Active Directory Domain Services (Azure AD DS) hitelesítését Azure Fileson. 
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>A munkafolyamat áttekintése

Mielőtt engedélyezi az Azure AD DS hitelesítést az SMB protokollon keresztül a Azure Files számára, ellenőrizze, hogy az Azure AD és az Azure Storage-környezetek megfelelően vannak-e konfigurálva. Javasoljuk, hogy járjon el az [Előfeltételek](#prerequisites) között, és győződjön meg arról, hogy végrehajtotta az összes szükséges lépést.

Ezután az alábbi lépéseket követve engedélyezze az Azure AD-beli hitelesítő adatokkal Azure Files erőforrásokhoz való hozzáférést:

1. Engedélyezze az Azure AD DS hitelesítést az SMB protokollon keresztül a Storage-fiók számára a Storage-fiók regisztrálásához a társított Azure AD DS-telepítéssel.
2. Megosztás hozzáférési engedélyeinek kiosztása egy Azure AD-identitáshoz (felhasználó, csoport vagy egyszerű szolgáltatásnév).
3. NTFS-engedélyek konfigurálása az SMB protokollal a címtárakhoz és a fájlokhoz.
4. Azure-fájlmegosztás csatlakoztatása tartományhoz csatlakoztatott virtuális gépről.

Az alábbi ábra a teljes munkafolyamatot mutatja be, amely lehetővé teszi az Azure AD DS hitelesítés használatát az SMB-en keresztül a Azure Files számára.

![Az Azure AD-t az Azure Files-munkafolyamathoz SMB-t megjelenítő diagram](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Előfeltételek

Mielőtt engedélyezi az Azure AD-t az SMB-en keresztül a Azure Fileshoz, győződjön meg arról, hogy végrehajtotta a következő előfeltételeket:

1.  **Válasszon ki vagy hozzon létre egy Azure AD-bérlőt.**

    Az Azure AD-hitelesítéshez új vagy meglévő bérlőt használhat az SMB protokollon keresztül. A bérlőt és az elérni kívánt fájlmegosztást ugyanahhoz az előfizetéshez kell társítani.

    Új Azure AD-bérlő létrehozásához [hozzáadhat egy Azure ad-bérlőt és egy Azure ad-előfizetést](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Ha rendelkezik meglévő Azure AD-Bérlővel, de szeretne létrehozni egy új bérlőt Azure Fileshoz való használatra, tekintse meg a [Azure Active Directory-bérlő létrehozása](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant)című témakört.

2.  **Engedélyezze Azure AD Domain Services az Azure AD-bérlőn.**

    Az Azure AD-beli hitelesítő adatokkal való hitelesítés támogatásához engedélyeznie kell Azure AD Domain Services az Azure AD-bérlő számára. Ha nem az Azure AD-bérlő rendszergazdája, lépjen kapcsolatba a rendszergazdával, és kövesse a lépésenkénti útmutatót, amely [lehetővé teszi Azure Active Directory Domain Services használatát a Azure Portal használatával](../../active-directory-domain-services/tutorial-create-instance.md).

    Általában körülbelül 15 percet vesz igénybe, hogy az Azure AD DS üzembe helyezése befejeződjön. A következő lépés végrehajtása előtt ellenőrizze, hogy **fut**-e az Azure AD DS állapota, és hogy engedélyezve van-e a jelszó kivonatának szinkronizálása.

3.  **Tartomány – Azure-beli virtuális gép csatlakoztatása az Azure AD DShoz.**

    Ha egy virtuális gépről Azure AD-beli hitelesítő adatokkal kívánja elérni a fájlmegosztást, a virtuális gépnek tartományhoz kell tartoznia az Azure AD DS-hez. A virtuális gépek tartományhoz való csatlakoztatásával kapcsolatos további információkért lásd: [Windows Server rendszerű virtuális gép csatlakoztatása felügyelt tartományhoz](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > Az Azure AD DS a hitelesítést az SMB protokollon keresztül, Azure Files csak a Windows 7 vagy Windows Server 2008 R2 operációs rendszereken futó Azure-beli virtuális gépeken támogatott.

4.  **Válasszon ki vagy hozzon létre egy Azure-fájlmegosztást.**

    Válasszon ki egy új vagy meglévő fájlmegosztást, amely ugyanahhoz az előfizetéshez tartozik, mint az Azure AD-bérlő. További információ az új fájlmegosztás létrehozásáról: [fájlmegosztás létrehozása Azure Filesban](storage-how-to-create-file-share.md).
    Az optimális teljesítmény érdekében javasoljuk, hogy a fájlmegosztás ugyanabban a régióban legyen, mint a virtuális gép, amelyről a megosztást szeretné elérni.

5.  **Ellenőrizze Azure Files kapcsolatot az Azure-fájlmegosztás a Storage-fiók kulcsa használatával történő csatlakoztatásával.**

    Annak ellenőrzéséhez, hogy a virtuális gép és a fájlmegosztás megfelelően van-e konfigurálva, próbálja meg csatlakoztatni a fájlmegosztást a Storage-fiók kulcsa alapján. További információ: [Azure-fájlmegosztás csatlakoztatása és a megosztás elérése a Windowsban](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Azure AD DS-hitelesítés engedélyezése a fiókhoz

Ha engedélyezni szeretné az Azure AD DS hitelesítést az SMB protokollon keresztül a Azure Fileshoz, a Azure Portal, a Azure PowerShell vagy az Azure CLI használatával beállíthat egy tulajdonságot a Storage-fiókokban. Ha a tulajdonságot implicit módon állítja be, a "tartomány csatlakozik" a Storage-fiókhoz a társított Azure AD DS-telepítéssel. Az Azure AD DS az SMB protokollon keresztüli hitelesítés a Storage-fiókban lévő összes új és meglévő fájlmegosztás esetében engedélyezve lesz.

Ne feledje, hogy az Azure AD DS-hitelesítést csak akkor engedélyezheti az SMB-en keresztül, ha sikeresen telepítette az Azure AD DSt az Azure AD-bérlőbe. További információ: [Előfeltételek](#prerequisites).

### <a name="azure-portal"></a>Azure Portal

Ha engedélyezni szeretné az Azure AD DS hitelesítést az SMB protokollon keresztül a [Azure Portal](https://portal.azure.com)használatával, kövesse az alábbi lépéseket:

1. A Azure Portal nyissa meg a meglévő Storage-fiókot, vagy [hozzon létre egy Storage-fiókot](../common/storage-account-create.md).
2. A **Beállítások** szakaszban válassza a **Konfigurálás**lehetőséget.
3. Válassza ki az **Azure Active Directory Domain Services (azure AD DS)** elemet az **Identity-based Directory szolgáltatásból az Azure file Authentication** legördülő listához.

Az alábbi képen bemutatjuk, hogyan engedélyezhető az Azure AD DS hitelesítés az SMB protokollon keresztül a Storage-fiókhoz.

![Az Azure AD-hitelesítés engedélyezése az SMB protokollon keresztül a Azure Portal](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

### <a name="powershell"></a>PowerShell  

Ha engedélyezni szeretné az Azure AD DS hitelesítést az SMB protokollon keresztül a Azure PowerShell, telepítse a legújabbat az az Module (2,4 vagy újabb) vagy az az. Storage modul (1,5 vagy újabb). A PowerShell telepítésével kapcsolatos további információkért lásd: [Azure PowerShell telepítése Windows rendszerre a PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps)használatával.

Új Storage-fiók létrehozásához hívja a [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0), majd állítsa a **EnableAzureActiveDirectoryDomainServicesForFile** paramétert **true (igaz**) értékre. A következő példában ne felejtse el lecserélni a helyőrző értékeket a saját értékeire. (Ha az előző előzetes modult használta, a szolgáltatás engedélyezésének paramétere a következő: **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

Ha engedélyezni szeretné a funkciót a meglévő Storage-fiókokon, használja a következő parancsot:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Azure CLI

Ha engedélyezni szeretné az Azure AD-hitelesítést az SMB protokollon keresztül az Azure CLI-vel, telepítse a CLI legújabb verzióját (2.0.70 vagy újabb verzió). Az Azure CLI telepítésével kapcsolatos további információkért lásd: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Hozzon létre egy új Storage-fiókot az[az Storage Account Create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)paranccsal, és állítsa a `--enable-files-aadds` tulajdonságot **true (igaz**) értékre. A következő példában ne felejtse el lecserélni a helyőrző értékeket a saját értékeire. (Ha az előző előzetes modult használta, a szolgáltatás engedélyezésének paramétere a **file-HRE**.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Ha engedélyezni szeretné a funkciót a meglévő Storage-fiókokon, használja a következő parancsot:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```


## <a name="assign-access-permissions-to-an-identity"></a>Hozzáférési engedélyek kiosztása identitáshoz

Az Azure AD-beli hitelesítő adatokkal rendelkező Azure Files erőforrásainak eléréséhez az identitásnak (egy felhasználónak, csoportnak vagy egyszerű szolgáltatásnak) rendelkeznie kell a szükséges engedélyekkel a megosztás szintjén. Ez a folyamat hasonló a Windows-megosztási engedélyek megadásához, ahol megadhatja, hogy egy adott felhasználó milyen típusú hozzáférést osszon meg a fájlmegosztás számára. Az ebben a szakaszban található útmutatás azt mutatja be, hogyan lehet olvasási, írási vagy törlési engedélyeket rendelni egy fájlmegosztás identitásához.

Két Azure-beli beépített szerepkört vezettünk be a felhasználók számára a megosztási szintű engedélyek megadásához:

- **Storage file-adat SMB-megosztás olvasója** az Azure Storage-fájlmegosztás SMB-kapcsolaton keresztüli olvasási hozzáférését teszi lehetővé.
- A **Storage file-adat SMB-megosztási közreműködői** lehetővé teszik az olvasási, írási és törlési hozzáférést az Azure Storage-fájlmegosztás SMB-en keresztül.
- A **Storage file-ADATsmb-megosztás emelt szintű közreműködője** lehetővé teszi az Azure Storage-FÁJLMEGOSZTÁS az SMB protokollon keresztül történő olvasását, írását, törlését és módosítását.

> [!IMPORTANT]
> Egy fájlmegosztás teljes körű felügyeleti felügyelete, beleértve a szerepkör identitáshoz való hozzárendelésének lehetőségét is, a Storage-fiók kulcsát kell használnia. Az Azure AD hitelesítő adatai nem támogatják a felügyeleti felügyeletet.

A Azure Portal, a PowerShell vagy az Azure CLI használatával hozzárendelheti a beépített szerepköröket egy felhasználó Azure AD-identitásához a megosztási szintű engedélyek megadásához.

#### <a name="azure-portal"></a>Azure Portal
Ha RBAC-szerepkört szeretne hozzárendelni egy Azure AD-identitáshoz a [Azure Portal](https://portal.azure.com)használatával, kövesse az alábbi lépéseket:

1. A Azure Portal nyissa meg a fájlmegosztást, vagy [hozzon létre egy fájlmegosztást a Azure Filesban](storage-how-to-create-file-share.md).
2. Válassza a **Access Control (iam)** lehetőséget.
3. Válassza **a szerepkör-hozzárendelés hozzáadása** elemet.
4. A **szerepkör-hozzárendelés hozzáadása** panelen válassza ki a megfelelő beépített szerepkört (tárolási fájl adatsmb-megosztási olvasó, tárolási fájl adat SMB-megosztás közreműködője) a **szerepkör** listából. Az alapértelmezett beállításnál tartsa **meg a hozzáférés kiosztása** beállítást: **Azure ad-felhasználó, csoport vagy egyszerű szolgáltatásnév**. Válassza ki a cél Azure AD-identitást név vagy e-mail-cím alapján.
5. A szerepkör-hozzárendelési művelet befejezéséhez válassza a **Mentés** lehetőséget.

#### <a name="powershell"></a>PowerShell

A következő PowerShell-parancs bemutatja, hogyan rendelhet hozzá egy RBAC-szerepkört egy Azure AD-identitáshoz a bejelentkezési név alapján. A RBAC-szerepkörök PowerShell-lel való hozzárendelésével kapcsolatos további információkért lásd: [a hozzáférés kezelése a RBAC és a Azure PowerShell használatával](../../role-based-access-control/role-assignments-powershell.md).

A következő minta parancsfájl futtatása előtt ne felejtse el helyettesíteni a helyőrző értékeket, beleértve a zárójeleket is a saját értékeivel.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>parancssori felület

A következő CLI 2,0-parancs bemutatja, hogyan rendelhet hozzá egy RBAC-szerepkört egy Azure AD-identitáshoz a bejelentkezési név alapján. A RBAC szerepköreinek Azure CLI-vel való hozzárendelésével kapcsolatos további információkért lásd: [a hozzáférés kezelése a RBAC és az Azure CLI használatával](../../role-based-access-control/role-assignments-cli.md).

A következő minta parancsfájl futtatása előtt ne felejtse el helyettesíteni a helyőrző értékeket, beleértve a zárójeleket is a saját értékeivel.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>NTFS-engedélyek konfigurálása SMB-kapcsolaton keresztül
A megosztási szintű engedélyek RBAC való hozzárendelését követően megfelelő NTFS-engedélyeket kell rendelnie a gyökér, a könyvtár vagy a fájl szintjén. Gondoljon arra, hogy a megosztási szintű engedélyek magas szintű forgalomirányító, amely meghatározza, hogy a felhasználó hozzáférhet-e a megosztáshoz. Míg az NTFS-engedélyek részletesebben határozzák meg, hogy a felhasználó milyen műveleteket végezhet el a címtárban vagy a fájl szintjén.

Azure Files a teljes NTFS alapszintű és speciális engedélyeket támogatja. Az Azure-fájlmegosztás könyvtárain és fájljain NTFS-engedélyeket tekinthet meg és konfigurálhat, ha csatlakoztatja a megosztást, majd a Windows fájlkezelővel vagy a Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) vagy a [set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) parancsot futtatja.

Az NTFS rendszergazdai jogosultságokkal történő konfigurálásához a megosztást a tartományhoz csatlakoztatott virtuális gépről a Storage-fiók kulcsa használatával kell csatlakoztatnia. Kövesse a következő szakaszban található utasításokat egy Azure-fájlmegosztás parancssorból való csatlakoztatásához és ennek megfelelően az NTFS-engedélyek konfigurálásához.

A fájlmegosztás gyökérkönyvtárában a következő engedélyek támogatottak:

- Builtin\rendszergazda: (OI) (CI) (F)
- NT AUTHORITY\SYSTEM: (OI) (CI) (F)
- BUILTIN\Users: (RX)
- BUILTIN\Users: (OI) (CI) (IO) (GR, GE)
- NT AUTHORITY\Authenticated-felhasználók: (OI) (CI) (M)
- NT AUTHORITY\SYSTEM: (F)
- LÉTREHOZÓ TULAJDONOS: (OI) (CI) (IO) (F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Fájlmegosztás csatlakoztatása a parancssorból

Az Azure-fájlmegosztás csatlakoztatásához használja a Windows **net use** parancsot. Ne felejtse el lecserélni az alábbi példában szereplő helyőrző értékeket a saját értékeire. A fájlmegosztás csatlakoztatásával kapcsolatos további információkért lásd: [Azure-fájlmegosztás csatlakoztatása és a megosztás elérése a Windowsban](storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>NTFS-engedélyek konfigurálása a Windows fájlkezelővel
A Windows fájlkezelővel teljes hozzáférést biztosíthat a fájlmegosztás alatt lévő összes könyvtárhoz és fájlhoz, beleértve a gyökérkönyvtárat is.

1. Nyissa meg a Windows fájlkezelő alkalmazást, és kattintson a jobb gombbal a fájlra/könyvtárra, és válassza a **Tulajdonságok** lehetőséget
2. Kattintson a **Biztonság** fülre.
3. Kattintson a **Szerkesztés..** . elemre. az engedélyek módosításához szükséges gomb
4. Módosíthatja a meglévő felhasználók engedélyeit, vagy kattintson a **Hozzáadás...** gombra az új felhasználók engedélyeinek megadásához.
5. Az új felhasználók felvételének megadására szolgáló ablakban adja meg azt a célként megadott felhasználónevet, amelyet az **adja meg a kijelölendő objektumok nevét** mezőbe, majd kattintson **a Névellenőrzés elemre, hogy** megkeresse a CÉLKÉNT megadott felhasználó teljes UPN-nevét.
7.  Kattintson **az OK** gombra
8.  A biztonság lapon válassza ki az összes olyan engedélyt, amelyet az újonnan felvett felhasználónak szeretne megadni
9.  Kattintson az **alkalmaz** gombra

### <a name="configure-ntfs-permissions-with-icacls"></a>NTFS-engedélyek konfigurálása icacls-val
A következő Windows-paranccsal teljes körű engedélyeket adhat meg a fájlmegosztás alatt lévő összes könyvtárhoz és fájlhoz, beleértve a gyökérkönyvtárat is. Ne felejtse el lecserélni a példában szereplő helyőrző értékeket a saját értékeire.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Ha többet szeretne megtudni arról, hogyan használható a icacls az NTFS-engedélyek megadásához és a különböző típusú támogatott engedélyekhez, tekintse meg [az icacls parancssori útmutatóját](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Fájlmegosztás csatlakoztatása tartományhoz csatlakoztatott virtuális gépről

A következő folyamat ellenőrzi, hogy az Azure AD-beli hitelesítő adatai megfelelően vannak-e beállítva, és hogy elérhető-e egy Azure-fájlmegosztás egy tartományhoz csatlakoztatott virtuális gépről:

Jelentkezzen be a virtuális gépre azzal az Azure AD-identitással, amelyhez engedélyeket adott meg, ahogy az az alábbi képen is látható.

![Az Azure AD bejelentkezési képernyőjét bemutató képernyőkép a felhasználói hitelesítéshez](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

Az Azure-fájlmegosztás csatlakoztatásához használja az alábbi parancsot. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire. Mivel már hitelesítette a hitelesítést, nem kell megadnia a Storage-fiók kulcsát vagy az Azure AD-beli felhasználónevét és jelszavát. Az Azure AD az SMB protokollon keresztül egyszeri bejelentkezési élményt nyújt az Azure AD hitelesítő adataival.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

Mostantól sikeresen engedélyezte az Azure AD-hitelesítést az SMB protokollon keresztül, és hozzárendelt egy egyéni szerepkört, amely hozzáférést biztosít egy Azure-fájlmegosztás Azure AD-identitással való eléréséhez. Ha további felhasználóknak szeretne hozzáférést adni a fájlmegosztás eléréséhez, kövesse a [hozzáférési engedélyek hozzárendelése identitáshoz](#assign-access-permissions-to-an-identity) és az NTFS- [engedélyek konfigurálása SMB](#configure-ntfs-permissions-over-smb) -szakaszokban című témakör utasításait.

## <a name="next-steps"></a>Következő lépések

A Azure Files és az Azure AD SMB használatával történő használatáról további információt az alábbi forrásokban talál:

- [Bevezetés a Azure Filesba](storage-files-introduction.md)
- [Az SMB-n történő Azure Active Directory-hitelesítés Azure Fileshoz való használatának áttekintése](storage-files-active-directory-overview.md)
- [GYIK](storage-files-faq.md)
