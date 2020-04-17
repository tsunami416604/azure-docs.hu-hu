---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/11/2019
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: e40171b95e6faae0020f8bf61410aad8999ddecb
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536529"
---
## <a name="2-assign-access-permissions-to-an-identity"></a>2. Hozzáférési engedélyek hozzárendelése identitáshoz

Az Azure Files-erőforrások identitásalapú hitelesítéssel való eléréséhez egy identitásnak (egy felhasználónak, csoportnak vagy egyszerű szolgáltatásnak) rendelkeznie kell a megosztási szinten szükséges engedélyekkel. Ez a folyamat hasonló a Windows megosztási engedélyek megadásához, ahol megadhatja, hogy egy adott felhasználó milyen típusú hozzáféréssel rendelkezik egy fájlmegosztáshoz. Ebben a szakaszban található útmutató bemutatja, hogyan rendelhet olvasási, írási vagy törlési engedélyeket egy fájlmegosztáshoz egy identitáshoz. 

Három Beépített Azure-szerepkört vezettünk be a felhasználók megosztásszintű engedélyeinek megadására:

- **Storage File Data SMB Share Reader** lehetővé teszi az olvasási hozzáférést az Azure Storage fájlmegosztások SMB-n keresztül.
- **Storage File Data SMB Share Contributor** lehetővé teszi az olvasást, írást és törlést az Azure Storage fájlmegosztások SMB-n keresztül.
- **Storage File Data SMB Share Emelt szintű közreműködő** lehetővé teszi az NTFS-engedélyek olvasását, írását, törlését és módosítását az Azure Storage fájlmegosztások SMB-n keresztül.

> [!IMPORTANT]
> A fájlmegosztás teljes felügyeleti vezérlése, beleértve a fájl tulajdonjogának átvételét, a tárfiók kulcsának használatát igényli. Felügyeleti vezérlés nem támogatott az Azure AD hitelesítő adatok.

Az Azure Portalon, a PowerShellben vagy az Azure CLI-ben a beépített szerepkörök hozzárendelése a felhasználó Azure AD identitása a megosztásszintű engedélyek megadásához.

> [!NOTE]
> Ne [felejtse el szinkronizálni az AD DS hitelesítő adatait az Azure AD-vel,](../articles/active-directory/hybrid/how-to-connect-install-roadmap.md) ha a helyszíni AD DS-t kívánja használni a hitelesítéshez. Az AD DS és az Azure AD jelszó-kivonatszinkronizálása nem kötelező. Megosztási szintű engedélyt kap az Azure AD-identitás, amely szinkronizálva van a helyszíni AD DS.

Az általános javaslat az, hogy használja a megosztási szintű engedély a magas szintű hozzáférés-kezelés egy AD csoport képviselő felhasználók és identitások egy csoportja, majd használja NTFS engedélyek részletes hozzáférés-vezérlés a címtár/fájl szinten. 

#### <a name="azure-portal"></a>Azure Portal
Ha RBAC-szerepkört szeretne hozzárendelni egy Azure AD-identitáshoz az [Azure Portal](https://portal.azure.com)használatával, kövesse az alábbi lépéseket:

1. Az Azure Portalon nyissa meg a fájlmegosztást, vagy [hozzon létre egy fájlmegosztást.](../articles/storage/files/storage-how-to-create-file-share.md)
2. Válassza **a Hozzáférés-vezérlés (IAM) lehetőséget.**
3. **Szerepkör-hozzárendelés hozzáadása kijelölése**
4. A **Szerepkör-hozzárendelés hozzáadása** panelen válassza ki a megfelelő beépített szerepkört (Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor) a **Szerepkör** listából. Hagyja **a hozzáférés hozzárendelése az** alapértelmezett beállítás: Azure **AD felhasználó, csoport vagy egyszerű szolgáltatás.** Válassza ki a cél Azure AD-identitás név vagy e-mail cím alapján.
5. A szerepkör-hozzárendelési művelet végrehajtásához válassza a **Mentés** lehetőséget.

#### <a name="powershell"></a>PowerShell

A következő PowerShell-minta bemutatja, hogyan rendelhet rBAC-szerepkört egy Azure AD-identitáshoz bejelentkezési név alapján. Az RBAC-szerepkörök PowerShellhez való hozzárendeléséről az [RBAC és az Azure PowerShell használatával idoben](../articles/role-based-access-control/role-assignments-powershell.md)című témakörben talál további információt.

A következő mintaparancsfájl futtatása előtt ne felejtse el lecserélni a helyőrző értékeket, beleértve a szögletes zárójeleket is, a saját értékeivel.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>parancssori felület
  
A következő CLI 2.0 parancs bemutatja, hogyan rendelhet rBAC-szerepkört egy Azure AD-identitáshoz bejelentkezési név alapján. Az RBAC-szerepkörök Azure CLI-vel való hozzárendeléséről az [Access kezelése az RBAC és az Azure CLI használatával című](../articles/role-based-access-control/role-assignments-cli.md)témakörben talál további információt. 

A következő mintaparancsfájl futtatása előtt ne felejtse el lecserélni a helyőrző értékeket, beleértve a szögletes zárójeleket is, a saját értékeivel.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="3-configure-ntfs-permissions-over-smb"></a>3. NTFS-engedélyek konfigurálása SMB-n keresztül 
Miután megosztott szintű engedélyeket rendelt az RBAC-hoz, megfelelő NTFS-engedélyeket kell hozzárendelnie a gyökér-, könyvtár- vagy fájlszinten. Gondoljon a megosztásszintű engedélyekre, mint a magas szintű forgalomirányítóra, amely meghatározza, hogy a felhasználó hozzáférhet-e a megosztáshoz. Mivel az NTFS-engedélyek részletesebb szinten működnek annak meghatározásához, hogy a felhasználó milyen műveleteket tehet a címtár vagy a fájl szintjén.

Az Azure Files támogatja az NTFS alap- és speciális engedélyeiteljes készletét. Az Azure-fájlmegosztásban lévő könyvtárakra és fájlokra vonatkozó NTFS-engedélyek et a megosztás csatlakoztatásával, majd a Windows Fájlkezelő használatával, illetve a Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) vagy [set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-acl) parancs futtatásával tekintheti meg és konfigurálhatja. 

Az NTFS rendszergazdai engedélyekkel történő konfigurálásához csatlakoztatnia kell a megosztást a tartományhoz csatlakoztatott virtuális gép tárfiókkulcsával. Kövesse a következő szakaszban található utasításokat az Azure-fájlmegosztás csatlakoztatásához a parancssorból, és ennek megfelelően konfigurálja az NTFS-engedélyeket.

A következő engedélykészletek támogatottak a fájlmegosztás gyökérkönyvtárában:

- BUILTIN\Rendszergazdák:(OI)(CI)(F)
- NT HATÓSÁG\SYSTEM:(OI)(CI)(F)
- BUILTIN\Felhasználók:(RX)
- BUILTIN\Felhasználók:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Hitelesített felhasználók:(OI)(CI)(M)
- NT HATÓSÁG\RENDSZER:(F)
- LÉTREHOZÓ TULAJDONOSA:(OI)(CI)(IO)(F)

### <a name="configure-ntfs-permissions-with-icacls"></a>NTFS-engedélyek konfigurálása icacls-szel
A következő Windows-paranccsal teljes körű engedélyeket adhat a fájlmegosztás alatt lévő összes könyvtárnak és fájlnak, beleértve a gyökérkönyvtárat is. Ne felejtse el lecserélni a példában lévő helyőrző értékeket a saját értékeire.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Az NTFS-engedélyek beállításához és a támogatott engedélyek különböző típusainak beállításához szükséges icacls használatáról a [icacls parancssori hivatkozásában](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)talál további információt.

### <a name="mount-a-file-share-from-the-command-prompt"></a>Fájlmegosztás csatlakoztatása a parancssorból

Az **Azure-fájlmegosztás** csatlakoztatása a Windows net use paranccsal csatlakoztathatja. Ne felejtse el lecserélni a helyőrző értékeket a következő példában a saját értékeire. A fájlmegosztások csatlakoztatásáról az [Azure-fájlmegosztás használata a Windows rendszerrel](../articles/storage/files/storage-how-to-use-files-windows.md)című témakörben talál további információt. 

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>NTFS-engedélyek konfigurálása a Windows Fájlkezelővel
A Windows Fájlkezelővel teljes körű engedélyt adhat a fájlmegosztás alatt lévő összes könyvtárnak és fájlnak, beleértve a gyökérkönyvtárat is.

1. Nyissa meg a Windows Fájlkezelőt, és kattintson a jobb gombbal a fájlra/könyvtárra, és válassza a **Tulajdonságok parancsot.**
2. Válassza a **Biztonság** lapot.
3. Válassza **a Szerkesztés lehetőséget.** az engedélyek módosításához.
4. Módosíthatja a meglévő felhasználók engedélyeit, vagy a **Hozzáadás...** lehetőséget választva engedélyeket adhat az új felhasználóknak.
5. Az új felhasználók hozzáadásának kérdésablakában írja be azt a célfelhasználónevet, amelynek engedélyt szeretne adni a **kijelölendő objektumnevek megadása** mezőbe, és a **Nevek ellenőrzése** lehetőséget választva keresse meg a megcélzott felhasználó teljes UPN-nevét.
7.    Válassza **az OK gombot.**
8.    A **Biztonság** lapon jelölje ki az új felhasználónak adni kívánt összes engedélyt.
9.    Kattintson az **Alkalmaz** gombra.

## <a name="4-mount-a-file-share-from-a-domain-joined-vm"></a>4. Fájlmegosztás csatlakoztatása tartományhoz csatlakoztatott virtuális gépről

A következő folyamat ellenőrzi, hogy a fájlmegosztási és hozzáférési engedélyek megfelelően vannak-e beállítva, és hogy egy tartományhoz csatlakozó virtuális gépről hozzáférhet-e az Azure-fájlmegosztáshoz. Ne feledje, hogy a megosztási szintű RBAC szerepkör-hozzárendelés eltarthat egy ideig, hogy a hatályos. 

Jelentkezzen be a virtuális gépaz Azure AD-identitás használatával, amelyhez engedélyeket adott, ahogy az alábbi képen látható. Ha engedélyezte a helyszíni AD DS-hitelesítést az Azure Files számára, használja az AD DS hitelesítő adatait. Az Azure AD DS-hitelesítéshez jelentkezzen be az Azure AD hitelesítő adataival.

![Az Azure AD bejelentkezési képernyőjét bemutató képernyő a felhasználói hitelesítéshez](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Az Azure-fájlmegosztás csatlakoztatása a következő paranccsal csatlakoztathatja az Azure-fájlmegosztást. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire. Mivel már hitelesített, nem kell megadnia a tárfiók kulcs, a helyszíni AD DS hitelesítő adatok, vagy az Azure AD DS hitelesítő adatait. Egyszeri bejelentkezési élmény a helyszíni AD DS vagy az Azure AD DS hitelesítése támogatott. Ha problémákba ütközik az Active ád ds hitelesítő adatok csatlakoztatása során, útmutatásért olvassa el az [Azure Files problémák elhárítása](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) a Windows rendszerben című témakört.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```
