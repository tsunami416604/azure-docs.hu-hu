---
title: "Gyakori kérdések és ismert problémákat a felügyelt szolgáltatás Identity (MSI) az Azure Active Directory"
description: "Szolgáltatásidentitás felügyelete az Azure Active Directory szolgáltatással kapcsolatos ismert problémák."
services: active-directory
documentationcenter: 
author: BryanLa
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/15/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 447844d1779c537eb9e336a32575cb68ac9ad9eb
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="faq-and-known-issues-with-managed-service-identity-msi-for-azure-active-directory"></a>Gyakori kérdések és ismert problémákat a felügyelt szolgáltatás Identity (MSI) az Azure Active Directory

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="does-msi-work-with-azure-cloud-services"></a>MSI-fájl az Azure Cloud Services működik?

Nincsenek nem, nem támogatja az MSI-fájl az Azure Cloud Services tervezi.

### <a name="does-msi-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>MSI működik az Active Directory Authentication Library (ADAL) vagy a Microsoft hitelesítési könyvtár (MSAL)?

Nem, MSI nincs még integrálva az adal-t vagy MSAL. Az MSI REST-végpont használatával egy MSI-token beszerzése a részletekért lásd: [egy Azure virtuális gép felügyelt szolgáltatás Identity (MSI) használata a token beszerzési](msi-how-to-use-vm-msi-token.md).

### <a name="what-are-the-supported-linux-distributions"></a>Mik azok a támogatott Linux disztribúciókról?

A következő Linux terjesztésekről MSI támogatja: 

- CoreOS stabil
- 7.1 – centOS
- RedHat 7.2
- Ubuntu 15.04

Más Linux terjesztésekről jelenleg nem támogatottak, és a bővítményt a nem támogatott disztribúciók meghiúsulhat.

A bővítmény CentOS 6.9 működik. Azonban a 6.9 támogatását hiánya miatt a bővítmény fog nem automatikus újraindítás Ha összeomlott vagy leállt. Ha a virtuális gép újraindul újraindul. Indítsa újra manuálisan a kiterjesztés, lásd: [hogyan újra indítani az MSI-bővítmény?](#how-do-you-restart-the-msi-extension)

### <a name="how-do-you-restart-the-msi-extension"></a>Hogyan újraindította az MSI-bővítmény?
A Windows és Linux bizonyos verzióit a bővítmény leáll, a következő parancsmag használható manuális újraindítást:

```powershell
Set-AzureRmVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Az elemek magyarázata: 
- Bővítmény neve és a Windows típusa: ManagedIdentityExtensionForWindows
- Bővítmény neve és a Linux típusa van: ManagedIdentityExtensionForLinux

### <a name="are-there-rbac-roles-for-user-assigned-identities"></a>Vannak-e az RBAC-szerepkörök felhasználói hozzárendelt identitások?
igen:
1. MSI-közreműködői: 
- A következőket teheti: CRUD a felhasználói identitások. 
- Nem: A felhasználó identitása az erőforráshoz rendelt rendelhető hozzá. (azaz hozzárendelése az identitás a virtuális gépek)
2. MSI operátor: 
- Is: Hozzárendelése egy erőforrást egy hozzárendelt felhasználói azonosító. (azaz hozzárendelése az identitás a virtuális gépek)
- Nem lehet: CRUD a felhasználói identitások.

Megjegyzés: A beépített közreműködő szerepkört végezhetők el a fent leírt műveleteket: 
- A felhasználói identitások CRUD
- Rendelje hozzá a felhasználó identitása az erőforráshoz rendelt. (azaz hozzárendelése az identitás a virtuális gépek)


## <a name="known-issues"></a>Ismert problémák

### <a name="automation-script-fails-when-attempting-schema-export-for-msi-extension"></a>"Automatizálási parancsfájl" sikertelen sémaexportálásnál MSI-kiterjesztés megkísérlése során

Felügyelt Szolgáltatásidentitás engedélyezésekor a virtuális gép a következő jelenik meg a hiba használja a "Automatizálási parancsfájl" funkciót a virtuális Gépet, vagy az erőforráscsoport tett kísérlet során:

![MSI automatizálási parancsfájl exportálási hiba](~/articles/active-directory/media/msi-known-issues/automation-script-export-error.png)

A felügyelt szolgáltatás identitásának Virtuálisgép-bővítmény jelenleg nem támogatja a séma exportálhat egy erőforrás-sablon olyan. Ennek eredményeképpen a létrehozott sablon nem szerepelnek a felügyelt Szolgáltatásidentitás ahhoz, hogy az erőforrás-konfigurációs paraméterek. Ezek a szakaszok manuálisan következő szereplő példák hozzáadhatók [egy sablon használatával konfigurálja a VM felügyelt Szolgáltatásidentitás](msi-qs-configure-template-windows-vm.md).

Ha a séma exportálási funkció az MSI-Virtuálisgép-bővítmény elérhetővé válik, akkor megjelenik [exportálása erőforrás tartalmazó csoportok Virtuálisgép-bővítmények](~/articles/virtual-machines/windows/extensions-export-templates.md#supported-virtual-machine-extensions).

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>Konfigurációs panel nem jelenik meg az Azure-portálon

Ha a Virtuálisgép-konfiguráció panel nem jelenik meg a virtuális Gépet, majd MSI nem engedélyezett a portálon a saját régiójában még.  Próbálkozzon újra később.  MSI is engedélyezheti a virtuális gép a [PowerShell](msi-qs-configure-powershell-windows-vm.md) vagy a [Azure CLI](msi-qs-configure-cli-windows-vm.md).

### <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>Nem lehet hozzáférés hozzárendelése a hozzáférés-vezérlés (IAM) panelen lévő virtuális gépek

Ha **virtuális gép** nem jelenik meg az Azure portálon téve a **való hozzáférés hozzárendelése** a **hozzáférés-vezérlés (IAM)** > **hozzáadása engedélyek**, majd felügyelt Szolgáltatásidentitás nincs engedélyezve a portálon a saját régiójában még. Próbálkozzon újra később.  Továbbra is az MSI-szolgáltatás egyszerű kereséssel válassza ki a felügyelt Szolgáltatásidentitás a szerepkör-hozzárendelés.  Adja meg a virtuális gép nevét a **válasszon** mező, és a szolgáltatás egyszerű jelenik meg a keresési eredmény.

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>Virtuális gép nem erőforráscsoportba vagy előfizetésbe az áthelyezés után indul el

Ha a virtuális gép futó állapotban lévő helyezi át, akkor is fut tovább az áthelyezés során. Azonban az áthelyezés után a virtuális gép leállítása és újraindítása, ha nem tudja elindítani. A probléma akkor fordul elő, mert a virtuális gép nem frissíti az MSI-identitás mutató hivatkozást, és továbbra is a régi erőforráscsoportban mutat.

**Megkerülő megoldás** 
 
A virtuális gép egy frissítésének indítás, azt a MSI fogja tapasztalni a helyes az értékük. Egy Virtuálisgép-tulajdonság módosításának frissíteni a hivatkozást az MSI-identitás teheti meg. Például egy új címke adhatók meg a virtuális Gépet a következő paranccsal:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Ez a parancs a virtuális gép beállítása egy új címke "fixVM", 1 értékű. 
 
Ez a tulajdonság beállításával a megfelelő MSI-erőforrás URI frissíti a virtuális Gépet, és majd el a virtuális gép indítását. 
 
Miután a virtuális gép elindul, a címke távolíthatja el a következő parancsot:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="known-issues-with-user-assigned-msi-private-preview-feature"></a>Felhasználó hozzárendelése MSI kapcsolatos ismert problémák *(Private Preview szolgáltatás)*

- Távolítsa el az összes felhasználó lehet hozzárendelve MSIs csak úgy azáltal, hogy a rendszer tartozik MSI-fájl. 
- Egy virtuális géphez a Virtuálisgép-bővítmény telepítése sikertelen lehet DNS-keresési hibák miatt. Indítsa újra a virtuális Gépet, és próbálkozzon újra. 
- Az Azure CLI: `Az resource show` és `Az resource list` sikertelen lesz, a virtuális gép és egy felhasználó lehet hozzárendelve MSI-fájl. Megoldás használja`az vm/vmss show`
- Azure Storage útmutató jelenleg csak központi Velünk EUAP érhető el. 
- Ha egy felhasználó hozzárendelt MSI engedélyeznek egy erőforráshoz való hozzáférés, a IAM az adott erőforrás megjelenik "Nem fér hozzá adatokhoz." A probléma megoldásához az adott erőforrás szerepkör-hozzárendelések megtekintése/módosítása a parancssori felület használatával.
- MSI rendelve a nevében aláhúzás felhasználó létrehozása nem támogatott.
- A második felhasználó hozzáadása hozzárendelése az identitás, a clientID nem feltétlenül érhető el a kérelmek jogkivonatainak. A megoldás, mint indítsa újra a MSI Virtuálisgép-bővítmény, az alábbi két bash parancsokkal:
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler disable"`
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler enable"`
- A vmagent esetében a Windows jelenleg nem támogatja a felhasználó hozzárendelt MSI-fájl. 
- A szerepkör hozzárendelése egy olyan MSI Csomaghoz, elért egy erőforrást jelenleg nem szükséges különleges engedély. 
- Ha egy virtuális gép rendelkezik egy felhasználó lehet hozzárendelve MSI, de a rendszer nem rendelnek MSI, a portál felhasználói felületének fog engedélyezettként MSI-fájl. A rendszer MSI hozzárendelt engedélyezéséhez az Azure Resource Manager-sablon, egy Azure CLI vagy az SDK.
