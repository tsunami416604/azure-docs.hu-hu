---
title: Felügyelt identitások használata egy Azure-beli virtuális gépen a bejelentkezéshez – Azure AD
description: Lépésenkénti utasítások és példák az Azure-beli virtuális gép felügyelt identitások használatával az Azure-erőforrások egyszerű parancsfájl-ügyfél bejelentkezéshez és erőforrás-hozzáféréshez.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34f4dc749c0254b5aa4e9ff018d2a869832de3f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547382"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Felügyelt identitások használata azure-erőforrásokhoz egy Azure-beli virtuális gépen a bejelentkezéshez 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Ez a cikk powershell- és CLI-parancsfájlos példákat tartalmaz az Azure-erőforrások egyszerű szolgáltatásának felügyelt identitások használatával történő bejelentkezéshez, valamint a fontos témakörök, például a hibakezelés útmutatásait.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Ha ebben a cikkben az Azure PowerShell vagy az Azure CLI példáit szeretné használni, telepítse az [Azure PowerShell](/powershell/azure/install-az-ps) vagy az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)legújabb verzióját. 

> [!IMPORTANT]
> - Ebben a cikkben az összes mintaparancsfájl feltételezi, hogy a parancssori ügyfél egy virtuális gépen fut, amelyen engedélyezve vannak az Azure-erőforrások felügyelt identitásai. Használja a virtuális gép "Connect" funkciót az Azure Portalon, távolról csatlakozhat a virtuális géphez. Az Azure-erőforrások felügyelt identitásainak virtuális gépeken való engedélyezéséről az [Azure-erőforrások felügyelt identitásainak konfigurálása az Azure Portalon,](qs-configure-portal-windows-vm.md)vagy az egyik változatcikk (PowerShell, CLI, sablon vagy Egy Azure SDK használatával) című témakörben olvashat. 
> - Az erőforrás-hozzáférés során fellépő hibák megelőzése érdekében a virtuális gép felügyelt identitásának legalább "Reader" hozzáférést kell biztosítani a megfelelő hatókörön (a virtuális gép vagy újabb) az Azure Resource Manager-műveletek engedélyezéséhez a virtuális gépen. A részletekért [lásd: Felügyelt identitások hozzárendelése az Azure-erőforrásokhoz az Azure Portalon keresztül egy erőforráshoz](howto-assign-access-portal.md) való hozzáférés.

## <a name="overview"></a>Áttekintés

Felügyelt identitások az Azure-erőforrások egy [egyszerű szolgáltatás objektumot,](../develop/developer-glossary.md#service-principal-object) amely akkor jön létre, amely az [Azure-erőforrások felügyelt identitások](overview.md#how-does-the-managed-identities-for-azure-resources-work) engedélyezése a virtuális gépen. Az egyszerű szolgáltatás azure-erőforrásokhoz való hozzáférést biztosít, és parancsfájl/parancssori ügyfelek által a bejelentkezéshez és az erőforrás-hozzáféréshez identitásként használható. A védett erőforrások saját identitása alatt való eléréséhez hagyományosan a parancsfájl-ügyfélnek a következőket kell a következőkre:  

   - regisztrálható és beleegyezett az Azure AD-hez bizalmas/webügyfél-alkalmazásként
   - bejelentkezés az egyszerű szolgáltatás alatt az alkalmazás hitelesítő adataival (amelyek valószínűleg be vannak ágyazva a parancsfájlba)

Az Azure-erőforrások felügyelt identitások, a parancsfájl-ügyfél már nem kell tennie sem, mivel betud jelentkezni a felügyelt identitások az Azure resources service principal. 

## <a name="azure-cli"></a>Azure CLI

A következő szkript bemutatja, hogyan kell:

1. Jelentkezzen be az Azure AD-be a virtuális gép felügyelt identitása alatt az Azure-erőforrások egyszerű szolgáltatásához  
2. Hívja fel az Azure Resource Managert, és vegye le a virtuális gép egyszerű szolgáltatásazonosítóját. A CLI gondoskodik a tokenek beszerzésének/használatának automatikus kezeléséről. Ügyeljen arra, hogy a `<VM-NAME>`virtuális gép nevét helyettesítse a.  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

A következő szkript bemutatja, hogyan kell:

1. Jelentkezzen be az Azure AD-be a virtuális gép felügyelt identitása alatt az Azure-erőforrások egyszerű szolgáltatásához  
2. Hívjon fel egy Azure Resource Manager-parancsmacst a virtuális gépről. A PowerShell gondoskodik a tokenhasználat automatikus kezeléséről.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Az Azure-szolgáltatások erőforrás-azonosítói

Tekintse meg az [Azure-szolgáltatásokat, amelyek támogatják az Azure AD-hitelesítést](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) az Azure AD-t támogató és felügyelt identitásokkal az Azure-erőforrásokkal és a megfelelő erőforrás-azonosítókkal tesztelt erőforrások listájáért.

## <a name="error-handling-guidance"></a>Hibakezelési útmutató 

Az alábbihoz hasonló válaszok azt jelezhetik, hogy a virtuális gép felügyelt identitása az Azure-erőforrásokhoz nincs megfelelően konfigurálva:

- PowerShell: *Meghívás-WebRequest : Nem lehet csatlakozni a távoli kiszolgálóhoz*
- CLI: *MSI: Nem sikerült lekérni egy jogkivonatot a következő `http://localhost:50342/oauth2/token` hibával: "HTTPConnectionPool(host='localhost', port=50342)* 

Ha a következő hibák valamelyikét kapja, térjen vissza az Azure-portálon az [Azure-szolgáltatásba,](https://portal.azure.com) és:

- Lépjen az **Identitás lapra,** és győződjön meg arról, hogy a **rendszer hozzárendelt** beállítása "Igen" lesz.
- Lépjen a **Bővítmények** lapra, és győződjön meg arról, hogy az Azure-erőforrások felügyelt identitásai **bővítmény (2019 januárjában tervezett eprecation)** sikeresen üzembe helyezése.

Ha bármelyik helytelen, előfordulhat, hogy újra kell telepítenie az Azure-erőforrások felügyelt identitásait az erőforráson, vagy hibaelhárítást kell elhárítania. Lásd: [Felügyelt identitások konfigurálása az Azure-erőforrások egy virtuális gép az Azure Portalon,](qs-configure-portal-windows-vm.md) ha segítségre van szüksége a virtuális gép konfigurációját.

## <a name="next-steps"></a>További lépések

- Ha engedélyezni szeretné az Azure-erőforrások felügyelt identitásait egy Azure virtuális gépen, olvassa [el az Azure-erőforrások felügyelt identitásának konfigurálása Egy Azure-beli virtuális gépen a PowerShell használatával](qs-configure-powershell-windows-vm.md)című témakört, vagy az [Azure-beli virtuális gépeken az Azure-beli virtuális gépeken felügyelt identitások konfigurálása.](qs-configure-cli-windows-vm.md)






