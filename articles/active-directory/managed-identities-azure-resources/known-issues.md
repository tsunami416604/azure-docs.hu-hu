---
title: A felügyelt identitásokkal kapcsolatos gyakori kérdések és ismert problémák – Azure AD
description: Ismert problémák az Azure-erőforrások felügyelt identitásával kapcsolatban.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266532"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Az Azure-erőforrások felügyelt identitásával kapcsolatos gyakori kérdések és ismert problémák

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Gyakori kérdések (GYIK)

> [!NOTE]
> Az Azure-erőforrások felügyelt identitásai a Managed Service Identity (MSI) szolgáltatás új neve.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Az Azure-erőforrások felügyelt identitásai működnek az Azure Cloud Services?

Nem, az Azure-Cloud Services nem támogatja az Azure-erőforrások felügyelt identitásának támogatását.

### <a name="does-managed-identities-for-azure-resources-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Az Azure-erőforrások felügyelt identitásai működnek a Active Directory-hitelesítési tár (ADAL) vagy a Microsoft Authentication Library (MSAL) szolgáltatással?

Nem, az Azure-erőforrások felügyelt identitásai még nincsenek integrálva a ADAL vagy a MSAL. A REST-végponttal rendelkező Azure-erőforrások felügyelt identitásokhoz való beszerzésével kapcsolatos részletekért lásd: [Hogyan lehet felügyelt identitásokat használni az Azure-beli virtuális gépeken egy hozzáférési jogkivonat beszerzéséhez](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Mi a felügyelt identitások biztonsági határa az Azure-erőforrásokhoz?

Az identitás biztonsági határa az az erőforrás, amelyhez hozzá van rendelve. Az Azure-erőforrások felügyelt identitással rendelkező virtuális gépek biztonsági határa például a virtuális gép. Az adott virtuális gépen futó kódok meghívhatják az Azure-erőforrások végpontjának felügyelt identitásait, és jogkivonatokat kérhetnek. Ez a hasonló élmény az Azure-erőforrások felügyelt identitásait támogató egyéb erőforrásokkal.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Milyen identitást IMDS az alapértelmezett érték, ha nem ad meg identitást a kérelemben?

- Ha a rendszerhez rendelt felügyelt identitás engedélyezve van, és nincs megadva identitás a kérelemben, a IMDS alapértelmezés szerint a rendszerhez rendelt felügyelt identitást fogja használni.
- Ha a rendszerhez rendelt felügyelt identitás nincs engedélyezve, és csak egy felhasználóhoz rendelt felügyelt identitás létezik, a IMDS alapértelmezés szerint az adott felhasználóhoz rendelt felügyelt identitást fogja használni. 
- Ha a rendszerhez rendelt felügyelt identitás nincs engedélyezve, és több felhasználó által hozzárendelt felügyelt identitás létezik, akkor a kérésben a felügyelt identitás megadása szükséges.

### <a name="should-i-use-the-managed-identities-for-azure-resources-imds-endpoint-or-the-vm-extension-endpoint"></a>Az Azure Resources IMDS-végpont vagy a virtuálisgép-bővítmény végpontjának felügyelt identitásait kell használnia?

Ha virtuális gépekkel felügyelt identitásokat használ az Azure-erőforrásokhoz, javasoljuk, hogy használja a IMDS-végpontot. Az Azure Instance Metadata Service egy REST-végpont, amely a Azure Resource Manager használatával létrehozott összes IaaS virtuális gép számára elérhető. 

Az Azure-erőforrások felügyelt identitások használatának néhány előnye az IMDS-on keresztül:
- Az Azure IaaS által támogatott operációs rendszerek felügyelt identitásokat használhatnak az Azure-erőforrásokhoz a IMDS-en keresztül.
- Már nem kell telepítenie egy bővítményt a virtuális gépre az Azure-erőforrások felügyelt identitásának engedélyezéséhez. 
- Az Azure-erőforrásokhoz tartozó felügyelt identitások által használt tanúsítványok már nem jelennek meg a virtuális gépen.
- Az IMDS-végpont egy jól ismert, nem irányítható IP-cím, csak a virtuális gépen belülről érhető el.
- 1000 felhasználó által hozzárendelt felügyelt identitások hozzárendelhetők egyetlen virtuális géphez. 

Az Azure-erőforrások virtuálisgép-bővítményének felügyelt identitásai továbbra is elérhetők; azonban már nem fejlesztünk új funkciókat. Javasoljuk, hogy váltson át az IMDS-végpont használatára. 

A virtuálisgép-bővítmény végpontjának használatának egyes korlátai a következők:
- Korlátozott támogatás a Linux-disztribúciók számára: CoreOS stabil, CentOS 7,1, Red Hat 7,2, Ubuntu 15,04, Ubuntu 16,04
- Csak 32 felhasználó által hozzárendelt felügyelt identitás rendelhető hozzá a virtuális géphez.


Megjegyzés: az Azure-erőforrások virtuálisgép-bővítményének felügyelt identitásai a 2019 januárjában lesznek támogatottak. 

Az Azure Instance Metadata Service-ról további információt a [IMDS dokumentációjában](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service) talál.

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>A felügyelt identitások automatikusan újra létrejönnek, ha egy előfizetést másik könyvtárba helyezek át?

Nem. Ha egy előfizetést másik könyvtárba helyez át, akkor manuálisan újra létre kell hoznia őket, és újra meg kell adnia az Azure RBAC szerepkör-hozzárendeléseket.
- A rendszerhez rendelt felügyelt identitások esetében: letiltás és újbóli engedélyezés. 
- A felhasználóhoz rendelt felügyelt identitások esetében: törölje, hozza létre újra, majd csatolja újra a szükséges erőforrásokhoz (például virtuális gépekhez)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Használhatok egy felügyelt identitást egy másik címtárban/bérlőben lévő erőforrás eléréséhez?

Nem. A felügyelt identitások jelenleg nem támogatják a könyvtárak közötti forgatókönyveket. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Milyen Azure RBAC-engedélyekre van szükség egy erőforrás felügyelt identitásához? 

- Rendszer által hozzárendelt felügyelt identitás: írási engedélyre van szüksége az erőforráson. Virtuális gépek esetében például a Microsoft.Compute/virtualMachines/write engedélyre van szükség. Ez a művelet az erőforrás-specifikus beépített szerepkörök, például a [virtuális gépek közreműködője](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)része.
- Felhasználó által hozzárendelt felügyelt identitás: írási engedélyre van szüksége az erőforráson. Virtuális gépek esetében például a Microsoft.Compute/virtualMachines/write engedélyre van szükség. A felügyelt identitás- [kezelő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör-hozzárendelésen felül a felügyelt identitáson kívül is.

### <a name="how-do-you-restart-the-managed-identities-for-azure-resources-extension"></a>Hogyan indítja újra az Azure-erőforrások bővítmény felügyelt identitásait?
Windows rendszeren és a Linux egyes verzióiban, ha a bővítmény leáll, a következő parancsmag használható a manuális újraindításhoz:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Az elemek magyarázata: 
- A Windows-bővítmény neve és típusa: ManagedIdentityExtensionForWindows
- Kiterjesztés neve és típusa Linux esetén: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Ismert problémák

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Az "Automation-parancsfájl" sikertelen, amikor a séma exportálását kísérli meg az Azure-erőforrások bővítmény felügyelt identitásai számára

Ha az Azure-erőforrások felügyelt identitása engedélyezve van egy virtuális gépen, a következő hibaüzenet jelenik meg, amikor a virtuális gép "Automation-parancsfájl" funkcióját vagy az erőforráscsoport használatát kísérli meg:

![Felügyelt identitások az Azure-erőforrások automatizálási parancsfájljának exportálási hibája](./media/msi-known-issues/automation-script-export-error.png)

Az Azure Resources virtuálisgép-bővítmény felügyelt identitásai (2019 januárjában) jelenleg nem támogatják a sémák erőforráscsoport-sablonba való exportálásának lehetőségét. Ennek eredményeképpen a generált sablon nem jeleníti meg a konfigurációs paramétereket az Azure-erőforrások felügyelt identitásának engedélyezéséhez az erőforráson. Ezeket a részeket manuálisan is hozzáadhatja az Azure-beli virtuális gépeken található [felügyelt identitások konfigurálása](qs-configure-template-windows-vm.md)az Azure-beli virtuális gépeken sablonok használatával című részben ismertetett példákkal.

Ha a séma-exportálási funkció elérhetővé válik a felügyelt identitások számára az Azure-erőforrások virtuálisgép-bővítmény számára (amely a 2019 januárjában van tervezve), akkor a rendszer a virtuálisgép- [bővítményeket tartalmazó erőforráscsoportok exportálását fogja tartalmazni](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>A virtuális gép nem indul el, miután áthelyezték az erőforráscsoportot vagy az előfizetést

Ha a virtuális gépet futó állapotba helyezi, az áthelyezés során továbbra is futni fog. Az áthelyezést követően azonban, ha a virtuális gép leáll és újraindul, nem fog elindulni. Ez a probléma azért fordul elő, mert a virtuális gép nem frissíti az Azure-erőforrások identitásához tartozó felügyelt identitásokra mutató hivatkozást, és továbbra is a régi erőforráscsoporthoz mutat.

**Áthidaló megoldás** 
 
Indítson el egy frissítést a virtuális gépen, hogy helyes értékeket kapjon az Azure-erőforrások felügyelt identitásai számára. A virtuális gép tulajdonságainak módosításával frissítheti az Azure-erőforrások identitásához tartozó felügyelt identitásokra mutató hivatkozást. Beállíthat például egy új címke értéket a virtuális gépen a következő paranccsal:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Ez a parancs egy "fixVM" nevű új címkét állít be a virtuális gépen 1 értékkel. 
 
Ennek a tulajdonságnak a beállításával a virtuális gép a megfelelő felügyelt identitásokkal frissíti az Azure-erőforrások erőforrás URI azonosítóját, majd el kell tudnia indítani a virtuális gépet. 
 
A virtuális gép elindítása után a címkét a következő parancs használatával távolíthatja el:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="vm-extension-provisioning-fails"></a>A virtuálisgép-bővítmény kiépítés sikertelen

A virtuálisgép-bővítmény üzembe helyezése sikertelen lehet a DNS-keresési hibák miatt. Indítsa újra a virtuális gépet, és próbálkozzon újra.
 
> [!NOTE]
> A virtuálisgép-bővítményt a rendszer a 2019 januári elavulás céljából tervezi. Javasoljuk, hogy térjen át a IMDS-végpont használatára.

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Előfizetés továbbítása az Azure AD-címtárak között

A felügyelt identitások nem frissülnek, ha egy előfizetést áthelyeznek/átvisznek egy másik könyvtárba. Ennek eredményeképpen minden létező, rendszer által hozzárendelt vagy felhasználó által hozzárendelt felügyelt identitás megszakad. 

Megkerülő megoldás a felügyelt identitások számára egy másik könyvtárba áthelyezett előfizetésben:

 - A rendszerhez rendelt felügyelt identitások esetében: letiltás és újbóli engedélyezés. 
 - A felhasználóhoz rendelt felügyelt identitások esetében: törölje, hozza létre újra, majd csatolja újra a szükséges erőforrásokhoz (például virtuális gépekhez)

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Felhasználó által hozzárendelt felügyelt identitás áthelyezése egy másik erőforráscsoporthoz vagy előfizetésbe

Ha egy felhasználóhoz rendelt felügyelt identitást másik erőforráscsoporthoz helyez át, a rendszer az identitást megtöri. Ennek eredményeképpen az adott identitást használó erőforrások (például virtuális gépek) nem igényelhetnek jogkivonatokat. 
