---
title: Jelentkezzen be egy Azure virtuális gépek Felügyeltszolgáltatás-identitás használata
description: Részletes útmutatásért és példákért használatával jelentkezzen be a parancsfájl-ügyfél az Azure virtuális gép MSI szolgáltatásnevekkel és az erőforrás eléréséhez.
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
ms.openlocfilehash: 205938bbf615face0768028717a333c13c1fafa1
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590313"
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in"></a>Jelentkezzen be egy Azure virtuális gépek Felügyeltszolgáltatás-identitás (MSI) használata 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
A cikk ismerteti a PowerShell és CLI-példaszkriptek a bejelentkezéshez, fontos hibakezelés többek között az MSI szolgáltatásnevekkel és útmutatás használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Ha azt tervezi, ez a cikk az Azure PowerShell vagy az Azure CLI-példák használata, ügyeljen arra, hogy telepítse a legújabb verzióját, [Azure PowerShell-lel](https://www.powershellgallery.com/packages/AzureRM) vagy [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Ez a cikk az összes minta parancsfájl feltételezi, hogy egy MSI-kompatibilis virtuális gépen fut a parancssori ügyfelét. Az Azure Portalon a virtuális gép "Csatlakozás" funkció használatával távolról csatlakozzon a virtuális Géphez. A virtuális gép MSI engedélyezéséhez a részletekért lásd: [konfigurálja a virtuális gépek Felügyeltszolgáltatás-identitás (MSI) az Azure portal használatával](qs-configure-portal-windows-vm.md), vagy az variant cikkekben (a PowerShell, CLI, egy sablon vagy az Azure SDK használatával). 
> - Erőforrásokhoz való hozzáférés során hibák megelőzése, a virtuális gép MSI meg kell adni legalább a megfelelő hatókörben eléréséhez "Olvasó" (a virtuális gép vagy újabb), hogy a virtuális gépen az Azure Resource Manager-műveletek. Lásd: [a Felügyeltszolgáltatás-identitás (MSI) hozzáférések hozzárendelése az Azure portal használatával erőforrás](howto-assign-access-portal.md) részleteiről.

## <a name="overview"></a>Áttekintés

Egy olyan MSI Csomaghoz biztosít egy [szolgáltatásnév-objektum](../develop/developer-glossary.md#service-principal-object) , amely [MSI engedélyezéséhez alapján létrehozott](overview.md#how-does-it-work) a virtuális gépen. Az egyszerű szolgáltatás Azure-erőforrásokhoz való hozzáférést adott, és olyan identitásként parancsfájl/parancssori ügyfelek bejelentkezési és erőforrás-hozzáférés használja. Hagyományosan annak érdekében, hogy a saját identitás alatt védett erőforrások eléréséhez, a parancsfájl ügyfél kell:  

   - regisztrált és az Azure AD-webalkalmazásként bizalmas vagy ügyfél által jóváhagyott
   - Jelentkezzen be a szolgáltatásnevet, az alkalmazás hitelesítő adatokkal (amelyek valószínűleg ágyazva a szkriptbe) alatt

Az MSI-vel a parancsfájl-ügyfél már nincs szüksége teheti, ahogy azt a bejelentkezéshez az MSI szolgáltatásnevekkel alatt. 

## <a name="azure-cli"></a>Azure CLI

Az alábbi parancsfájl bemutatja, hogyan lehet:

1. Jelentkezzen be Azure ad-ben a virtuális gép MSI szolgáltatásnév szerint  
2. Hívja meg az Azure Resource Manager és a virtuális gép szolgáltatás egyszerű azonosító lekérése CLI-token beszerzése és használata kezelése, automatikus gondoskodik. Ügyeljen arra, hogy a virtuális gép nevét `<VM-NAME>`.  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The MSI service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Az alábbi parancsfájl bemutatja, hogyan lehet:

1. Jelentkezzen be Azure ad-ben a virtuális gép MSI szolgáltatásnév szerint  
2. Hívja meg a virtuális gép adatainak lekérése egy Azure Resource Manager-parancsmagot. Token használatát, automatikusan kezelése PowerShell gondoskodik.  

   ```azurepowershell
   Add-AzureRmAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's MSI. 
   $vmInfoPs = Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The MSI service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Erőforrás-azonosítókat megtalálhatja az Azure-szolgáltatásokhoz

Lásd: [Azure-szolgáltatások, hogy a támogatás az Azure AD-hitelesítés](services-support-msi.md#azure-services-that-support-azure-ad-authentication) erőforrásokat, amelyek támogatják az Azure ad-ben, és MSI teszteltük, és a megfelelő erőforrás-azonosítók listáját.

## <a name="error-handling-guidance"></a>Kezelési útmutató hiba 

Például a következő válaszok arra utalhat, hogy a virtuális gép MSI nincs megfelelően konfigurálva:

- PowerShell: *Invoke-WebRequest: nem lehet kapcsolódni a távoli kiszolgáló*
- CLI: *MSI: nem sikerült beolvasni a származó jogkivonat "http://localhost:50342/oauth2/token" hibával "HTTPConnectionPool (gazdagép"localhost", port = = 50342)* 

Ha ezek a hibák egyike jelentkezik, térjen vissza az Azure VM a [az Azure portal](https://portal.azure.com) és:

- Nyissa meg a **konfigurációs** lapon és ellenőrizze a "Felügyelt szolgáltatásidentitás" értéke "Igen"értéket.
- Nyissa meg a **bővítmények** lapon, és győződjön meg, hogy a MSI-bővítmény sikeresen telepítve.

Ha vagy helytelen, szükség lehet ismételt üzembe helyezése az MSI az erőforráson újra, vagy az üzemelő példány hibájának elhárítása. Lásd: [konfigurálja a virtuális gépek Felügyeltszolgáltatás-identitás (MSI) az Azure portal használatával](qs-configure-portal-windows-vm.md) Ha Virtuálisgép-konfiguráció segítségre van szüksége.

## <a name="related-content"></a>Kapcsolódó tartalom

- Egy Azure-beli virtuális gépen az MSI engedélyezéséhez tekintse [konfigurálja a virtuális gépek Felügyeltszolgáltatás-identitás (MSI) PowerShell-lel](qs-configure-powershell-windows-vm.md), vagy [konfigurálja a virtuális gépek Felügyeltszolgáltatás-identitás (MSI) Azure parancssori felület használatával](qs-configure-cli-windows-vm.md)

Használja a következő megjegyzéseket visszajelzést, és segítsen finomíthatja és a tartalom formázása.








