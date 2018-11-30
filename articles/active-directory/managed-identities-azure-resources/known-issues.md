---
title: Gyakori kérdések és ismert problémái az Azure-erőforrások felügyelt identitásokból
description: Felügyelt identitások az Azure-erőforrások ismert problémái.
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
ms.openlocfilehash: 256f36ac56126fc76561a6dbe4281ac4975df6e4
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52632789"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Gyakori kérdések és ismert problémái az Azure-erőforrások felügyelt identitásokból

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Gyakori kérdések (GYIK)

> [!NOTE]
> Az Azure-erőforrások felügyelt identitásai a Managed Service Identity (MSI) szolgáltatás új neve.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Az Azure-erőforrások felügyelt identitások működik az Azure Cloud Services?

Nem, nem is tervezzük felügyelt identitások támogatásához az Azure-erőforrásokhoz az Azure Cloud Servicesben.

### <a name="does-managed-identities-for-azure-resources-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Felügyelt identitások Azure-erőforrások számára az Active Directory Authentication Library (ADAL) vagy a Microsoft-hitelesítési tár (MSAL) működik?

Nem, felügyelt identitások, az Azure-erőforrások még nem integrált az adal-t vagy az MSAL. További információ az Azure-erőforrások a REST-végpont használatával felügyelt identitások tokenbeolvasás: [felügyelt identitások használata az Azure-erőforrások egy Azure-beli virtuális gépen a hozzáférési jogkivonat beszerzése ](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Mi az Azure-erőforrások felügyelt identitások biztonsági határ?

Az identitás a biztonsági határokat, hogy az erőforrás, amelyhez csatlakoztatva van. A biztonsági határt, a virtuális gép felügyelt identitások Azure-erőforrások számára például engedélyezve van, a virtuális gép. A virtuális gépen futó kód is képes végpont és a kérés tokenek hívja a felügyelt identitások az Azure-erőforrásokhoz. A hasonló élményt más erőforrások, amelyek támogatják a felügyelt identitások az Azure-erőforrásokhoz.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Milyen identitás lesz IMDS alapértelmezett, ha nem adja meg a identitását a kérés?

- Ha engedélyezve van a rendszerhez rendelt identitáshoz felügyelt és nem identitás van megadva a kérelemben, IMDS alapértelmezés szerint a rendszer hozzárendelt felügyelt identitás.
- Ha a rendszer által hozzárendelt felügyelt identitás nem érhető el, és csak egyetlen felhasználóhoz hozzárendelt felügyelt identitás létezik, IMDS, hogy egyetlen felhasználóhoz hozzárendelt felügyelt identitás alapértelmezés szerint. 
- Ha nincs engedélyezve a rendszerhez rendelt identitáshoz felügyelt, és több felhasználó által a felügyelt identitásokból hozzárendelt létezik, majd megadja a kérelem egy felügyelt identitás szükség.

### <a name="should-i-use-the-managed-identities-for-azure-resources-vm-imds-endpoint-or-the-vm-extension-endpoint"></a>Az Azure-erőforrások virtuális gép IMDS endpoint vagy a Virtuálisgép-bővítmény végpont kell használnom a felügyelt identitásokból?

Ha felügyelt identitások használatával az Azure-erőforrások virtuális gépekkel, javasoljuk a felügyelt identitások használatával Azure-erőforrások IMDS végpont. Az Azure Instance Metadata szolgáltatás REST-végpont elérhető-e az Azure Resource Manageren keresztül létrehozott IaaS virtuális gépekre. Felügyelt identitások használatával az Azure-erőforrások keresztül IMDS előnyei a következők:
    - Az összes támogatott Azure IaaS operációs rendszer IMDS keresztül felügyelt identitások használható Azure-erőforrások.
    - Már nem kell telepítenie egy bővítmény engedélyezése a felügyelt identitások az Azure-erőforrások a virtuális gépen. 
    - Az Azure-erőforrások felügyelt identitások által használt tanúsítványok már nem találhatók a virtuális gépen.
    - IMDS végpont egy jól ismert nem átirányítható IP-címet, csak érhetőek el a virtuális gép.

A felügyelt identitásokból Virtuálisgép-bővítmény továbbra is érhető el még ma; használható az Azure-erőforrások azonban előrefelé lesznek érvényben a IMDS végpont használatával. A felügyelt identitások Azure-erőforrások Virtuálisgép-bővítmény a január 2019 elavulttá válik. 

Az Azure Instance Metadata szolgáltatás további információkért lásd: [IMDS dokumentációja](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Felügyelt identitások létrejönnek, automatikusan, ha egy előfizetés áthelyezem egy másik címtárral?

Nem. Ha egy előfizetést másik könyvtárba helyezi át, akkor manuálisan hozza létre őket ismét, és biztosítson számára újra Azure RBAC szerepkör-hozzárendeléseket.
    - A rendszer adott felügyelt identitások: letiltását és újraengedélyezését.
    - Felhasználói rendelt felügyelt identitások: törölje, hozza létre újból, és újra csatolja őket a szükséges erőforrásokat (pl. virtuális gépek)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Egy felügyelt identitás használata egy másik címtár bérlőben lévő erőforrások eléréséhez?

Nem. Felügyelt identitások jelenleg nem támogatja cross-directory-forgatókönyvet. 

### <a name="what-are-the-supported-linux-distributions"></a>Mik azok a támogatott Linux-disztribúciók?

Felügyelt identitások az Azure-erőforrásokhoz a IMDS végponton keresztül az összes Azure IaaS által támogatott Linux-disztribúciók is használható. 

A felügyelt identitások az Azure-erőforrások (elavult. január 2019 a tervezett) Virtuálisgép-bővítmény csak a következő Linux-disztribúciók támogatja:
- CoreOS Stable
- CentOS 7.1
- Red Hat 7.2
- Ubuntu 15.04
- Ubuntu 16.04

Más Linux-disztribúció jelenleg nem támogatottak, és a bővítmény nem támogatott disztribúciókon meghiúsulhat.

A bővítmény a CentOS 6.9 működik. Azonban 6.9. a rendszer támogatás hiánya miatt a bővítmény fog nem az automatikus újraindítás Ha leállt, vagy leállt. Ha újraindítja a virtuális gép újraindul. Indítsa újra manuálisan a kiterjesztést, lásd: [hogyan újra indítani a felügyelt identitások Azure-erőforrás-kiterjesztés?](#how-do-you-restart-the-managed-identities-for-Azure-resources-extension)

### <a name="how-do-you-restart-the-managed-identities-for-azure-resources-extension"></a>Hogyan újraindítja a felügyelt identitások Azure-erőforrás-kiterjesztés?
A Windows és Linux-bizonyos verziók Ha leáll a bővítményt, a következő parancsmag használható indítsa újra manuálisan:

```powershell
Set-AzureRmVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Az elemek magyarázata: 
- Bővítmény neve és típusa, a Windows: ManagedIdentityExtensionForWindows
- Bővítmény neve és a Linux-típus: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Ismert problémák

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Automation-szkript" sikertelen lesz, amikor megpróbálja felügyelt identitások Azure-erőforrás-kiterjesztés export schématu.

Ha egy virtuális gépen engedélyezve van a felügyelt identitások az Azure-erőforrásokhoz, használja az "Automation-szkript" funkciót a virtuális gép vagy a saját erőforráscsoportján megkísérlésekor a következő hiba jelenik meg:

![Az Azure-erőforrások automation-szkript felügyelt identitások exportálási hiba](./media/msi-known-issues/automation-script-export-error.png)

A felügyelt identitások az Azure-erőforrások (elavult. január 2019 a tervezett) Virtuálisgép-bővítmény amelyen jelenleg nem teszi lehetővé a séma egy erőforráscsoport sablonjának exportálása. Ennek eredményeképpen a létrejött sablon nem jeleníti meg konfigurációs paraméter egy felügyelt identitások az Azure-erőforrásokhoz az erőforráson. Ezek a szakaszok manuálisan is hozzáadhatók a következő szereplő példák [konfigurálása felügyelt Azure-erőforrások egy Azure-beli Virtuálisgép-sablonok használata az identitások](qs-configure-template-windows-vm.md).

Ha a séma exportálási funkciót az Azure-erőforrások Virtuálisgép-bővítmény (elavult. január 2019 a tervezett) felügyelt identitások számára elérhetővé válik, az lesz jelenik meg [exportálása előtt erőforráscsoportok, a Virtuálisgép-bővítményektartalmazó](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>Konfigurációs panelen nem jelenik meg az Azure Portalon

Ha a virtuális gép konfigurációs panel nem látható a virtuális Gépen, majd az Azure-erőforrások felügyelt identitások nincs engedélyezve a portálon az Ön régiójában még.  Próbálja újra később.  Felügyelt identitások az Azure-erőforrások is engedélyezheti a virtuális gép használatának [PowerShell](qs-configure-powershell-windows-vm.md) vagy a [Azure CLI-vel](qs-configure-cli-windows-vm.md).

### <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>A hozzáférés-vezérlés (IAM) paneljén a virtuális gépek nem hozzáférés hozzárendelése

Ha **virtuális gép** nem jelenik meg az Azure Portalon választást **rendelhet hozzáféréseket** a **hozzáférés-vezérlés (IAM)** > **hozzáadása engedélyek**, majd a felügyelt identitások Azure-erőforrások számára van még nincs engedélyezve a portálon az Ön régiójában. Próbálja újra később.  Az Azure-erőforrások egyszerű szolgáltatás a felügyelt identitásokból keressen rá a virtuális gép szerepkör-hozzárendelés továbbra is választhat az identitást.  Adja meg a virtuális gép nevét a **kiválasztása** mező, és az egyszerű szolgáltatás megjelenik a keresési eredményekben.

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>Virtuális gép nem erőforráscsoportba vagy előfizetésbe az áthelyezés után indul el

Ha áthelyezi a virtuális gép futó állapotba kerül, akkor továbbra is az áthelyezés idejére. Azonban az áthelyezés után a virtuális gép leáll és újraindul, ha akkor fog tudni elindulni. Ez a hiba akkor fordul elő, mert a virtuális gép nem frissíti a felügyelt identitások Azure-erőforrás-identitás mutató hivatkozást, és továbbra is a régi erőforráscsoportban mutat.

**Megkerülő megoldás** 
 
Aktiválása egy frissítést a virtuális gépen, így az előbbi beszerezheti a megfelelő értékeket a felügyelt identitások az Azure-erőforrásokhoz. Egy Virtuálisgép-tulajdonság módosításának a hivatkozást a felügyelt identitások Azure-erőforrás-identitás frissítése teheti meg. Például megadhatja egy új címke értéke a virtuális gépen a következő paranccsal:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Ezzel a paranccsal egy új címke "fixVM"-1 érték beállítása a virtuális gép. 
 
Ez a tulajdonság beállításával frissíti a virtuális gép a megfelelő felügyelt identitások Azure-erőforrások erőforrás URI-t, és majd kell látnia a virtuális gép elindításához. 
 
Miután a virtuális gép elindul, a címke távolíthatja el az alábbi parancs használatával:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="vm-extension-provisioning-fails"></a>Virtuálisgép-bővítmény kiépítése sikertelen

A Virtuálisgép-bővítmény kiépítése a DNS-keresési hibák miatt meghiúsulhat. Indítsa újra a virtuális Gépet, és próbálkozzon újra.
 
> [!NOTE]
> A Virtuálisgép-bővítmény által január 2019 elavulásának tervezünk. Azt javasoljuk, hogy áthelyezi a IMDS végpont használatával.

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Egy előfizetés átvitele az Azure AD-címtár között

Felügyelt identitások nem módosul, ha egy előfizetés áthelyezték vagy át egy másik címtárban. Ennek eredményeképpen bármely létező alapértelmezett vagy felhasználó által hozzárendelt felügyelt identitások működésképtelen lesz. 

Áthidaló megoldásként után az előfizetés át lett helyezve, letilthatja felügyelt identitások rendszer által hozzárendelt, és újra engedélyeznie kell őket. Hasonlóképpen törölje és hozza létre újra a felügyelt felhasználó által hozzárendelt identitások. 

## <a name="known-issues-with-user-assigned-managed-identities"></a>Felügyelt identitások felhasználó által hozzárendelt ismert problémái

- Egy felhasználó által hozzárendelt felügyelt identitás létrehozása a különleges karakterek (például aláhúzásjelet lehet) a neve, nem támogatott.
- Felhasználó által hozzárendelt identitás nevek korlátozódnak 24 karakter hosszúságú lehet. Ha a név legfeljebb 24 karakter hosszúságú, az identitás sikertelen lesz-e hozzá kell rendelni egy erőforrást (pl. virtuális gép.)
- (Elavult. január 2019 a tervezett) felügyelt identitás virtuálisgép-bővítmény használata esetén a támogatott határértéke 32, felhasználó által hozzárendelt felügyelt identitások. Nem felügyelt identitás virtuálisgép-bővítmény a támogatott határértéke 512.  
- Egy felhasználó által hozzárendelt felügyelt identitás áthelyezése egy másik erőforráscsoportban található az identitás érvényteleníteni miatt. Ennek eredményeképpen nem képes az identitáshoz tartozó jogkivonatok kérelmezésére. 
- Előfizetés átadása egy másik címtárban megszakítja a meglévő felhasználó által hozzárendelt felügyelt identitást. 