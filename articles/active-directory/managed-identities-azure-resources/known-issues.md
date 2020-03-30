---
title: Gyakori kérdések és ismert problémák a felügyelt identitásokkal kapcsolatban - Azure AD
description: Ismert problémák az Azure-erőforrások felügyelt identitásaival.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0f0c678f2426d9de58d2ab337c56243394b4d0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266532"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Gyakori kérdések és ismert problémák az Azure-erőforrások felügyelt identitásaival kapcsolatban

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Gyakori kérdések (GYAKORI KÉRDÉSEK)

> [!NOTE]
> Az Azure-erőforrások felügyelt identitásai a Managed Service Identity (MSI) szolgáltatás új neve.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Az Azure-erőforrások felügyelt identitásai együttműködnek az Azure Cloud Services szolgáltatással?

Nem, nincsenek tervek az Azure-erőforrások felügyelt identitások az Azure Cloud Services támogatja.

### <a name="does-managed-identities-for-azure-resources-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Az Azure-erőforrások felügyelt identitásai együttműködnek az Active Directory hitelesítési tárral (ADAL) vagy a Microsoft authentication library (MSAL) könyvtárral?

Nem, az Azure-erőforrások felügyelt identitásai még nincsenek integrálva az ADAL vagy az MSAL szolgáltatással. A REST-végpont használatával az Azure-erőforrások felügyelt identitások jogkivonatának beszerzéséről a Felügyelt identitások használata az [Azure-erőforrásokhoz egy Azure virtuális gépen egy hozzáférési jogkivonat beszerzéséhez](how-to-use-vm-token.md)című témakörben talál további információt.

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Mi az Azure-erőforrások felügyelt identitásainak biztonsági határa?

Az identitás biztonsági határa az az erőforrás, amelyhez csatolva van. Például a biztonsági határ egy virtuális gép felügyelt identitások az Azure-erőforrások engedélyezve van, a virtuális gép. A virtuális gépen futó bármely kód meghívhat az Azure-erőforrások végpontjának felügyelt identitásait, és jogkivonatokat kérhet. Ez a hasonló tapasztalat más erőforrásokkal, amelyek támogatják az Azure-erőforrások felügyelt identitások.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Milyen identitást fog az IMDS alapértelmezett, ha nem adja meg az identitást a kérelemben?

- Ha a rendszer hez rendelt felügyelt identitás engedélyezve van, és nincs megadva identitás a kérelemben, az IMDS alapértelmezés szerint a rendszer hez rendelt felügyelt identitást fogja megadni.
- Ha a rendszerhez rendelt felügyelt identitás nincs engedélyezve, és csak egy felhasználóhoz rendelt felügyelt identitás létezik, az IMDS alapértelmezés szerint az adott felhasználó által hozzárendelt felügyelt identitásra kerül. 
- Ha a rendszer hez rendelt felügyelt identitás nincs engedélyezve, és több felhasználó által hozzárendelt felügyelt identitáslétezik, akkor a kérelemben meg kell adni a felügyelt identitást.

### <a name="should-i-use-the-managed-identities-for-azure-resources-imds-endpoint-or-the-vm-extension-endpoint"></a>Érdemes használni a felügyelt identitások az Azure-erőforrások IMDS-végpont vagy a virtuális gép bővítmény végpont?

Ha felügyelt identitásokat használ az Azure-erőforrások virtuális gépek, javasoljuk, hogy használja az IMDS-végpont. Az Azure Instance metaadat-szolgáltatás egy REST-végpont érhető el az Azure Resource Manager en keresztül létrehozott összes IaaS virtuális gépek. 

Az Azure-erőforrások felügyelt identitásainak imds-en keresztüli használatának néhány előnye a következő:
- Minden Azure IaaS által támogatott operációs rendszer használhatja az Azure-erőforrások felügyelt identitásait az IMDS-en keresztül.
- Már nem kell telepítenie egy bővítményt a virtuális gépen az Azure-erőforrások felügyelt identitások engedélyezéséhez. 
- Az Azure-erőforrások felügyelt identitások által használt tanúsítványok már nem szerepelnek a virtuális gépben.
- Az IMDS-végpont egy jól ismert, nem irányítható IP-cím, csak a virtuális gépen belül érhető el.
- 1000 felhasználó által hozzárendelt felügyelt identitások egyetlen virtuális géphez rendelhető. 

Az Azure-erőforrások virtuálisgép-bővítményének felügyelt identitásai továbbra is elérhetők; azonban már nem fejlesztünk rajta új funkciókat. Javasoljuk, hogy váltson az IMDS-végpont használatára. 

A virtuális gép bővítményvégponthasználatának néhány korlátozása a következő:
- Korlátozott támogatás Linux disztribúciókhoz: CoreOS Stable, CentOS 7.1, Red Hat 7.2, Ubuntu 15.04, Ubuntu 16.04
- Csak 32 felhasználó által hozzárendelt felügyelt identitások rendelhető a virtuális géphez.


Megjegyzés: Az Azure-erőforrások virtuálisgép-bővítmény felügyelt identitásai 2019 januárjában nem támogatottak. 

Az Azure Instance metaadatszolgáltatásáról az [IMDS dokumentációjában](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service) olvashat bővebben.

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>A felügyelt identitások automatikusan újra létrejönnek, ha áthelyezek egy előfizetést egy másik könyvtárba?

Nem. Ha áthelyez egy előfizetést egy másik könyvtárba, manuálisan kell létrehoznia őket, és újra meg kell adnia az Azure RBAC szerepkör-hozzárendeléseket.
- A rendszerhez rendelt felügyelt identitások esetén: tiltsa le és engedélyezze újra. 
- Felügyelt identitások esetén: törölje, hozza létre újra és csatolja őket újra a szükséges erőforrásokhoz (pl. virtuális gépekhez)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Használhatok felügyelt identitást egy másik címtárban/bérlőben lévő erőforrás eléréséhez?

Nem. A felügyelt identitások jelenleg nem támogatják a címtárközi forgatókönyveket. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Milyen Azure RBAC-engedélyek szükségesek egy erőforrás felügyelt identitásához? 

- Rendszerhez rendelt felügyelt identitás: Írási engedélyeket kell az erőforráshoz. Virtuális gépek esetében például a Microsoft.Compute/virtualMachines/write engedélyre van szükség. Ez a művelet szerepel az erőforrás-specifikus beépített szerepkörökben, például a [Virtuálisgép-közreműködőben.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)
- Felhasználó által hozzárendelt felügyelt identitás: Írási engedélyeket kell az erőforráshoz. Virtuális gépek esetében például a Microsoft.Compute/virtualMachines/write engedélyre van szükség. A [felügyelt identitáskezelő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör-hozzárendelés mellett a felügyelt identitás on kívül.

### <a name="how-do-you-restart-the-managed-identities-for-azure-resources-extension"></a>Hogyan lehet újraindítani az Azure-erőforrások bővítmény felügyelt identitások?
Windows és a Linux bizonyos verzióiban, ha a kiterjesztés leáll, a következő parancsmag használható manuálisan újraindítani:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Az elemek magyarázata: 
- A Windows bővítményneve és típusa: ManagedIdentityExtensionForWindows
- A Linux kiterjesztés neve és típusa: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Ismert problémák

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Az "Automation script" sikertelen, amikor az Azure-erőforrások bővítmény felügyelt identitások rendszerének exportálását kíséreli meg

Ha az Azure-erőforrások felügyelt identitásai engedélyezve vannak egy virtuális gépen, a következő hiba jelenik meg, amikor megpróbálja használni az "Automation script" funkciót a virtuális gép, vagy annak erőforrás-csoport:

![Felügyelt identitások az Azure-erőforrások automatizálási parancsfájljának exportálási hibájához](./media/msi-known-issues/automation-script-export-error.png)

Az Azure-erőforrások virtuálisgép-bővítményének felügyelt identitásai (2019 januárjában evevésre tervezve) jelenleg nem támogatják a séma erőforráscsoport-sablonba történő exportálásának lehetőségét. Ennek eredményeképpen a létrehozott sablon nem jeleníti meg a konfigurációs paramétereket az Azure-erőforrások felügyelt identitások engedélyezéséhez az erőforráson. Ezek a szakaszok manuálisan is hozzáadhatók, ha követik az [Azure-erőforrások felügyelt identitásának konfigurálása egy Azure-beli virtuális gépen egy sablon használatával című példákat.](qs-configure-template-windows-vm.md)

Amikor a séma exportálási funkció elérhetővé válik az Azure-erőforrások virtuálisgép-bővítményének felügyelt identitásai számára (2019 januárjában tervezi az evevést), akkor a [virtuálisgép-bővítményeket tartalmazó Erőforráscsoportok exportálása](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions)listában jelenik meg.

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>A virtuális gép nem indul el, miután áthelyezték az erőforráscsoportból vagy az előfizetésből

Ha egy virtuális gép a futó állapotban, továbbra is fut az áthelyezés során. Azonban az áthelyezés után, ha a virtuális gép leáll, és újraindul, nem fog elindulni. Ez a probléma azért fordul elő, mert a virtuális gép nem frissíti az Azure-erőforrások identitásának felügyelt identitására mutató hivatkozást, és továbbra is a régi erőforráscsoportban mutat rá.

**Workaround** 
 
A virtuális gép frissítése, így az Azure-erőforrások felügyelt identitások megfelelő értékeket kaphat. A virtuális gép tulajdonságának módosításával frissítheti az Azure-erőforrások identitásának felügyelt identitására mutató hivatkozást. Például a következő paranccsal új címkeértéket állíthat be a virtuális gépen:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Ez a parancs egy új "fixVM" címkét állít be, amelynek értéke 1 a virtuális gépen. 
 
A tulajdonság beállításával a virtuális gép frissíti a megfelelő felügyelt identitások az Azure-erőforrások erőforrás URI-hoz, és akkor képesnek kell lennie a virtuális gép elindításához. 
 
A virtuális gép indítása után a címke a következő paranccsal eltávolítható:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="vm-extension-provisioning-fails"></a>A virtuális gép bővítmény-kiépítéssikertelen

A virtuálisgép-bővítmény kiépítése DNS-keresési hibák miatt sikertelen lehet. Indítsa újra a virtuális gép, és próbálkozzon újra.
 
> [!NOTE]
> A virtuális gép bővítménye a tervek szerint 2019 januárjáig evevődik. Azt javasoljuk, hogy az IMDS-végpont használatával.

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Előfizetés átvitele az Azure AD-könyvtárak között

A felügyelt identitások nem frissülnek, amikor egy előfizetést áthelyeznek/áthelyeznek egy másik könyvtárba. Ennek eredményeképpen minden létező rendszer-hozzárendelt vagy felhasználó által hozzárendelt felügyelt identitások megszakadnak. 

Megoldás egy másik könyvtárba áthelyezett előfizetés felügyelt identitásaihoz:

 - A rendszerhez rendelt felügyelt identitások esetén: tiltsa le és engedélyezze újra. 
 - Felügyelt identitások esetén: törölje, hozza létre újra és csatolja őket újra a szükséges erőforrásokhoz (pl. virtuális gépekhez)

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Felhasználó által hozzárendelt felügyelt identitás áthelyezése másik erőforráscsoportba/előfizetésbe

Ha egy felhasználó által hozzárendelt felügyelt identitást áthelyez egy másik erőforráscsoportba, az identitás megszakad. Ennek eredményeképpen az identitást használó erőforrások (például virtuális gép) nem tudnak jogkivonatokat kérni. 
