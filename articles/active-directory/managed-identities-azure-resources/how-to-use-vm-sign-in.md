---
title: Az Azure-erőforrások egy Azure virtuális gép esetében jelentkezzen be a felügyelt identitások használatával
description: Részletes utasítások és példák egy Azure virtuális gép felügyelt identitások az Azure-erőforrások egyszerű szolgáltatás ügyfél bejelentkezési parancsfájl és erőforrás-hozzáférés.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: 0f8789d64b550d9f0a45aa65728fbc1db64d6def
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094926"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Az Azure-erőforrások egy Azure virtuális gép esetében jelentkezzen be a felügyelt identitások használatával 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
A cikk ismerteti a PowerShell és CLI-példaszkriptek a bejelentkezéshez az Azure-erőforrások egyszerű szolgáltatást, és útmutatást, többek között a hibakezelés fontos felügyelt identitások használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Ha azt tervezi, ez a cikk az Azure PowerShell vagy az Azure CLI-példák használata, ügyeljen arra, hogy telepítse a legújabb verzióját, [Azure PowerShell-lel](https://www.powershellgallery.com/packages/AzureRM) vagy [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Ez a cikk az összes minta parancsfájl feltételezi, hogy a parancssori ügyfelét futtató felügyelt identitások az Azure-erőforrások engedélyezve van a virtuális gépen. Az Azure Portalon a virtuális gép "Csatlakozás" funkció használatával távolról csatlakozzon a virtuális Géphez. Felügyelt identitások a virtuális gép Azure-erőforrások engedélyezésével kapcsolatos részletekért lásd: [konfigurálása felügyelt identitások az Azure-erőforrások a virtuális gép az Azure portal használatával](qs-configure-portal-windows-vm.md), vagy az variant cikkekben (PowerShell, CLI, egy sablon vagy az Azure használatával SDK-T). 
> - Erőforrásokhoz való hozzáférés során hibák megelőzése, a virtuális gép felügyelt identitás meg kell adni legalább a megfelelő hatókörben eléréséhez "Olvasó" (a virtuális gép vagy újabb), hogy a virtuális gépen az Azure Resource Manager-műveletek. Lásd: [hozzárendelése felügyelt identitások Azure-erőforrások eléréséhez az Azure portal használatával erőforrás](howto-assign-access-portal.md) részleteiről.

## <a name="overview"></a>Áttekintés

Felügyelt identitások Azure-erőforrások számára biztosít egy [szolgáltatásnév-objektum](../develop/developer-glossary.md#service-principal-object) , amely [engedélyezése az Azure-erőforrások felügyelt identitások alapján létrehozott](overview.md#how-does-it-work) a virtuális gépen. Az egyszerű szolgáltatás Azure-erőforrásokhoz való hozzáférést adott, és olyan identitásként parancsfájl/parancssori ügyfelek bejelentkezési és erőforrás-hozzáférés használja. Hagyományosan annak érdekében, hogy a saját identitás alatt védett erőforrások eléréséhez, a parancsfájl ügyfél kell:  

   - regisztrált és az Azure AD-webalkalmazásként bizalmas vagy ügyfél által jóváhagyott
   - Jelentkezzen be a szolgáltatásnevet, az alkalmazás hitelesítő adatokkal (amelyek valószínűleg ágyazva a szkriptbe) alatt

Felügyelt identitások az Azure-erőforrások, az a parancsfájl-ügyfél már nincs szüksége teheti, ahogy azt a bejelentkezéshez az Azure-erőforrások egyszerű szolgáltatás a felügyelt identitások alapján. 

## <a name="azure-cli"></a>Azure CLI

Az alábbi parancsfájl bemutatja, hogyan lehet:

1. Jelentkezzen be a virtuális gép felügyelt identitás az Azure-erőforrások egyszerű szolgáltatás Azure ad-ben  
2. Hívja meg az Azure Resource Manager és a virtuális gép szolgáltatás egyszerű azonosító lekérése CLI-token beszerzése és használata kezelése, automatikus gondoskodik. Ügyeljen arra, hogy a virtuális gép nevét `<VM-NAME>`.  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Az alábbi parancsfájl bemutatja, hogyan lehet:

1. Jelentkezzen be a virtuális gép felügyelt identitás az Azure-erőforrások egyszerű szolgáltatás Azure ad-ben  
2. Hívja meg a virtuális gép adatainak lekérése egy Azure Resource Manager-parancsmagot. Token használatát, automatikusan kezelése PowerShell gondoskodik.  

   ```azurepowershell
   Add-AzureRmAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Erőforrás-azonosítókat megtalálhatja az Azure-szolgáltatásokhoz

Lásd: [Azure-szolgáltatások, hogy a támogatás az Azure AD-hitelesítés](services-support-msi.md#azure-services-that-support-azure-ad-authentication) , amelyek támogatják az Azure ad-ben, és az Azure-erőforrások és a megfelelő erőforrás-azonosítók felügyelt identitások teszteltük erőforrások listáját.

## <a name="error-handling-guidance"></a>Kezelési útmutató hiba 

Például a következő válaszok arra utalhat, hogy a virtuális gép felügyelt identitás az Azure-erőforrások nincs megfelelően konfigurálva:

- PowerShell: *Invoke-WebRequest: nem lehet kapcsolódni a távoli kiszolgáló*
- CLI: *MSI: nem sikerült beolvasni a származó jogkivonat "http://localhost:50342/oauth2/token" hibával "HTTPConnectionPool (gazdagép"localhost", port = = 50342)* 

Ha ezek a hibák egyike jelentkezik, térjen vissza az Azure VM a [az Azure portal](https://portal.azure.com) és:

- Nyissa meg a **identitás** lapon és ellenőrizze, **rendszerhez rendelt** van beállítva az "Igen"értéket.
- Nyissa meg a **bővítmények** lapon, és győződjön meg, hogy a felügyelt identitások Azure-erőforrás-kiterjesztés **(elavult. január 2019 a tervezett)** üzembe helyezése sikeresen befejeződött.

Ha vagy helytelen, szükség lehet ismételt üzembe helyezése az Azure-erőforrásokhoz az erőforráson a felügyelt identitásokból újra, vagy az üzemelő példány hibájának elhárítása. Lásd: [egy virtuális gépen az Azure portal segítségével Azure-erőforrások konfigurálása felügyelt identitások](qs-configure-portal-windows-vm.md) Ha Virtuálisgép-konfiguráció segítségre van szüksége.

## <a name="next-steps"></a>További lépések

- Engedélyezheti a felügyelt identitások az Azure-erőforrások egy Azure-beli virtuális gépen [konfigurálása felügyelt identitások az Azure-erőforrások PowerShell-lel, egy Azure virtuális Gépen található](qs-configure-powershell-windows-vm.md), vagy [konfigurálása felügyelt identitások az Azure-erőforrások egy Azure-beli virtuális gépen Azure CLI használatával](qs-configure-cli-windows-vm.md)






