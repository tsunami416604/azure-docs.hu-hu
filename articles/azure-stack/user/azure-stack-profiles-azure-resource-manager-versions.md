---
title: Erőforrás-szolgáltató API verziókat támogatja Azure verem profilok |} Microsoft Docs
description: További tudnivalók az Azure verem profilok által támogatott Azure Resource Manager-verzió.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 2E21C8DE-D540-4C1C-A0EF-1B7125DB7A6E
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 8cc5ce1ec113df7ce92c54022dbe1b6219c8c235
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Erőforrás-szolgáltató API verziókat támogatja Azure verem profilok

Az Azure erőforrás-szolgáltató megadja az erőforrások telepítheti és kezelheti az Azure Resource Manageren keresztül. Minden szolgáltató erőforrásokkal kapcsolatos műveletek kínál. Néhány általános erőforrás-szolgáltatók közé tartozik a Microsoft.Compute, amely megadja a virtuális gépek, a Microsoft.Storage, amely megadja a tárfiók erőforrásainak, és a Microsoft.Web, a webalkalmazások kapcsolódó erőforrások értendők. További információkért lásd az [erőforrás-szolgáltatókat és a típusaikat](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) ismertető cikket.

A következő táblázat az egyes erőforrás-szolgáltató profilok használatakor Azure verem azt jelzi, az API-verzió támogatott verziója.

### <a name="microsoftauthorization"></a>Microsoft.Authorization

Szerepköralapú hozzáférés-vezérlés használatával a műveleteinek a szervezet felhasználói erőforrások kezelése. A műveletek készletét lehetővé teszi szerepkörök definiálása, felhasználók vagy csoportok szerepkörök hozzárendelése és engedélyek adatainak beolvasása. További információkért lásd: [engedélyezési](https://docs.microsoft.com/rest/api/authorization/).

| Típusú erőforrások | API-verziók |
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

Az Azure számítási API-k adnia Önnek programozott hozzáférést a virtuális gépek és a támogató erőforrásokat. További információkért lásd: [Azure számítási](https://docs.microsoft.com/en-us/rest/api/compute/).

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

| Típusú erőforrások | API-verziók |
|--------------------|--------------------|
| Riasztási szabályok | 2016-03-01 |
| Kategóriák | 2017-03-01-preview |
| Eseménytípusok | 2017-03-01-preview |
| Metrikadefiníciók | 2016-03-01 |
| Műveletek | 2015-04-01 |

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

A kulcs kezelése-tárolók és a kulcsokat, a titkos kulcsok és a tanúsítványok belül a kulcstárolót. További információkért lásd: [Azure Key Vault REST API-referencia](https://docs.microsoft.com/rest/api/keyvault/).

| Típusú erőforrások | API-verziók |
|-------------------------|--------------|
| Műveletek | 2016-10-01 |
| Tárolók | 2016-10-01 |
| Tárolók / hozzáférési házirendek | 2016-10-01 |
| Tárolók/titkos kulcsok | 2016-10-01 |

### <a name="microsoftkeyvaultadmin"></a>Microsoft.Keyvault.Admin

A kulcs kezelése-tárolók és a kulcsokat, a titkos kulcsok és a tanúsítványok belül a kulcstárolót. További információkért lásd: [Azure Key Vault REST API-referencia](https://docs.microsoft.com/rest/api/keyvault/).

| Típusú erőforrások | API-verziók |
|------------------|--------------------|
| Helyek | 2017-02-01. dátumú előnézeti |
| Helyek/kvóták | 2017-02-01. dátumú előnézeti |

### <a name="microsoftnetwork"></a>Microsoft.Network

Műveletek hívás eredménye a a rendelkezésre álló hálózati felhő műveletek listájának megjelenítése. További információkért lásd: [művelet REST API](https://docs.microsoft.com/rest/api/operation/).

| Típusú erőforrások | API-verziók |
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

| Típusú erőforrások | API-verziók |
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
| Subscriptions/resourceGroups/resources | 2015-012016-09-01 |
| Subscriptions/resources | 2015-012016-09-01 |
| Subscriptions/tagNames | 2016-0609-01 |
| Subscriptions/tagNames/tagValues | 2016-0609-01 |
| Bérlők | 2015-012017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage 

A Storage erőforrás-szolgáltató (SRP) lehetővé teszi a tárfiók és a kulcsok programozott módon kezelését. További információkért lásd: [Azure Storage erőforrás szolgáltató REST API-referencia](https://docs.microsoft.com/rest/api/storagerp/).

| Típusú erőforrások | API-verziók |
|-------------------------|--------------|
| Név foglaltságának ellenőrzése | 2016-01-01 |
| Helyek | 2016-01-01 |
| Helyek/kvóták | 2016-01-01 |
| Műveletek | 2016-01-01 |
| StorageAccounts | 2016-01-01 |
| A tanúsítványalgoritmusok | 2016-01-01 |

## <a name="next-steps"></a>További lépések

* [A PowerShell telepítése az Azure Stack szolgáltatáshoz](azure-stack-powershell-install.md)
* [Az Azure-verem felhasználói PowerShell környezet konfigurálása](azure-stack-powershell-configure-user.md)  
