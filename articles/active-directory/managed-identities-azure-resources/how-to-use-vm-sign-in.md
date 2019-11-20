---
title: Felügyelt identitások használata Azure-beli virtuális gépen bejelentkezéshez – Azure AD
description: Részletes utasítások és példák az Azure-beli virtuális gépek felügyelt identitásának használatára az Azure-erőforrások egyszerű üzembe helyezéséhez a parancsfájl-ügyfél bejelentkezéséhez és az erőforrás-hozzáféréshez.
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
ms.openlocfilehash: e3d6d128677d2e82f4750a7771885474bf284fb1
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184218"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Azure-erőforrások felügyelt identitásának használata Azure-beli virtuális gépen bejelentkezéshez 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Ez a cikk PowerShell-és CLI-parancsfájl-példákat tartalmaz a bejelentkezéshez az Azure-erőforrások egyszerű szolgáltatásához felügyelt identitások használatával, valamint útmutatást nyújt a fontos témakörökhöz, például a hibák kezeléséhez.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Ha a cikkben a Azure PowerShell vagy az Azure CLI-példákat is használni szeretné, telepítse az [Azure PowerShell](/powershell/azure/install-az-ps) vagy az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)legújabb verzióját. 

> [!IMPORTANT]
> - A cikkben szereplő összes minta parancsfájl azt feltételezi, hogy a parancssori ügyfél olyan virtuális gépen fut, amelyen engedélyezve van az Azure-erőforrások felügyelt identitása. Használja a Azure Portal virtuális gép "kapcsolódás" szolgáltatását a virtuális géphez való távoli kapcsolódáshoz. A virtuális gépeken található Azure-erőforrások felügyelt identitásának engedélyezésével kapcsolatos részletekért lásd: [felügyelt identitások konfigurálása egy virtuális gépen az Azure-erőforrások számára a Azure Portal](qs-configure-portal-windows-vm.md)vagy az egyik varianting article (POWERSHELL, CLI, sablon vagy Azure SDK használatával). 
> - Az erőforrás-hozzáférés során felmerülő hibák megelőzése érdekében a virtuális gép felügyelt identitásának legalább "olvasó" hozzáférési jogosultsággal kell rendelkeznie a megfelelő hatókörben (a virtuális gépen vagy magasabb szinten), hogy engedélyezze a virtuális gép Azure Resource Manager műveleteit. További részletekért lásd: [felügyelt identitások társítása az Azure-erőforrásokhoz az erőforrásokhoz való hozzáféréshez a Azure Portal használatával](howto-assign-access-portal.md) .

## <a name="overview"></a>Áttekintés

Az Azure-erőforrások felügyelt identitásai egy [egyszerű szolgáltatásnév-objektumot](../develop/developer-glossary.md#service-principal-object) biztosítanak, amely az [Azure-erőforrások felügyelt identitásának engedélyezésével jön létre](overview.md#how-does-it-work) a virtuális gépen. Az egyszerű szolgáltatás hozzáférést biztosíthat az Azure-erőforrásokhoz, és identitásként használhatja a bejelentkezéshez és az erőforrás-hozzáféréshez parancsfájl-vagy parancssori ügyfelekként. Hagyományosan a saját identitása alá tartozó biztonságos erőforrások eléréséhez a parancsfájl-ügyfélnek a következőket kell tennie:  

   - regisztrálva kell lennie az Azure AD-ben, bizalmas/webes ügyfélalkalmazásként
   - Jelentkezzen be az egyszerű szolgáltatásnév alá, és használja az alkalmazás hitelesítő adatait (amelyek valószínűleg a szkriptbe vannak beágyazva)

Az Azure-erőforrásokhoz tartozó felügyelt identitások esetében a parancsfájl-ügyfélnek többé nem kell tennie, mert az Azure-erőforrások szolgáltatásnév felügyelt identitások területén tud bejelentkezni. 

## <a name="azure-cli"></a>Azure CLI

Az alábbi szkript a következőket mutatja be:

1. Jelentkezzen be az Azure AD-be a virtuális gép felügyelt identitása Azure-erőforrások egyszerű szolgáltatásához  
2. Hívja meg Azure Resource Manager és szerezze be a virtuális gép egyszerű szolgáltatásnév AZONOSÍTÓját. A CLI gondoskodik a jogkivonat-beszerzések és-használat automatikus kezeléséről. Ügyeljen rá, hogy a `<VM-NAME>`a virtuális gép nevét cserélje le.  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Az alábbi szkript a következőket mutatja be:

1. Jelentkezzen be az Azure AD-be a virtuális gép felügyelt identitása Azure-erőforrások egyszerű szolgáltatásához  
2. Hívja meg az Azure Resource Manager parancsmagot a virtuális géppel kapcsolatos információk lekéréséhez. A PowerShell gondoskodik a jogkivonat-használat automatikus kezeléséről.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Az Azure-szolgáltatások erőforrás-azonosítói

Tekintse meg az Azure ad- [hitelesítést támogató Azure-szolgáltatásokat](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) az Azure ad-t támogató erőforrások listájáért, valamint az Azure-erőforrások felügyelt identitásokkal való tesztelését, valamint a hozzájuk tartozó erőforrás-azonosítókat.

## <a name="error-handling-guidance"></a>Hiba a kezelési útmutatóban 

A következő válaszok például azt jelezhetik, hogy a virtuális gép felügyelt identitása nem lett megfelelően konfigurálva az Azure-erőforrásokhoz:

- PowerShell: *meghívó-webkérés: nem lehet csatlakozni a távoli kiszolgálóhoz*
- CLI: *MSI: nem sikerült beolvasni a tokent `http://localhost:50342/oauth2/token` "HTTPConnectionPool (host =" localhost ", Port = 50342) hibával* . 

Ha ezen hibák valamelyikét megkapja, térjen vissza az Azure-beli virtuális géphez a [Azure Portal](https://portal.azure.com) és:

- Lépjen az **Identity (identitás** ) lapra, és győződjön meg róla, hogy a **rendszer hozzárendelve** értéke "igen".
- Lépjen a **bővítmények** lapra, és győződjön meg arról, hogy a felügyelt identitások az Azure-erőforrások bővítményhez **(a 2019 januári verzióra tervezettek)** üzembe helyezése sikeresen megtörtént.

Ha valamelyik helytelen, lehetséges, hogy újra telepítenie kell az Azure-erőforrásokhoz tartozó felügyelt identitásokat az erőforráson, vagy hibaelhárítást kell végeznie az üzembe helyezési hiba miatt. Ha segítségre van szüksége a virtuálisgép-konfigurációval kapcsolatban, tekintse [meg a felügyelt identitások konfigurálása az Azure-erőforrásokhoz a Azure Portal segítségével](qs-configure-portal-windows-vm.md) című témakört.

## <a name="next-steps"></a>Következő lépések

- Azure-beli virtuális gépen lévő Azure-erőforrások felügyelt identitásának engedélyezéséhez lásd: [felügyelt identitások konfigurálása](qs-configure-powershell-windows-vm.md)Azure-beli virtuális gépen Azure-beli virtuális gépeken a PowerShell használatával, illetve felügyelt identitások konfigurálása Azure-beli virtuális gépeken az Azure [CLI használatával](qs-configure-cli-windows-vm.md)






