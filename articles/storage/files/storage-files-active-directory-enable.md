---
title: Az Azure Active Directory-hitelesítés engedélyezése az Azure Files (előzetes verzió) – Azure Storage SMB-n keresztül
description: Megtudhatja, hogyan azonosító-alapú hitelesítés engedélyezéséhez az SMB (Server Message Block) (előzetes verzió) az Azure Files segítségével az Azure Active Directory (Azure AD) Domain Services számára. A tartományhoz csatlakoztatott Windows virtuális gépek (VM) ezután hozzáférhetnek az Azure-fájlmegosztásokat az Azure AD hitelesítő adatait.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/02/2019
ms.author: tamram
ms.openlocfilehash: fd635682d1b5dc7c3ab784208ac485872d5c7099
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "53998999"
---
# <a name="enable-azure-active-directory-authentication-over-smb-for-azure-files-preview"></a>Az Azure Active Directory-hitelesítés engedélyezése az SMB-n keresztül az Azure Files (előzetes verzió)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Az Azure Files SMB-n keresztül az Azure AD-hitelesítés áttekintését lásd: [áttekintése az Azure Active Directory hitelesítési SMB-n keresztül az Azure Files (előzetes verzió)](storage-files-active-directory-overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>A munkafolyamat áttekintése
Mielőtt az Azure Files SMB-n keresztül engedélyezi az Azure AD, ellenőrizze, hogy az Azure AD és az Azure Storage-környezetek megfelelően legyenek konfigurálva. Javasoljuk, hogy vezeti végig a [Előfeltételek](#prerequisites) , győződjön meg arról, hogy végzett el a szükséges lépéseket. 

Ezután adja meg az Azure AD hitelesítő adatait az Azure Files-erőforrásokhoz való hozzáférés az alábbi lépéseket: 

1. Az Azure AD-hitelesítés engedélyezése a storage-fiókot regisztrálni a társított Azure AD tartományi szolgáltatások telepítési a tárfiók SMB-n keresztül.
2. Egy megosztásra vonatkozó hozzáférési engedélyeket rendelhet az Azure AD-identitás (felhasználó, csoport vagy szolgáltatásnév).
3. NTFS-engedélyek konfigurálása SMB-n keresztül a könyvtárak és fájlok.
4. Azure-fájlmegosztás csatlakoztatása egy tartományhoz csatlakoztatott virtuális gépről.

Az alábbi ábra szemlélteti a végpontok közötti munkafolyamat az Azure AD-hitelesítés engedélyezéséhez az Azure Files SMB-n keresztül szükséges. 

![Ábra az Azure AD SMB-n keresztül az Azure Files-munkafolyamat](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Előfeltételek 

Mielőtt az Azure Files SMB-n keresztül engedélyezi az Azure AD, győződjön meg arról, befejeződött a következő előfeltételek vonatkoznak:

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
    Az optimális teljesítmény érdekében a Microsoft azt javasolja, hogy a fájlmegosztás a virtuális gép, amelyről azt tervezi, hogy a megosztás elérése ugyanabban a régióban van.

5.  **Ellenőrizze az Azure Files kapcsolatot csatlakoztatja az Azure-fájlmegosztások a tárfiók kulcsára.**

    Győződjön meg arról, hogy megfelelően vannak-e konfigurálva a virtuális gép és a fájlmegosztást, próbálja meg csatlakoztatja a fájlmegosztást a tárfiók kulcsára. További információkért lásd: [egy Azure-fájlmegosztás csatlakoztatása és a Windows a megosztás elérése](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-authentication-for-your-account"></a>A fiók az Azure AD-hitelesítés engedélyezése

Az Azure AD-hitelesítés engedélyezéséhez SMB-n keresztül az Azure Files számára beállíthat egy tulajdonságot a 2018. szeptember 24., után létrehozott storage-fiókok az Azure Portalon, az Azure PowerShell vagy az Azure CLI használatával. Ez a tulajdonság beállítása a társított Azure AD tartományi szolgáltatások telepítési a storage-fiókban regisztrálja. SMB-n keresztül az Azure AD-hitelesítés majd engedélyezve van az összes meglévő és új fájlmegosztást a storage-fiókban. 

Ne feledje, hogy engedélyezheti az Azure AD-hitelesítés SMB-n keresztül csak, miután sikeresen telepítette az Azure AD tartományi szolgáltatásokat az Azure AD-bérlővel. További információkért tekintse meg a [Előfeltételek](#prerequisites).

### <a name="azure-portal"></a>Azure Portal

Az Azure AD-hitelesítés engedélyezéséhez az SMB használatához képest a [az Azure portal](https://portal.azure.com), kövesse az alábbi lépéseket:

1. Az Azure Portalon keresse meg a meglévő tárfiókot, vagy [hozzon létre egy tárfiókot](../common/storage-quickstart-create-account.md).
2. Az a **beállítások** szakaszban jelölje be **konfigurációs**.
3. Engedélyezése **Azure Active Directory-hitelesítést az Azure Files (előzetes verzió)**.

Az alábbi képen látható az Azure AD-hitelesítés engedélyezése az SMB-n keresztül a tárfiók számára.

![Az Azure AD-hitelesítés engedélyezéséhez az SMB-n keresztül az Azure Portalon](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)
  
### <a name="powershell"></a>PowerShell  

Az Azure AD-hitelesítés engedélyezése az Azure PowerShell SMB-n keresztül, először telepítse az előzetes verzió build a `Az.Storage` modul, amely az Azure AD támogatja. PowerShell telepítésével kapcsolatos további információkért lásd: [Azure PowerShell telepítése a Windows a Powershellgettel](https://docs.microsoft.com/powershell/azure/install-Az-ps):

```powershell
Install-Module -Name Az.Storage -AllowPrerelease -Force -AllowClobber
```

Következő lépésként hozzon létre egy új tárfiókot fiókra, majd hívjon [Set-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) és állítsa be a **EnableAzureFilesAadIntegrationForSMB** paramétert **igaz**. Az alábbi példában a vágólapra a helyőrző értékeket cserélje le a saját értékeire.

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureFilesAadIntegrationForSMB $true

# Update an existing storage account
# Supported for storage accounts created after September 24, 2018 only
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureFilesAadIntegrationForSMB $true```
```

### <a name="azure-cli"></a>Azure CLI

Az Azure AD-hitelesítés engedélyezése az Azure CLI 2.0-s SMB-n keresztül, először telepítse a `storage-preview` bővítményt:

```cli-interactive
az extension add --name storage-preview
```
  
Következő lépésként hozzon létre egy új tárfiókot fiókra, majd hívjon [storage-fiók frissítése az](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) és állítsa be a `--file-aad` tulajdonságot **igaz**. Az alábbi példában a vágólapra a helyőrző értékeket cserélje le a saját értékeire.

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --file-aad true

# Update an existing storage account
# Supported for storage accounts created after September 24, 2018 only
az storage account update -n <storage-account-name> -g <resource-group-name> --file-aad true
```

## <a name="assign-access-permissions-to-an-identity"></a>Hozzáférési engedélyek hozzárendelése a identitás 

Azure AD hitelesítő adatait az Azure Files-erőforrások eléréséhez az identitás (felhasználó, csoport vagy szolgáltatásnév) a megosztás szintjén kell rendelkeznie a szükséges engedélyekkel. Ebben a szakaszban található útmutatást bemutatja, hogyan rendelje hozzá az olvasási, írási, vagy egy fájlmegosztás-identitásra vonatkozó engedélyeinek törlése.

> [!IMPORTANT]
> Egy fájlmegosztást, beleértve a szerepkör hozzárendelése a identitást, teljes körű felügyeletet igényel, a tárfiók-kulcsot használ. Az Azure AD hitelesítő adatait a felügyeleti vezérlő nem támogatott. 

### <a name="define-a-custom-role"></a>Egyéni szerepkör definiálása

Megosztási szintű engedélyeket, adja meg egy egyéni RBAC-szerepkört, és rendelje hozzá az identitás felmerülő, egy adott fájlmegosztáshoz. Ez a folyamat hasonlít a Windows megosztása engedélyek megadásával adhatja meg, amely egy adott felhasználó fájlmegosztás hozzáférési.  

A sablonok a következő szakaszban látható adja meg a fájlmegosztás engedélyeinek olvasása vagy módosítása. Adja meg egy egyéni biztonsági szerepkört, hozzon létre egy JSON-fájlt, és másolja ki a megfelelő sablont, amely a fájl. További információ az egyéni RBAC-szerepkörök definiálása: [egyéni szerepkörök az Azure-ban](../../role-based-access-control/custom-roles.md).

**Szerepkör-definíció a megosztásszintű engedélyeinek módosítása**  
A következő egyéni szerepkör sablon megosztásszintű engedélyeinek módosítása, az identitás olvasási, írási és törlési jogosultsága a megosztáshoz biztosítása biztosít.

```json
{
  "Name": "<Custom-Role-Name>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read, write and delete access to Azure File Share over SMB",
  "Actions": [
    "Microsoft.Storage/storageAccounts/fileServices/fileshare/*"
  ],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/*"
  ],
  "NotDataActions": [
    "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermission",
    "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/actasadmin"
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
  "Description": "Allows for read access to Azure File Share over SMB",
  "Actions": [
    "Microsoft.Storage/storageAccounts/fileServices/fileshare/read"
  ],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read"
  ],
  "AssignableScopes": [
        "/subscriptions/<Subscription-ID>"
  ]
}
```

### <a name="create-the-custom-role"></a>Az egyéni szerepkör létrehozása

Az egyéni szerepkör létrehozásához használja a PowerShell vagy az Azure CLI használatával. 

#### <a name="powershell"></a>PowerShell

A következő PowerShell-parancs létrehoz egy egyéni biztonsági szerepkört a mintasablonokat egyike alapján.

```powershell
#Create a custom role based on the sample template above
New-AzRoleDefinition -InputFile "<custom-role-def-json-path>"
```

#### <a name="cli"></a>parancssori felület 

Az Azure CLI-parancsot egy egyéni biztonsági szerepkört a mintasablonokat egyike alapján hoz létre.

```azurecli-interactive
#Create a custom role based on the sample templates above
az role definition create --role-definition "<Custom-role-def-JSON-path>"
```

### <a name="assign-the-custom-role-to-the-target-identity"></a>Az egyéni szerepkör hozzárendelése a cél-identitás

Ezután használja az egyéni szerepkör hozzárendelése egy Azure AD identity PowerShell vagy az Azure CLI használatával. 

#### <a name="powershell"></a>PowerShell

A következő PowerShell-parancs bemutatja, hogyan egyéni szerepkörök listázása és a egy egyéni biztonsági szerepkört hozzárendelheti az Azure AD identitás, a bejelentkezési neve alapján. A PowerShell-lel RBAC-szerepkörök hozzárendelésével kapcsolatos további információkért lásd: [RBAC és az Azure PowerShell-hozzáférés kezelése](../../role-based-access-control/role-assignments-powershell.md).

Az alábbi parancsprogram futtatásakor ne felejtse el lecserélni a helyőrző értékeket, beleértve a zárójeleket, a saját értékeire.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>"
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>parancssori felület
  
A következő CLI 2.0-parancs bemutatja, hogyan egyéni szerepkörök listázása és a egy egyéni biztonsági szerepkört hozzárendelheti az Azure AD identitás, a bejelentkezési neve alapján. Az Azure CLI-vel RBAC-szerepkörök hozzárendelése kapcsolatos további információkért lásd: [RBAC és az Azure CLI-hozzáférés kezelése](../../role-based-access-control/role-assignments-cli.md). 

Az alábbi parancsprogram futtatásakor ne felejtse el lecserélni a helyőrző értékeket, beleértve a zárójeleket, a saját értékeire.

```azurecli-interactive
#List the custom roles
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
#Assign the custom role to the target identity
az role assignment create --role "<custome-role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>NTFS-engedélyek konfigurálása SMB-n keresztül 
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

### <a name="mount-a-file-share-from-the-command-prompt"></a>A fájlmegosztás csatlakoztatása a parancssor használatával

Használja a Windows **használata net** parancsot az Azure-fájlmegosztás csatlakoztatásához. Fontos, hogy a példában a helyőrző értékeket cserélje le a saját értékeire. Fájlmegosztások kapcsolatos további információkért lásd: [egy Azure-fájlmegosztás csatlakoztatása és a Windows a megosztás elérése](storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```

### <a name="configure-ntfs-permissions-with-icacls"></a>Icacls NTFS-engedélyek konfigurálása
A következő Windows-parancs segítségével minden könyvtárnak és fájlnak a fájlmegosztást, beleértve a legfelső szintű könyvtár alatt teljes körű engedélyeket. Ne felejtse el lecserélni a helyőrző értékeket, a példában a saját értékeire zárójelben látható.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

További információ az NTFS-engedélyek beállítására és az engedélyek támogatott különböző típusú, lásd: icacls használatával [icacls parancssori referenciája](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>A fájlmegosztás csatlakoztatása egy tartományhoz csatlakoztatott virtuális gépről 

Most már készen áll a győződjön meg arról, hogy végzett a fenti lépéseket sikeresen használatával egy tartományhoz csatlakoztatott virtuális gépről ossza meg az Azure AD hitelesítő adatait az Azure fájl eléréséhez. Első lépésként jelentkezzen be a virtuális gép az Azure AD-identitásnak, amelyhez kapott engedélyt, a következő képen látható módon.

![Képernyőkép bemutató az Azure AD bejelentkezési képernyőn a felhasználók hitelesítéséhez](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

Ezután az alábbi parancs segítségével az Azure-fájlmegosztás csatlakoztatása. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire. Ön már hitelesített, mert nem kell a tárfiók kulcsát, vagy az Azure AD-felhasználó nevét és jelszavát. SMB-n keresztül az Azure AD egyszeri bejelentkezés használatát Azure AD hitelesítő adatok használatával támogatja.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

Most már sikeresen engedélyezve van az Azure AD-hitelesítés SMB-n keresztül, és egy egyéni szerepkört, amely egy Azure AD identity fájlmegosztás hozzáférést biztosít. A fájlmegosztás hozzáférést további felhasználók számára, kövesse az utasításokat, 2. és 3. lépésében megadott.

## <a name="next-steps"></a>További lépések

Az Azure Files és az SMB-n keresztül az Azure AD-vel kapcsolatos további információkért tekintse meg ezeket az erőforrásokat:

- [Bevezetés az Azure Files használatába](storage-files-introduction.md)
- [Az Azure Files (előzetes verzió) SMB-n keresztül az Azure Active Directory-hitelesítés áttekintése](storage-files-active-directory-overview.md)
- [Gyakori kérdések](storage-files-faq.md)
