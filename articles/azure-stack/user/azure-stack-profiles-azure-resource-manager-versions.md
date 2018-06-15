---
title: Erőforrás-szolgáltató API verziókat támogatja Azure verem profilok |} Microsoft Docs
description: További tudnivalók az Azure verem profilok által támogatott Azure Resource Manager-verzió.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: ee4321b905396f78e7dad9248b9e377dad250a13
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "34010975"
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Erőforrás-szolgáltató API verziókat támogatja Azure verem profilok

Az erőforrás-szolgáltató és a verziószámokra minden ebben a cikkben az Azure verem által használt API-profil található. Ez a cikk a táblázat mindegyik erőforrás-szolgáltató és a profilok az API verzió támogatott verziók. Mindegyik erőforrás-szolgáltató olyan típusú erőforrások és az adott verziószámok tartalmazza.

Az API-profil három elnevezési konvenciót használ:
 - legújabb
 - Éééé-hh-nn-hibrid
 - éééé-hh-nn-profil

Egy ismertetése API profilok és verzió kiadás ütemben történik az Azure-verem [kezelése API verziója profilok Azure verem](azure-stack-version-profiles.md).

> [!Note]  
> A **legújabb** API profil az erőforrás-szolgáltató API verziója legújabb tartalmazza, és nem szerepel ebben a cikkben.

## <a name="overview-of-2018--03-01-hybrid"></a>2018 – 03-01-hibrid áttekintése

| Erőforrás-szolgáltató | API-verzió |
|-----------------------------------------------|-----------------------------------------------------|
| Microsoft.Compute | 2017-03-30 |
| Microsoft.Network | 2017-10-01<br>VPN-átjáró lesz 2017-03-01 |
| Microsoft.Storage (adatok Vezérlősík) | 2017-04-17 |
| Microsoft.Storage (vezérlő Vezérlősík) | 2016-01-01 |
| Microsoft. Web | 2016-08-01<br>Ez az a legújabb (mostantól) az Azure-ban |
| Microsoft.KeyVault | 2016-10-01 (nem módosítása) |
| Microsoft.Resources (Azure Resource Manager maga) | 2016-02-01 |
| Microsoft.Authorization (házirend műveletek) | 2015-11-01 |
| Microsoft.Insights | 2015-11-01 |
| Microsoft.Keyvault | 2016-10-01 |
| Szabályzat | 2016-10-01 |
| További források | 2016-10-01 |
| Resources_Links | 2016-10-01 |
| Resources_Locks | 2016-10-01 |
| Előfizetések | 2016-10-01 |

Több a verziók listáját a szolgáltatók a api-profilban szereplő minden erőforrástípus, lásd: [részleteit a 2018-03-01-hibrid](#details-for-the-2018-03-01-hybrid) profil.

## <a name="overview-of-2017-03-09-profile"></a>2017-03-09-profil – áttekintés

| Erőforrás-szolgáltató | API-verzió |
|------------------------------------------------|------------------------------|
| Microsoft.Compute | 2016-03-30 |
| Microsoft.Network | 2015-06-15 |
| Microsoft.Storage (adatok Vezérlősík) | 2015-04-05  |
| Microsoft.Storage (vezérlő Vezérlősík) | 2016-01-01   |
| Microsoft.Websites | 2016-01-01 |
| Microsoft.KeyVault | 2016-10-01<br>(Nem módosítása) |
| Microsoft.Resources<br>(Az azure Resource Manager maga) | 2016-02-01 |
| Microsoft.Authorization<Br>(házirend műveletek) | 2015-11-01 |
| Microsoft.Insights | 2015-11-01 |
| Microsoft.Keyvault | 2016-10-01 |
| Szabályzat | 2015-10-01. dátumú előnézeti |
| További források | 2016-02-01 |
| Resources_Links | 2016-09-01 |
| Resources_Locks | 2016-09-01 |
| Előfizetések | 2016-06-1 |

Több a verziók listáját a szolgáltatók a api-profilban szereplő minden erőforrástípus, lásd: [2017-03-09-profil részletei](#details-for-the-2017-03-09-profile)

## <a name="details-for-the-2018-03-01-hybrid"></a>2018-03-01-hibrid részletei

### <a name="microsoftauthorization"></a>Microsoft.Authorization

Szerepköralapú hozzáférés-vezérlés használatával a műveleteinek a szervezet felhasználói erőforrások kezelése. A műveletek készletét lehetővé teszi szerepkörök definiálása, felhasználók vagy csoportok szerepkörök hozzárendelése és engedélyek adatainak beolvasása. További információkért lásd: [engedélyezési](https://docs.microsoft.com/rest/api/authorization/).

| Erőforrástípusok | API-verziók |
|---------------------|--------------------|
| Zárolások | 2017-04-01 |
| Műveletek | 2015-07-01 |
| Engedélyek | 2015-07-01 |
| Szabályzat-hozzárendelések | 2016-12-01 (2017-06-01. előzetes verzió) |
| Szabályzatdefiníciók | 2016-12-01 |
| Szolgáltatói műveletekhez | 2015 07-01. dátumú előnézeti |
| Szerepkör-hozzárendelések | 2015-07-01 |
| Szerepkör-definíciók | 2015-07-01 |

### <a name="microsoftcommerce"></a>Microsoft.Commerce

| Erőforrás típusa | API-verzió |
|----------------------------------|----------------------|
| Delegált szolgáltató előfizetések | 2015-06-01 - megtekintése |
| Delegált használati összesítések | 2015-06-01 - megtekintése |
| Becsült erőforrás töltött | 2015-06-01 – előzetes |
| Műveletek | 2015-06-01 - megtekintése |
| Előfizető használati összesítések | 2015-06-01 - megtekintése |
| Használati összesítések | 2015-06-01 - megtekintése |

### <a name="microsoftcompute"></a>Microsoft.Compute

Az Azure számítási API-k adnia Önnek programozott hozzáférést a virtuális gépek és a támogató erőforrásokat. További információkért lásd: [Azure számítási](https://docs.microsoft.com/rest/api/compute/).

| Erőforrás típusa | API-verzió |
|---------------------------------------------------------------|-------------|
| Rendelkezésre állási csoportok | 2016-03-30 |
| Helyek | 2016-03-30 |
| Az operations helyek | 2016-03-30 |
| Helyek/közzétevők | 2016-03-30 |
| Helyek/módjait | 2016-03-30 |
| Helyek/vmSizes | 2016-03-30 |
| Műveletek | 2016-03-30 |
| Virtuális gépek | 2016-03-30 |
| Virtuálisgép-bővítmények | 2016-03-30 |
| Virtual Machine Scale Sets | 2016-03-30 |
| Virtuálisgép-méretezési készletek/bővítmények | 2016-03-30 |
| Virtuálisgép-méretezési készletek és a hálózati adapterek | 2016-03-30 |
| Virtuálisgép-méretezési készletek/virtuális gépek | 2016-03-30 |
| Virtuális gépek méretezési készletek/virtuális gépek vannak/megváltoztatja | 2016-03-30 |

### <a name="microsoftgallery"></a>Microsoft.Gallery

| Erőforrás típusa | API-verzió |
|------------------|-------------|
| Válogatás | 2015-04-01 |
| Válogatott tartalom | 2015-04-01 |
| Válogatott tartalom kivonata | 2015-04-01 |
| Gyűjteményelemek | 2015-04-01 |
| Műveletek | 2015-04-01 |
| Portál | 2015-04-01 |
| Keresés | 2015-04-01 |
| Javaslat | 2015-04-01 |

### <a name="microsoftinsights"></a>Microsoft.Insights

| Erőforrástípusok | API-verziók |
|--------------------|--------------------|
| Riasztási szabályok | 2016-03-01 |
| Kategóriák | 2017 03-01. dátumú előnézeti |
| Eseménytípusok | 2017 03-01. dátumú előnézeti |
| Metrikadefiníciók | 2016-03-01 |
| Műveletek | 2015-04-01 |

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

A kulcs kezelése-tárolók és a kulcsokat, a titkos kulcsok és a tanúsítványok belül a kulcstárolót. További információkért lásd: [Azure Key Vault REST API-referencia](https://docs.microsoft.com/rest/api/keyvault/).

| Erőforrástípusok | API-verziók |
|-------------------------|--------------|
| Műveletek | 2016-10-01 |
| Tárolók | 2016-10-01 |
| Tárolók / hozzáférési házirendek | 2016-10-01 |
| Tárolók/titkos kulcsok | 2016-10-01 |

### <a name="microsoftnetwork"></a>Microsoft.Network

Műveletek hívás eredménye a a rendelkezésre álló hálózati felhő műveletek listájának megjelenítése. További információkért lásd: [művelet REST API](https://docs.microsoft.com/rest/api/operation/).

| Erőforrástípusok | API-verziók |
|---------------------------|--------------|
| Kapcsolatok | 2015-06-15 |
| DNS-zónák | 2016-04-01 |
| Terheléselosztók | 2015-06-15 |
| Helyi hálózati átjáró | 2015-06-15 |
| Helyek | 2016-04-01 |
| Hely/operationResults | 2016-04-01 |
| Az operations helyek | 2016-04-01 |
| Helyek/módjait | 2016-04-01 |
| Hálózati illesztők | 2015-06-15 |
| Network Security Groups (Hálózati biztonsági csoportok) | 2015-06-15 |
| Műveletek | 2015-06-15 |
| Nyilvános IP-cím | 2015-06-15 |
| Útvonaltáblák | 2015-06-15 |
| Virtuális hálózati átjáró | 2015-06-15 |
| Virtuális hálózatok | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

Az Azure Resource Manager lehetővé teszi, hogy akkor helyezheti üzembe és felügyelheti a Azure megoldások infrastruktúra. Az erőforráscsoportok kapcsolódó erőforrások rendszerezése, és az erőforrások JSON sablonok telepítése. Bevezető történő központi telepítésére és a Resource Manager-erőforrások kezelése, tekintse meg a [Azure Resource Manager áttekintése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

| Erőforrástípusok | API-verziók |
|-----------------------------------------|-------------------|
| Alkalmazás-regisztráció | 2015-01-01 |
| Erőforrásnév ellenőrzése | 2015-012016-09-01 |
| Delegált szolgáltatók | 2015-01-01 |
| Delegált szolgáltatók/ajánlatok | 2015-01-01 |
| DelegatedProviders/offers/estimatePrice | 2015-01-01 |
| Központi telepítés | 2016-0209-01 |
| Az operations központi telepítések | 2016-0209-01 |
| Bővítmények metaadatok | 2015-01-01 |
| Hivatkozások | 2015-012016-09-01 |
| Helyek | 2015-01-01 |
| Ajánlatok | 2015-01-01 |
| Műveletek | 2015-01-01 |
| Szolgáltatók | 2015-012017-08-01 |
| Erőforráscsoportok | 2015-012016-09-01 |
| További források | 2015-012016-09-01 |
| Előfizetések | 2015-012016-09-01 |
| Előfizetések/hely | 2015-012016-09-01 |
| Előfizetések/művelet | 2015-012016-09-01 |
| Előfizetések/szolgáltatók | 2015-012017-08-01 |
| Az előfizetések és az erőforrások csoportok | 2015-012016-09-01 |
| Előfizetések/resourceGroups/erőforrások | 2015-012016-09-01 |
| Előfizetések/erőforrások | 2015-012016-09-01 |
| Előfizetések/tagNames | 2016-0609-01 |
| Subscriptions/tagNames/tagValues | 2016-0609-01 |
| Bérlők | 2015-012017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage 

A Storage erőforrás-szolgáltató (SRP) lehetővé teszi a tárfiók és a kulcsok programozott módon kezelését. További információkért lásd: [Azure Storage erőforrás szolgáltató REST API-referencia](https://docs.microsoft.com/rest/api/storagerp/).

| Erőforrástípusok | API-verziók |
|-------------------------|--------------|
| Név foglaltságának ellenőrzése | 2016-01-01 |
| Helyek | 2016-01-01 |
| Helyek/kvóták | 2016-01-01 |
| Műveletek | 2016-01-01 |
| Tárfiókok | 2016-01-01 |
| A tanúsítványalgoritmusok | 2016-01-01 |

## <a name="details-for-the-2017-03-09-profile"></a>A 2017-03-09-profil részletei

### <a name="microsoft-authorization"></a>Microsoft-engedély

| Erőforrástípusok | API-verziók |
|---------------------|---------------------------------|
| Zárolások | 2017-04-01 |
| Műveletek | 2015-07-01 |
| Engedélyek | 2015-07-01 |
| Szabályzat-hozzárendelések | 2016-12-01 (2017-06-01. előzetes verzió) |
| Szabályzatdefiníciók | 2016-12-01 |
| Szolgáltatói műveletekhez | 2015 07-01. dátumú előnézeti |
| Szerepkör-hozzárendelések | 2015-07-01 |
| Szerepkör-definíciók | 2015-07-01 |

### <a name="microsoftcompute"></a>Microsoft.Compute

| Erőforrás típusa | API-verzió |
|---------------------------------------------------------------|-------------|
| Rendelkezésre állási csoportok | 2016-03-30 |
| Helyek | 2016-03-30 |
| Az operations helyek | 2016-03-30 |
| Helyek/közzétevők | 2016-03-30 |
| Helyek/módjait | 2016-03-30 |
| Helyek/vmSizes | 2016-03-30 |
| Műveletek | 2016-03-30 |
| Virtuális gépek | 2016-03-30 |
| Virtuálisgép-bővítmények | 2016-03-30 |
| Virtual Machine Scale Sets | 2016-03-30 |
| Virtuálisgép-méretezési készletek/bővítmények | 2016-03-30 |
| Virtuálisgép-méretezési készletek és a hálózati adapterek | 2016-03-30 |
| Virtuálisgép-méretezési készletek/virtuális gépek | 2016-03-30 |
| Virtuális gépek méretezési készletek/virtuális gépek vannak/megváltoztatja | 2016-03-30 |

### <a name="microsoftnetwork"></a>Microsoft.Network

| Erőforrástípusok | API-verziók |
|---------------------------|--------------|
| Kapcsolatok | 2015-06-15 |
| DNS-zónák | 2016-04-01 |
| Terheléselosztók | 2015-06-15 |
| Helyi hálózati átjáró | 2015-06-15 |
| Helyek | 2016-04-01 |
| Hely/operationResults | 2016-04-01 |
| Az operations helyek | 2016-04-01 |
| Helyek/módjait | 2016-04-01 |
| Hálózati illesztők | 2015-06-15 |
| Network Security Groups (Hálózati biztonsági csoportok) | 2015-06-15 |
| Műveletek | 2015-06-15 |
| Nyilvános IP-cím | 2015-06-15 |
| Útvonaltáblák | 2015-06-15 |
| Virtuális hálózati átjáró | 2015-06-15 |
| Virtuális hálózatok | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

| Erőforrástípusok | API-verziók |
|-----------------------------------------|--------------|
| Alkalmazás-regisztráció | 2015-01-01 |
| Erőforrásnév ellenőrzése | 2016-09-01 |
| Delegált szolgáltatók | 2015-01-01 |
| Delegált szolgáltatók/ajánlatok | 2015-01-01 |
| DelegatedProviders/offers/estimatePrice | 2015-01-01 |
| Központi telepítés | 2016-09-01 |
| Az operations központi telepítések | 2016-09-01 |
| Bővítmények metaadatok | 2015-01-01 |
| Hivatkozások | 2016-09-01 |
| Helyek | 2015-01-01 |
| Ajánlatok | 2015-01-01 |
| Műveletek | 2015-01-01 |
| Szolgáltatók | 2017-08-01 |
| Erőforráscsoportok | 2016-09-01 |
| További források | 2016-09-01 |
| Előfizetések | 2016-09-01 |
| Előfizetések/hely | 2016-09-01 |
| Előfizetések/művelet | 2016-09-01 |
| Előfizetések/szolgáltatók | 2017-08-01 |
| Az előfizetések és az erőforrások csoportok | 2016-09-01 |
| Előfizetések/resourceGroups/erőforrások | 2016-09-01 |
| Előfizetések/erőforrások | 2016-09-01 |
| Subscriptiosn/tagNames | 2016-09-01 |
| Subscriptions/tagNames/tagValues | 2016-09-01 |
| Bérlők | 2017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

| Erőforrástípusok | API-verziók |
|-------------------------|--------------|
| Név foglaltságának ellenőrzése | 2016-01-01 |
| Helyek | 2016-01-01 |
| Helyek/kvóták | 2016-01-01 |
| Műveletek | 2016-01-01 |
| Tárfiókok | 2016-01-01 |
| A tanúsítványalgoritmusok | 2016-01-01 |

## <a name="next-steps"></a>További lépések

* [A PowerShell telepítése az Azure Stack szolgáltatáshoz](azure-stack-powershell-install.md)
* [Az Azure-verem felhasználói PowerShell környezet konfigurálása](azure-stack-powershell-configure-user.md)  
