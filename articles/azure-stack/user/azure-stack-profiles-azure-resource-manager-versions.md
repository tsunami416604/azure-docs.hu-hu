---
title: Erőforrás-szolgáltató API-ja verziókat támogatja az Azure Stackben profilok |} A Microsoft Docs
description: Ismerje meg a profilok az Azure Stackben által támogatott Azure Resource Manager-verzió.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 9d33ccf9262d4432ac7255121e97f318d00b5145
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43050649"
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Erőforrás-szolgáltató API-ja verziókat támogatja az Azure Stackben profilok

Ez a cikk az Azure Stack által használt minden egyes API-profil az erőforrás-szolgáltató és a verziószámok találja. Ez a cikk a táblázatokban a verziók mindegyik erőforrás-szolgáltató és a profilok az API-verziók esetében támogatott. Mindegyik erőforrás-szolgáltató erőforrás-típusok és adott verziószámok tartalmazza.

Az API-profil három elnevezési konvenciókat használja:
 - legújabb
 - éééé-hh-nn-hibrid
 - éééé-hh-nn-profil

Egy API-profilokat és verzió kiadási ütem az Azure Stack, ismertetését [kezelése API-verzióprofilok az Azure Stackben](azure-stack-version-profiles.md).

> [!Note]  
> A **legújabb** API profil az erőforrás-szolgáltató API-ja verzió a legújabb tartalmaz, és nem szerepel ebben a cikkben.

## <a name="overview-of-2018--03-01-hybrid"></a>2018-03-01-hibrid áttekintése

| Erőforrás-szolgáltató | API-verzió |
|-----------------------------------------------|-----------------------------------------------------|
| Microsoft.Compute | 2017-03-30 |
| Microsoft.Network | 2017-10-01<br>VPN-átjáró lesz 2017-03-01 |
| A Microsoft.Storage (Adatsík) | 2017-04-17-es |
| A Microsoft.Storage (Vezérlősík) | 2016-01-01 |
| A Microsoft. Web | 2016-08-01<br>Ez az a legújabb (jelen pillanatban) az Azure-ban |
| Microsoft.KeyVault | (Nem módosítása) 2016-10-01 |
| Microsoft.Resources (Azure Resource Manager magát) | 2016-02-01 |
| Microsoft.Authorization (házirend műveletek) | 2015-11-01 |
| Microsoft.Insights | 2015-11-01 |
| Microsoft.Keyvault | 2016-10-01 |
| Szabályzat | 2016-10-01 |
| További források | 2016-10-01 |
| Resources_Links | 2016-10-01 |
| Resources_Locks | 2016-10-01 |
| Előfizetések | 2016-10-01 |

Több a szolgáltatók a api-profilban az egyes erőforrástípusok verzióját, lásd: [részleteit a 2018-03-01-hibrid](#details-for-the-2018-03-01-hybrid) profilt.

## <a name="overview-of-2017-03-09-profile"></a>2017-03-09-profile áttekintése

| Erőforrás-szolgáltató | API-verzió |
|------------------------------------------------|------------------------------|
| Microsoft.Compute | 2016-03-30 |
| Microsoft.Network | 2015-06-15 |
| A Microsoft.Storage (Adatsík) | 2015-04-05  |
| A Microsoft.Storage (Vezérlősík) | 2016-01-01   |
| Microsoft.Websites | 2016-01-01 |
| Microsoft.KeyVault | 2016-10-01<br>(Nem módosítása) |
| Microsoft.Resources<br>(Az azure Resource Manager magát) | 2016-02-01 |
| Microsoft.Authorization<Br>(a házirend műveletek) | 2015-11-01 |
| Microsoft.Insights | 2015-11-01 |
| Microsoft.Keyvault | 2016-10-01 |
| Szabályzat | 2015-10-01-előzetes verzió |
| További források | 2016-02-01 |
| Resources_Links | 2016-09-01 |
| Resources_Locks | 2016-09-01 |
| Előfizetések | 2016. 06. 1 |

Több a szolgáltatók a api-profilban az egyes erőforrástípusok verzióját, lásd: [a 2017-03-09-profile részletei](#details-for-the-2017-03-09-profile)

## <a name="details-for-the-2018-03-01-hybrid"></a>A 2018-03-01-hibrid részletei

### <a name="microsoftauthorization"></a>Microsoft.Authorization

Szerepköralapú hozzáférés-vezérlés használatával kezelheti a a szervezeti felhasználók végezhetnek el az erőforrásokon. Ez számos műveletet lehetővé teszi a Szerepkörök definiálása, szerepköröket hozzárendelni felhasználók vagy csoportok és engedélyek adatainak beolvasása. További információkért lásd: [engedélyezési](https://docs.microsoft.com/rest/api/authorization/).

| Erőforrástípusok | API-verziók |
|---------------------|--------------------|
| Zárolások | 2017-04-01 |
| Műveletek | 2015-07-01 |
| Engedélyek | 2015-07-01 |
| Szabályzat-hozzárendelések | 2016-12-01 (2017. 06. 01-előzetes verzió) |
| Szabályzatdefiníciók | 2016-12-01 |
| Szolgáltatói műveletek | 2015-07-01-előzetes verzió |
| Szerepkör-hozzárendelések | 2015-07-01 |
| Szerepkör-definíciók | 2015-07-01 |

### <a name="microsoftcommerce"></a>Microsoft.Commerce

| Erőforrás típusa | API-verzió |
|----------------------------------|----------------------|
| Delegált szolgáltatói előfizetés | 2015-06-01 - előzetes verzió |
| Delegált használati összesítések | 2015-06-01 - előzetes verzió |
| Erőforrás becsült költségek | 2015-06-01 – előzetes verzió |
| Műveletek | 2015-06-01 - előzetes verzió |
| Előfizető használati összesítések | 2015-06-01 - előzetes verzió |
| Használati összesítések | 2015-06-01 - előzetes verzió |

### <a name="microsoftcompute"></a>Microsoft.Compute

Az Azure Compute API-k, programozás alapú hozzáférést biztosít a virtuális gépek és az azokat támogató erőforrásokhoz. További információkért lásd: [Azure Compute](https://docs.microsoft.com/rest/api/compute/).

| Erőforrás típusa | API-verzió |
|---------------------------------------------------------------|-------------|
| Rendelkezésre állási csoportok | 2016-03-30 |
| Helyek | 2016-03-30 |
| Helyek és műveletek | 2016-03-30 |
| Helyek-közzétevő | 2016-03-30 |
| Helyek és használat | 2016-03-30 |
| Helyek/vmSizes | 2016-03-30 |
| Műveletek | 2016-03-30 |
| Virtuális gépek | 2016-03-30 |
| Virtuálisgép-bővítmények | 2016-03-30 |
| Virtual Machine Scale Sets | 2016-03-30 |
| Virtual Machine Scale Sets/bővítmények | 2016-03-30 |
| Virtual Machine Scale Sets/hálózati adapterek | 2016-03-30 |
| Virtual Machine Scale Sets/virtuális gépek | 2016-03-30 |
| Virtual Machines Scale Sets/virtuális gép/hálózati | 2016-03-30 |

### <a name="microsoftgallery"></a>Microsoft.Gallery

| Erőforrás típusa | API-verzió |
|------------------|-------------|
| Válogatás | 2015-04-01 |
| Válogatott tartalom | 2015-04-01 |
| Válogatott tartalom kivonata | 2015-04-01 |
| Katalóguselemek | 2015-04-01 |
| Műveletek | 2015-04-01 |
| Portál | 2015-04-01 |
| Keresés | 2015-04-01 |
| Javaslat | 2015-04-01 |

### <a name="microsoftinsights"></a>Microsoft.Insights

| Erőforrástípusok | API-verziók |
|--------------------|--------------------|
| Műveletek | 2015-04-01 |
| Eseménytípusok | 2015-04-01 |
| Esemény-kategóriák | 2015-04-01 |
| Metrikadefiníciók | 2018-01-01 |
| Mérőszámok | 2018-01-01 |
| Diagnosztikai beállítások | 2017-05-01-előzetes verzió |
| Diagnosztikai beállítások kategóriái | 2017-05-01-előzetes verzió |


### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

A kulcs kezelése tárolók, valamint a kulcsok, titkos kódok és tanúsítványok a kulcstartók belül. További információkért lásd: [Azure Key Vault REST API-referencia](https://docs.microsoft.com/rest/api/keyvault/).

| Erőforrástípusok | API-verziók |
|-------------------------|--------------|
| Műveletek | 2016-10-01 |
| Tárolók | 2016-10-01 |
| Tárolók / hozzáférési házirendek | 2016-10-01 |
| Tárolók vagy titkos kódokkal | 2016-10-01 |

### <a name="microsoftnetwork"></a>Microsoft.Network

Műveleti hívás eredménye az elérhető hálózati lista reprezentációját. További információkért lásd: [művelet REST API-val](https://docs.microsoft.com/rest/api/operation/).

| Erőforrástípusok | API-verziók |
|---------------------------|--------------|
| Kapcsolatok | 2015-06-15 |
| DNS-zónák | 2016-04-01 |
| Terheléselosztók | 2015-06-15 |
| Helyi hálózati átjáró | 2015-06-15 |
| Helyek | 2016-04-01 |
| Hely/operationResults | 2016-04-01 |
| Helyek és műveletek | 2016-04-01 |
| Helyek és használat | 2016-04-01 |
| Hálózati illesztők | 2015-06-15 |
| Network Security Groups (Hálózati biztonsági csoportok) | 2015-06-15 |
| Műveletek | 2015-06-15 |
| Nyilvános IP-cím | 2015-06-15 |
| Útvonaltáblák | 2015-06-15 |
| Virtuális hálózati átjáró | 2015-06-15 |
| Virtuális hálózatok | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

Az Azure Resource Manager üzembe helyezése és kezelése az Azure-megoldások infrastruktúrája lehetővé teszi. Az erőforráscsoportok kapcsolódó erőforrások rendezéséhez, és az erőforrások JSON-sablonok üzembe helyezése. Bevezetés az erőforrások Resource Manager használatával történő üzembe helyezéséről, lásd: [Azure Resource Manager áttekintése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

| Erőforrástípusok | API-verziók |
|-----------------------------------------|-------------------|
| Alkalmazásregisztráció | 2015-01-01 |
| Erőforrásnév ellenőrzése | 2016-09-01 |
| Delegált szolgáltatók | 2015-01-01 |
| Delegált szolgáltatók és ajánlatokkal. | 2015-01-01 |
| DelegatedProviders/offers/estimatePrice | 2015-01-01 |
| Központi telepítés | 2016-09-01 |
| Az operations központi telepítések | 2016-09-01 |
| Bővítmény-metaadatok | 2015-01-01 |
| Hivatkozások | 2016-09-01 |
| Helyek | 2015-01-01 |
| Ajánlatok | 2015-01-01 |
| Műveletek | 2015-01-01 |
| Szolgáltatók | 2017-08-01 |
| Erőforráscsoportok | 2016-09-01 |
| További források | 2016-09-01 |
| Előfizetések | 2016-09-01 |
| Előfizetések/hely | 2016-09-01 |
| Előfizetések/műveleti eredmények | 2016-09-01 |
| Az előfizetések és szolgáltatók | 2017-08-01 |
| Előfizetések vagy erőforráscsoportok | 2016-09-01 |
| Előfizetés/resourceGroups/erőforrások | 2016-09-01 |
| Az előfizetések és erőforrások | 2016-09-01 |
| Előfizetések/tagNames | 2016-09-01 |
| Subscriptions/tagNames/tagValues | 2016-09-01 |
| Bérlők | 2017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage 

A Storage erőforrás-szolgáltató (SRP) lehetővé teszi, hogy a storage-fiók és kulcsok programozással felügyelheti. További információkért lásd: [Azure Storage Resource Provider REST API-referencia](https://docs.microsoft.com/rest/api/storagerp/).

| Erőforrástípusok | API-verziók |
|-------------------------|--------------|
| Név foglaltságának ellenőrzése | 2016-01-01 |
| Helyek | 2016-01-01 |
| Helyek és kvóták | 2016-01-01 |
| Műveletek | 2016-01-01 |
| Tárfiókok | 2016-01-01 |
| Használati listák | 2016-01-01 |

## <a name="details-for-the-2017-03-09-profile"></a>A 2017-03-09-profile részletei

### <a name="microsoft-authorization"></a>Microsoft-engedély

| Erőforrástípusok | API-verziók |
|---------------------|---------------------------------|
| Zárolások | 2017-04-01 |
| Műveletek | 2015-07-01 |
| Engedélyek | 2015-07-01 |
| Szabályzat-hozzárendelések | 2016-12-01 (2017. 06. 01-előzetes verzió) |
| Szabályzatdefiníciók | 2016-12-01 |
| Szolgáltatói műveletek | 2015-07-01-előzetes verzió |
| Szerepkör-hozzárendelések | 2015-07-01 |
| Szerepkör-definíciók | 2015-07-01 |

### <a name="microsoftcompute"></a>Microsoft.Compute

| Erőforrás típusa | API-verzió |
|---------------------------------------------------------------|-------------|
| Rendelkezésre állási csoportok | 2016-03-30 |
| Helyek | 2016-03-30 |
| Helyek és műveletek | 2016-03-30 |
| Helyek-közzétevő | 2016-03-30 |
| Helyek és használat | 2016-03-30 |
| Helyek/vmSizes | 2016-03-30 |
| Műveletek | 2016-03-30 |
| Virtuális gépek | 2016-03-30 |
| Virtuálisgép-bővítmények | 2016-03-30 |
| Virtual Machine Scale Sets | 2016-03-30 |
| Virtual Machine Scale Sets/bővítmények | 2016-03-30 |
| Virtual Machine Scale Sets/hálózati adapterek | 2016-03-30 |
| Virtual Machine Scale Sets/virtuális gépek | 2016-03-30 |
| Virtual Machines Scale Sets/virtuális gép/hálózati | 2016-03-30 |

### <a name="microsoftnetwork"></a>Microsoft.Network

| Erőforrástípusok | API-verziók |
|---------------------------|--------------|
| Kapcsolatok | 2015-06-15 |
| DNS-zónák | 2016-04-01 |
| Terheléselosztók | 2015-06-15 |
| Helyi hálózati átjáró | 2015-06-15 |
| Helyek | 2016-04-01 |
| Hely/operationResults | 2016-04-01 |
| Helyek és műveletek | 2016-04-01 |
| Helyek és használat | 2016-04-01 |
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
| Alkalmazásregisztráció | 2015-01-01 |
| Erőforrásnév ellenőrzése | 2016-09-01 |
| Delegált szolgáltatók | 2015-01-01 |
| Delegált szolgáltatók és ajánlatokkal. | 2015-01-01 |
| DelegatedProviders/offers/estimatePrice | 2015-01-01 |
| Központi telepítés | 2016-09-01 |
| Az operations központi telepítések | 2016-09-01 |
| Bővítmény-metaadatok | 2015-01-01 |
| Hivatkozások | 2016-09-01 |
| Helyek | 2015-01-01 |
| Ajánlatok | 2015-01-01 |
| Műveletek | 2015-01-01 |
| Szolgáltatók | 2017-08-01 |
| Erőforráscsoportok | 2016-09-01 |
| További források | 2016-09-01 |
| Előfizetések | 2016-09-01 |
| Előfizetések/hely | 2016-09-01 |
| Előfizetések/műveleti eredmények | 2016-09-01 |
| Az előfizetések és szolgáltatók | 2017-08-01 |
| Előfizetések vagy erőforráscsoportok | 2016-09-01 |
| Előfizetés/resourceGroups/erőforrások | 2016-09-01 |
| Az előfizetések és erőforrások | 2016-09-01 |
| Subscriptiosn/tagNames | 2016-09-01 |
| Subscriptions/tagNames/tagValues | 2016-09-01 |
| Bérlők | 2017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

| Erőforrástípusok | API-verziók |
|-------------------------|--------------|
| Név foglaltságának ellenőrzése | 2016-01-01 |
| Helyek | 2016-01-01 |
| Helyek és kvóták | 2016-01-01 |
| Műveletek | 2016-01-01 |
| Tárfiókok | 2016-01-01 |
| Használati listák | 2016-01-01 |

## <a name="next-steps"></a>További lépések

* [A PowerShell telepítése az Azure Stack szolgáltatáshoz](azure-stack-powershell-install.md)
* [Az Azure Stack felhasználói PowerShell-környezet konfigurálása](azure-stack-powershell-configure-user.md)  
