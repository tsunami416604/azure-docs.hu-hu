---
title: Gyakori kérdések és ismert problémák a Felügyeltszolgáltatás-identitás (MSI) Azure Active Directory
description: Felügyeltszolgáltatás-identitás az Azure Active Directory szolgáltatással kapcsolatos ismert problémák.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.component: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: daveba
ms.openlocfilehash: 05096050dfc29aebd2859b298eef884dcd9a1111
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37908076"
---
# <a name="faqs-and-known-issues-with-managed-service-identity-msi-for-azure-active-directory"></a>Gyakori kérdések és ismert problémák a Felügyeltszolgáltatás-identitás (MSI) Azure Active Directory

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Gyakori kérdések (GYIK)

### <a name="does-msi-work-with-azure-cloud-services"></a>MSI működik az Azure Cloud Services?

Nem, nem is tervezzük MSI támogatásához az Azure Cloud Servicesben.

### <a name="does-msi-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>MSI működik az Active Directory Authentication Library (ADAL) vagy a Microsoft-hitelesítési tár (MSAL)?

Nem, MSI nem még integrált az adal-t vagy az MSAL. Az MSI-REST-végpont használatával egy MSI-token beszerzése a részletekért lásd: [egy Azure virtuális gépek Felügyeltszolgáltatás-identitás (MSI) használata a token beszerzéséhez](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-a-managed-service-identity"></a>A biztonsági határokat a Felügyeltszolgáltatás-identitást, mit jelent?

Az identitás a biztonsági határokat, hogy az erőforrás, amelyhez csatlakoztatva van. Ha például a biztonsági határ a virtuális gép MSI a virtuális gép. A virtuális gépen futó kód is képes az MSI-végpont meghívása és a jogkivonatok kérése. A hasonló más erőforrások, amelyek támogatják az MSI élményt.

### <a name="should-i-use-the-msi-vm-imds-endpoint-or-the-msi-vm-extension-endpoint"></a>A virtuális gép IMDS MSI-végpont vagy az MSI-Virtuálisgép-bővítmény végpont kell használnom?

MSI-alapú virtuális gépekhez használatakor javasoljuk az MSI IMDS végpont használatával. Az Azure Instance Metadata szolgáltatás REST-végpont elérhető-e az Azure Resource Manageren keresztül létrehozott IaaS virtuális gépekre. MSI-val IMDS előnyei a következők:

1. MSI használata az összes Azure IaaS támogatott operációs rendszerek IMDS keresztül. 
2. Már nem kell telepíteni egy bővítményt a virtuális Gépen az MSI engedélyezéséhez. 
3. MSI által használt tanúsítványok már nem találhatók a virtuális gépen. 
4. IMDS végpont egy jól ismert nem átirányítható IP-címet, csak érhetőek el a virtuális gép. 

Az MSI-Virtuálisgép-bővítmény továbbra is érhető el. már ma; használandó azonban előrefelé lesznek érvényben a IMDS végpont használatával. Az MSI-Virtuálisgép-bővítmény hamarosan megkezdődik az elavulással kapcsolatos csomagot. 

Az Azure-példány Metada Service további információkért lásd: [IMDS dokumentációja](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="what-are-the-supported-linux-distributions"></a>Mik azok a támogatott Linux-disztribúciók?

Az összes Azure IaaS által támogatott Linux-disztribúciókon a IMDS végponton keresztül MSI-vel is használható. 

Megjegyzés: A MSI Virtuálisgép-bővítmény a következő Linux-disztribúciók csak támogatja:
- CoreOS Stable
- CentOS 7.1
- Red Hat 7.2
- Ubuntu 15.04
- Ubuntu 16.04

Más Linux-disztribúció jelenleg nem támogatottak, és a bővítmény nem támogatott disztribúciókon meghiúsulhat.

A bővítmény a CentOS 6.9 működik. Azonban 6.9. a rendszer támogatás hiánya miatt a bővítmény fog nem az automatikus újraindítás Ha leállt, vagy leállt. Ha újraindítja a virtuális gép újraindul. Indítsa újra manuálisan a kiterjesztést, lásd: [hogyan újra indítani az MSI-bővítményt?](#how-do-you-restart-the-msi-extension)

### <a name="how-do-you-restart-the-msi-extension"></a>Hogyan újraindítja az MSI-bővítményt?
A Windows és Linux-bizonyos verziók Ha leáll a bővítményt, a következő parancsmag használható indítsa újra manuálisan:

```powershell
Set-AzureRmVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Az elemek magyarázata: 
- Bővítmény neve és típusa, a Windows: ManagedIdentityExtensionForWindows
- Bővítmény neve és a Linux-típus: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Ismert problémák

### <a name="automation-script-fails-when-attempting-schema-export-for-msi-extension"></a>"Automation-szkript" sikertelen lesz, amikor megpróbálja MSI-bővítmény export schématu.

Ha egy virtuális gépen engedélyezve van a Felügyeltszolgáltatás-identitást, használja az "Automation-szkript" funkciót a virtuális gép vagy a saját erőforráscsoportján megkísérlésekor a következő hiba jelenik meg:

![MSI-automatizálási parancsfájl exportálási hiba](../media/msi-known-issues/automation-script-export-error.png)

A Managed Service Identity Virtuálisgép-bővítmény jelenleg nem támogatja a séma egy erőforráscsoport sablonjának exportálása lehetővé teszi. Ennek eredményeképpen a létrejött sablon nem jeleníti meg a Felügyeltszolgáltatás-identitást ahhoz, hogy az erőforráson konfigurációs paramétereket. Ezek a szakaszok manuálisan is hozzáadhatók a következő szereplő példák [konfigurálása a virtuális gépek Felügyeltszolgáltatás-identitását egy sablon használatával](qs-configure-template-windows-vm.md).

Ha a séma exportálási funkció válik elérhetővé az MSI-Virtuálisgép-bővítmény, az lesz jelenik meg [exportálása előtt erőforráscsoportok, a Virtuálisgép-bővítmények tartalmazó](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>Konfigurációs panelen nem jelenik meg az Azure Portalon

Ha a virtuális gép konfigurációs panel nem látható a virtuális Gépen, majd MSI nincs engedélyezve a portálon az Ön régiójában még.  Próbálja újra később.  MSI is engedélyezheti a virtuális gép használatának [PowerShell](qs-configure-powershell-windows-vm.md) vagy a [Azure CLI-vel](qs-configure-cli-windows-vm.md).

### <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>A hozzáférés-vezérlés (IAM) paneljén a virtuális gépek nem hozzáférés hozzárendelése

Ha **virtuális gép** nem jelenik meg az Azure Portalon választást **rendelhet hozzáféréseket** a **hozzáférés-vezérlés (IAM)** > **hozzáadása engedélyek**, majd a Felügyeltszolgáltatás-identitás van még nincs engedélyezve a portálon az Ön régiójában. Próbálja újra később.  A Felügyeltszolgáltatás-identitást a szerepkör-hozzárendelés az MSI-szolgáltatás egyszerű kereséssel továbbra is választhat.  Adja meg a virtuális gép nevét a **kiválasztása** mező, és az egyszerű szolgáltatás megjelenik a keresési eredményekben.

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>Virtuális gép nem erőforráscsoportba vagy előfizetésbe az áthelyezés után indul el

Ha áthelyezi a virtuális gép futó állapotba kerül, akkor továbbra is az áthelyezés idejére. Azonban az áthelyezés után a virtuális gép leáll és újraindul, ha akkor fog tudni elindulni. Ez a hiba akkor fordul elő, mert a virtuális gép nem frissíti a hivatkozást az MSI-identitást, és továbbra is a régi erőforráscsoportban mutat.

**Megkerülő megoldás** 
 
Így helyes értékek azt is beolvasása az MSI-trigger egy frissítést a virtuális gépen. Egy Virtuálisgép-tulajdonság módosításának a hivatkozást az MSI-identitás frissítése teheti meg. Például megadhatja egy új címke értéke a virtuális gépen a következő paranccsal:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Ezzel a paranccsal egy új címke "fixVM"-1 érték beállítása a virtuális gép. 
 
Ez a tulajdonság beállításával frissíti a virtuális gép a megfelelő MSI-erőforrás URI-t, és majd kell látnia a virtuális gép elindításához. 
 
Miután a virtuális gép elindul, a címke távolíthatja el az alábbi parancs használatával:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="known-issues-with-user-assigned-identities"></a>Felhasználói hozzárendelt identitások ismert problémái

- Felhasználói identitás hozzárendelt hozzárendelések csak rendelkezésre áll-e a virtuális gép és a VMSS. Fontos: Felhasználói hozzárendelt identitás hozzárendelések módosítani fogja a hónapon belül.
- Ugyanazon ismétlődő felhasználói hozzárendelt identitások VM/VMSS miatt a VM/VMSS sikertelen lesz. Ez magában foglalja az identitások, a másik kis-és a hozzáadott. Példa: MyUserAssignedIdentity és myuserassignedidentity. 
- A Virtuálisgép-bővítményt a virtuális gép kiépítése a DNS-keresési hibák miatt meghiúsulhat. Indítsa újra a virtuális Gépet, és próbálkozzon újra. 
- Sikertelen a virtuális gép hozzáadása egy "nem létező" felhasználóhoz hozzárendelt identitás miatt. 
- Hozzárendelt identitás a speciális karakterek (például aláhúzásjelet) nevét a felhasználó létrehozása nem támogatott.
- A felhasználóhoz hozzárendelt identitás nevek korlátozva, végpontok közötti forgatókönyv 24 karakter hosszúságú lehet. A 24 karakternél hosszabb nevű felhasználói hozzárendelt identitások nem fogja tudni hozzárendelni.
- Felügyelt identitás virtuálisgép-bővítmény használata esetén a támogatott korlátot hozzárendelve a felügyelt identitásokból 32 felhasználói. Nem felügyelt identitás virtuálisgép-bővítmény a támogatott határértéke 512.  
- Amikor felveszi a második felhasználót hozzárendelt identitás, a clientID előfordulhat, hogy nem érhető el a kérések jogkivonatok a Virtuálisgép-bővítmény. Egy megoldás próbálja meg újraindítani a MSI Virtuálisgép-bővítmény, az alábbi két bash-parancsok:
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler disable"`
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler enable"`
- Ha egy virtuális Gépet egy felhasználóhoz hozzárendelt identitás, de nem a rendszerhez rendelt identitáshoz tartozik, a portál felhasználói felületén jelennek meg az MSI letiltottként. A rendszerhez rendelt identitáshoz engedélyezéséhez használja az Azure Resource Manager-sablonok, az Azure CLI-vel vagy egy SDK-t.
