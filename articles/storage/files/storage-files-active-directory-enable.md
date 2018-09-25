---
title: Az Azure Active Directory-hitelesítés engedélyezése az Azure Files (előzetes verzió) – Azure Storage SMB-n keresztül
description: Megtudhatja, hogyan azonosító-alapú hitelesítés engedélyezéséhez az SMB (Server Message Block) (előzetes verzió) az Azure Files segítségével az Azure Active Directory (Azure AD) Domain Services számára. A tartományhoz csatlakoztatott Windows virtuális gépek (VM) ezután hozzáférhetnek az Azure-fájlmegosztásokat az Azure AD hitelesítő adatait.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/19/2018
ms.author: tamram
ms.openlocfilehash: 31bfcd99e23cc7fe0575a293e3dc22507dbd9e13
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973269"
---
# <a name="enable-azure-active-directory-authentication-over-smb-for-azure-files-preview"></a>Az Azure Active Directory-hitelesítés engedélyezése az SMB-n keresztül az Azure Files (előzetes verzió)

[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Az Azure Files SMB-n keresztül az Azure AD-hitelesítés áttekintését lásd: [áttekintése az Azure Active Directory hitelesítési SMB-n keresztül az Azure Files (előzetes verzió)](storage-files-active-directory-overview.md).

## <a name="workflow-overview"></a>A munkafolyamat áttekintése

Mielőtt az Azure Files SMB-n keresztül engedélyezi az Azure AD, ellenőrizze, hogy az Azure AD és az Azure Storage-környezetek megfelelően legyenek konfigurálva. Javasoljuk, hogy vezeti végig a [Előfeltételek](#prerequisites) , győződjön meg arról, hogy végzett el a szükséges lépéseket. 

Ezután adja meg az Azure AD hitelesítő adatait az Azure Files-erőforrásokhoz való hozzáférés az alábbi lépéseket: 

1. Az Azure AD-hitelesítés engedélyezése a storage-fiókot regisztrálni a társított Azure AD tartományi szolgáltatások telepítési a tárfiók SMB-n keresztül.
2. Egy megosztásra vonatkozó hozzáférési engedélyeket rendelhet az Azure AD-identitás (felhasználó, csoport vagy szolgáltatásnév).
3. NTFS-engedélyek konfigurálása SMB-n keresztül a könyvtárak és fájlok.
4. Azure-fájlmegosztás csatlakoztatása egy tartományhoz csatlakoztatott virtuális gépről.

Az alábbi ábra szemlélteti a végpontok közötti munkafolyamat az Azure AD-hitelesítés engedélyezéséhez az Azure Files SMB-n keresztül szükséges. 

![Ábra az Azure AD SMB-n keresztül az Azure Files-munkafolyamat](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Előfeltételek 

1.  **Válassza ki, vagy hozzon létre egy Azure AD-bérlővel.**

    SMB-n keresztül használhatja egy új vagy meglévő bérlő az Azure AD-hitelesítés. A bérlői és az elérni kívánt fájlmegosztás ugyanahhoz az előfizetéshez társítva kell lennie.

    Hozzon létre egy új Azure AD-bérlőjében is [Azure AD-bérlő és a egy Azure AD-előfizetés hozzáadása](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Ha rendelkezik meglévő Azure AD-bérlővel, de szeretne létrehozni egy új bérlőt használja az Azure Files: [létrehozása az Azure Active Directory-bérlő](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

2.  **Az Azure AD-bérlőre az Azure AD tartományi szolgáltatások engedélyezése.**

    Támogatja a hitelesítés az Azure AD hitelesítő adatait, engedélyeznie kell az Azure AD-bérlő Azure AD tartományi szolgáltatásokat. Ha nem az Azure AD-bérlő rendszergazdája, forduljon a rendszergazdához, és kövesse a részletes útmutatónkat [engedélyezése az Active Directory Domain Servicest az Azure portal használatával](../../active-directory-domain-services/active-directory-ds-getting-started.md).

    Általában végrehajtásához az Azure AD Domain Services telepítését bemutató nagyjából 15 percet vesz igénybe. Győződjön meg arról, hogy az Azure AD tartományi szolgáltatások állapotát jeleníti meg **futó**, engedélyezve van a Jelszókivonat-szinkronizálás, a korábban a Folytatás a következő lépéssel.

3.  **Tartományhoz való csatlakozás egy Azure virtuális Gépen az Azure AD tartományi szolgáltatásokat.**

    Hozzáférhessenek egy adatmegosztáshoz, egy virtuális Gépet az Azure AD hitelesítő adataival, hogy a virtuális Gépnek kell lennie, az Azure AD Domain Services tartományhoz. Tartományhoz való csatlakozás a virtuális gép kapcsolatos további információkért lásd: [egy Windows Servert futtató virtuális gép csatlakoztatása felügyelt tartományokhoz](../../active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal.md).

    > [!NOTE]
    > Az Azure AD-hitelesítés az Azure Files SMB-n keresztül csak az operációsrendszer-verziók, Windows 7 vagy Windows Server 2008 R2 rendszert futtató Azure virtuális gépeken támogatott.

4.  **Válassza ki, vagy hozzon létre egy Azure-fájlmegosztást.**

    Válassza ki egy új vagy meglévő fájlmegosztást, amely ugyanazt az előfizetést az Azure AD-bérlővel van társítva. Új fájlmegosztás létrehozásával kapcsolatban lásd: [fájlmegosztás létrehozása az Azure Files](storage-how-to-create-file-share.md). 

    Az Azure AD-bérlővel kell telepíteni, SMB-n keresztül az Azure AD az előzetes verzióra támogatott. Az összes nyilvános régióban, az alábbiakat kivéve az előzetes verzió érhető el: USA nyugati RÉGIÓJA, USA nyugati RÉGIÓJA 2, USA déli középső Régiója, USA keleti RÉGIÓJA, USA keleti RÉGIÓJA 2, USA középső RÉGIÓJA, USA északi középső Régiója, Kelet-Ausztrália, Nyugat-Európa, Észak-Európa.

    Az optimális teljesítmény érdekében a Microsoft azt javasolja, hogy a fájlmegosztás a virtuális gép, amelyről azt tervezi, hogy a megosztás elérése ugyanabban a régióban van.

5.  **Ellenőrizze az Azure Files kapcsolatot csatlakoztatja az Azure-fájlmegosztások a tárfiók kulcsára.**

    Győződjön meg arról, hogy megfelelően vannak-e konfigurálva a virtuális gép és a fájlmegosztást, próbálja meg csatlakoztatja a fájlmegosztást a tárfiók kulcsára. További információkért lásd: [egy Azure-fájlmegosztás csatlakoztatása és a Windows a megosztás elérése](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-authentication"></a>Az Azure AD-hitelesítés engedélyezése

Miután végrehajtotta a [Előfeltételek](#prerequisites), engedélyezheti az Azure AD-hitelesítés SMB-n keresztül.

### <a name="step-1-enable-azure-ad-authentication-over-smb-for-your-storage-account"></a>1. lépés: A tárfiók engedélyezze az SMB-n keresztül az Azure AD-hitelesítés

Az Azure AD-hitelesítés engedélyezéséhez SMB-n keresztül az Azure Files számára adhatók meg egy tulajdonságot 29, 2018 augusztus után létrehozott storage-fiókok az Azure Storage erőforrás-szolgáltató a PowerShell vagy az Azure CLI használatával. A következő tulajdonság beállítása az Azure Portal előzetes kiadásban nem támogatott. 

Ez a tulajdonság beállítása a társított Azure AD tartományi szolgáltatások telepítési a storage-fiókban regisztrálja. SMB-n keresztül az Azure AD-hitelesítés majd engedélyezve van az összes meglévő és új fájlmegosztást a storage-fiókban. 

Ne feledje, hogy engedélyezheti az Azure AD-hitelesítés SMB-n keresztül csak, miután sikeresen telepítette az Azure AD tartományi szolgáltatásokat az Azure AD-bérlővel. További információkért tekintse meg a [Előfeltételek](#prerequisites).

**PowerShell**

SMB-n keresztül az Azure AD-hitelesítés engedélyezéséhez telepítse a `AzureRM.Storage 6.0.0-preview` PowerShell-modult. PowerShell telepítésével kapcsolatos információkért lásd: [Azure PowerShell telepítése a Windows a Powershellgettel](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

Ezután hívja [Set-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/set-azurermstorageaccount) és állítsa be a **EnableAzureFilesAadIntegrationForSMB** paramétert **igaz**. Az alábbi példában a vágólapra a helyőrző értékeket cserélje le a saját értékeire.

```powershell
# Create a new storage account
New-AzureRmStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureFilesAadIntegrationForSMB $true

# Update an existing storage account
# Supported for storage accounts created after August 29, 2018 only
Set-AzureRmStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureFilesAadIntegrationForSMB $true```
```

**Parancssori felület**

"Kell először adja hozzá a storage-preview jelű verziót bővítményt, majd hajtsa végre a példa helye. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire. Tekintse meg ebben a cikkben talál további információt az Azure CLI bővítményeivel."

Az Azure AD-hitelesítés engedélyezése az Azure CLI 2.0-s SMB-n keresztül, először telepítse a *storage – előzetes verzió* bővítményt:

```azurecli-interactive
az extension add --name storage-preview
```

Ezután hívja [storage-fiók frissítése az](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) és állítsa be a `--file-aad` tulajdonságot **igaz**. Az alábbi példában a vágólapra a helyőrző értékeket cserélje le a saját értékeire.

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --file-aad true

# Update an existing storage account
# Supported for storage accounts created after August 29, 2018 only
az storage account update -n <storage-account-name> -g <resource-group-name> --file-aad true
```

### <a name="step-2-assign-access-permissions-to-an-identity"></a>2. lépés: A hozzáférési engedélyek hozzárendelése az identitás 

Azure AD hitelesítő adatait az Azure Files-erőforrások eléréséhez az identitás (felhasználó, csoport vagy szolgáltatásnév) a megosztás szintjén kell rendelkeznie a szükséges engedélyekkel. A részletes útmutatója az alábbi bemutatja, hogyan rendelje hozzá az olvasási, írási, vagy egy fájlmegosztás-identitásra vonatkozó engedélyeinek törlése.

> [!IMPORTANT]
> Egy fájlmegosztást, beleértve a szerepkör hozzárendelése a identitást, teljes körű felügyeletet igényel, a tárfiók-kulcsot használ. Az Azure AD hitelesítő adatait a felügyeleti vezérlő nem támogatott. 

#### <a name="step-21-define-a-custom-role"></a>2.1. lépés: Adja meg az egyéni szerepkör

Megosztási szintű engedélyeket, adja meg egy egyéni RBAC-szerepkört, és rendelje hozzá az identitás felmerülő, egy adott fájlmegosztáshoz. Ez a folyamat hasonlít a Windows megosztása engedélyek megadásával adhatja meg, amely egy adott felhasználó fájlmegosztás hozzáférési.  

A sablonok a következő szakaszban látható adja meg a fájlmegosztás engedélyeinek olvasása vagy módosítása. Adja meg egy egyéni biztonsági szerepkört, hozzon létre egy JSON-fájlt, és másolja ki a megfelelő sablont, amely a fájl. További információ az egyéni RBAC-szerepkörök definiálása: [egyéni szerepkörök az Azure-ban](../../role-based-access-control/custom-roles.md).

**Szerepkör-definíció a megosztásszintű engedélyeinek módosítása**

A következő egyéni szerepkör sablon megosztásszintű engedélyeinek módosítása, az identitás olvasási, írási és törlési jogosultsága a megosztáshoz biztosítása biztosít.

```json
{
  "Name": "<Custom-Role-Name>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read, write and delete access to Azure File Share",
  "Actions": [
    "*"
  ],
  "NotActions": [
      "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ],
  "DataActions": [
    "*"
  ],
  "AssignableScopes": [
        "/subscriptions/<Subscription-ID>"
  ]
}
```

**Szerepkör-definíció megosztásszintű olvasási engedélyek**

A következő egyéni szerepkör-sablon megosztásszintű olvasási engedélyeket, az identitás olvasási hozzáférést a megosztáshoz biztosít.

```json
{
  "Name": "<Custom-Role-Name>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure File Share",
  "Actions": [
    "*/read"
  ],
  "DataActions": [
    "*/read"
  ],
  "AssignableScopes": [
        "/subscriptions/<Subscription-ID>"
  ]
}
```

#### <a name="step-22-create-the-custom-role-and-assign-it-to-the-target-identity"></a>2.2. lépés: Az egyéni szerepkör létrehozása, és rendelje hozzá a cél-identitás

Ezután használhatja a szerepkör létrehozásához, és rendelje hozzá az Azure AD identity PowerShell vagy az Azure CLI használatával. 

**PowerShell**

SMB-n keresztül az Azure AD-hitelesítés engedélyezéséhez telepítse a `AzureRM.Storage 6.0.0-preview` PowerShell-modult. PowerShell telepítésével kapcsolatos információkért lásd: [Azure PowerShell telepítése a Windows a Powershellgettel](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

A következő PowerShell-parancs létrehoz egy egyéni biztonsági szerepkört, és a szerepkört rendel egy Azure AD-identitásnak, a bejelentkezési neve alapján. A PowerShell-lel RBAC-szerepkörök hozzárendelésével kapcsolatos további információkért lásd: [RBAC és az Azure PowerShell-hozzáférés kezelése](../../role-based-access-control/role-assignments-powershell.md).

Az alábbi parancsprogram futtatásakor ne felejtse el lecserélni a helyőrző értékeket a saját értékeire.

```powershell
#Create a custom role based on the sample template above
New-AzureRmRoleDefinition -InputFile "<custom-role-def-json-path>"
#Get the name of the custom role
$FileShareContributorRole = Get-AzureRmRoleDefinition "<role-name>"
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzureRmRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

**Parancssori felület**

A következő CLI 2.0 parancs létrehoz egy egyéni biztonsági szerepkört, és a szerepkört rendel egy Azure AD-identitásnak, a bejelentkezési neve alapján. Az Azure CLI-vel RBAC-szerepkörök hozzárendelése kapcsolatos további információkért lásd: [RBAC és az Azure CLI-hozzáférés kezelése](../../role-based-access-control/role-assignments-cli.md). 

Az alábbi parancsprogram futtatásakor ne felejtse el lecserélni a helyőrző értékeket a saját értékeire.

```azurecli-interactive
#Create a custom role based on the sample templates above
az role definition create --role-definition "<Custom-role-def-JSON-path>"
#List the custom roles
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
#Assign the custom role to the target identity
az role assignment create --role "<custome-role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
```

### <a name="step-3-configure-ntfs-permissions-over-smb"></a>3. lépés: Az NTFS-engedélyek konfigurálása a SMB-n keresztül 

Az RBAC megosztási szintű engedélyek hozzárendelése után hozzá kell rendelnie a megfelelő NTFS-engedéllyel a legfelső szintű, könyvtárat vagy fájlt szintjén. Úgy gondolja, hogy a megosztás szintű engedélyekkel a, a magas szintű forgalomirányító, amely meghatározza, hogy e egy felhasználó hozzáférhessen a megosztást, amíg a felhasználó egy részletesebb szintet határozza meg, milyen műveleteket intézkedjen NTFS-engedélyek hajthat végre, a fájl vagy könyvtár szintjén. 

Az Azure Files NTFS Alapszintű és speciális engedélyek teljes készletét támogatja. Megtekintheti és NTFS-engedélyek konfigurálása a könyvtárak és fájlok az Azure-fájlmegosztások a megosztás csatlakoztatásával, és futtassa a Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) vagy [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) parancsot. 

> [!NOTE]
> Az előzetes kiadásban megtekintésre engedélyeket a Windows Fájlkezelővel csak támogatja. Szerkesztési jogosultsággal még nem támogatott.

NTFS-engedélyek konfigurálása a SUPERUSER felhasználói jogosultságai, csatlakoztatnia kell a megosztás és a tárfiók kulcsának a tartományhoz csatlakoztatott virtuális gépről. Kövesse az utasításokat az Azure-fájlmegosztás csatlakoztatása a parancssorból, és ennek megfelelően NTFS-engedélyek konfigurálása a következő szakaszban.

A következő eljárások egyikét az engedélyeket a fájlmegosztás gyökérkönyvtárában támogatják:

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\USERS:(Rx)
- BUILTIN\USERS:(OI)(CI)(IO)(GR,GE)
- NT Authority\Hitelesített Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- LÉTREHOZÓ OWNER:(OI)(CI)(IO)(F)

#### <a name="step-31-mount-an-azure-file-share-from-the-command-prompt"></a>3.1. lépés Azure-fájlmegosztás csatlakoztatása a parancssor használatával

Használja a Windows **használata net** parancsot az Azure-fájlmegosztás csatlakoztatásához. Fontos, hogy a példában a helyőrző értékeket cserélje le a saját értékeire. Fájlmegosztások kapcsolatos további információkért lásd: [egy Azure-fájlmegosztás csatlakoztatása és a Windows a megosztás elérése](storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```

#### <a name="step-32-configure-ntfs-permissions-with-icacls"></a>3.2. lépés konfigurálása NTFS-engedéllyel rendelkező icacls

A következő Windows-parancs segítségével minden könyvtárnak és fájlnak a fájlmegosztást, beleértve a legfelső szintű könyvtár alatt teljes körű engedélyeket. Fontos, hogy a példában a helyőrző értékeket cserélje le a saját értékeire.

```
icacls <mounted-drive-letter> /grant <user-email>:(f)
```

További információ az NTFS-engedélyek beállítására és az engedélyek támogatott különböző típusú, lásd: icacls használatával [icacls parancssori referenciája](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

### <a name="step-4-mount-an-azure-file-share-from-a-domain-joined-vm"></a>4. lépés: Azure-fájlmegosztás csatlakoztatása egy tartományhoz csatlakoztatott virtuális gépről 

Most már készen áll a győződjön meg arról, hogy végzett a fenti lépéseket sikeresen használatával egy tartományhoz csatlakoztatott virtuális gépről ossza meg az Azure AD hitelesítő adatait az Azure fájl eléréséhez. Első lépésként jelentkezzen be a virtuális gép az Azure AD-identitásnak, amelyhez kapott engedélyt, a következő képen látható módon.

![Képernyőkép bemutató az Azure AD bejelentkezési képernyőn a felhasználók hitelesítéséhez](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

Ezután az alábbi parancs segítségével az Azure-fájlmegosztás csatlakoztatása. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire. Ön már hitelesített, mert nem kell a tárfiók kulcsát, vagy az Azure AD-felhasználó nevét és jelszavát. SMB-n keresztül az Azure AD egyszeri bejelentkezés használatát Azure AD hitelesítő adatok használatával támogatja.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

Most már sikeresen engedélyezve van az Azure AD-hitelesítés SMB-n keresztül, és egy egyéni szerepkört, amely egy Azure AD identity fájlmegosztás hozzáférést biztosít. A fájlmegosztás hozzáférést további felhasználók számára, kövesse az utasításokat, a 2. lépésben megadott.

## <a name="next-steps"></a>További lépések

Az Azure Files és az SMB-n keresztül az Azure AD-vel kapcsolatos további információkért tekintse meg ezeket az erőforrásokat:

- [Bevezetés az Azure Files használatába](storage-files-introduction.md)
- [Az Azure Files (előzetes verzió) SMB-n keresztül az Azure Active Directory-hitelesítés áttekintése](storage-files-active-directory-overview.md)
- [GYIK](storage-files-faq.md)