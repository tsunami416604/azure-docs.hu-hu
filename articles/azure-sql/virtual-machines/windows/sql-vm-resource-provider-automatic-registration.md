---
title: Automatikus regisztráció az SQL VM erőforrás-szolgáltatóval
description: Ebből a témakörből megtudhatja, hogyan engedélyezheti az automatikus regisztrációs funkciót, hogy a Azure Portal használatával automatikusan regisztrálja az összes múltbeli és jövőbeli SQL Server virtuális gépet az SQL VM erőforrás-szolgáltatóval.
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/21/2020
ms.openlocfilehash: 45285f2f26f1f17408f97bfede2b97e4c4752a5c
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762470"
---
# <a name="automatic-registration-with-sql-vm-resource-provider"></a>Automatikus regisztráció az SQL VM erőforrás-szolgáltatóval
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

A Azure Portal automatikus regisztráció funkciójának engedélyezésével automatikusan regisztrálhatja az Azure-beli virtuális gépek összes aktuális és jövőbeli SQL Serverát az SQL VM erőforrás-szolgáltatóval, könnyű módban.

Ebből a cikkből megtudhatja, hogyan engedélyezheti az automatikus regisztrálási funkciót. Azt is megteheti, hogy [egyetlen virtuális gépet regisztrál](sql-vm-resource-provider-register.md), vagy a virtuális [gépeket tömegesen regisztrálja](sql-vm-resource-provider-bulk-register.md) az SQL VM erőforrás-szolgáltatóval. 

## <a name="overview"></a>Áttekintés

Az [SQL VM erőforrás-szolgáltató](sql-vm-resource-provider-register.md#overview) lehetővé teszi a SQL Server VM kezelését a Azure Portal. Emellett az erőforrás-szolgáltató egy robusztus szolgáltatáskészlet használatát is lehetővé teszi, beleértve az [automatikus javítást](automated-patching.md), az [automatikus biztonsági mentést](automated-backup.md), valamint a figyelési és kezelhetőségi képességeket. Emellett feloldja a [licencelési](licensing-model-azure-hybrid-benefit-ahb-change.md) és a [kiadási](change-sql-server-edition.md) rugalmasságot is. Ezek a funkciók korábban csak az Azure Marketplace-en üzembe helyezett lemezképek SQL Server VM voltak elérhetők. 

Az automatikus regisztrálási funkció lehetővé teszi, hogy az ügyfelek automatikusan regisztrálják az összes aktuális és jövőbeli SQL Server virtuális gépet az Azure-előfizetésében az SQL VM erőforrás-szolgáltatóval. Ez különbözik a manuális regisztrációtól, amely csak a jelenlegi SQL Server virtuális gépekre koncentrál. 

Az automatikus regisztráció egyszerű módban regisztrálja a SQL Server virtuális gépeket. Továbbra is [manuálisan kell frissítenie a teljes kezelhetőségi módra](sql-vm-resource-provider-register.md#upgrade-to-full) , hogy kihasználhassa a teljes szolgáltatáskészlet előnyeit. 

## <a name="prerequisites"></a>Előfeltételek

A SQL Server VM erőforrás-szolgáltatóval való regisztrálásához a következőkre lesz szüksége: 

- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- Egy Azure-beli erőforrás-modell [Windows rendszerű virtuális gép](../../../virtual-machines/windows/quick-create-portal.md) , amelynek [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) központilag telepítve van a nyilvános vagy Azure Government felhőben. 


## <a name="enable"></a>Engedélyezés

Ha engedélyezni szeretné a SQL Server virtuális gépek automatikus regisztrációját a Azure Portalban, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Navigáljon az [**SQL Virtual Machines**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) erőforrás oldalára. 
1. Válassza az **automatikus SQL Server VM regisztráció** lehetőséget az **automatikus regisztráció** lap megnyitásához. 

   :::image type="content" source="media/sql-vm-resource-provider-automatic-registration/automatic-registration.png" alt-text="Az automatikus regisztráció lap megnyitásához válassza az automatikus SQL Server VM regisztráció lehetőséget.":::

1. Válassza ki az előfizetést a legördülő menüből. 
1. Olvassa el a feltételeket, és ha elfogadja, válassza az **Elfogadom**lehetőséget. 
1. Válassza a **regisztráció** lehetőséget a funkció engedélyezéséhez és az összes jelenlegi és jövőbeli SQL Server virtuális gép automatikus regisztrálásához az SQL VM erőforrás-szolgáltatóval. Ez nem indítja újra a SQL Server szolgáltatást a virtuális gépeken. 

## <a name="disable"></a>Letiltás

Az automatikus regisztrációs funkció letiltásához használja az [Azure CLI](/cli/azure/install-azure-cli) -t vagy a [Azure PowerShell](/powershell/azure/install-az-ps) . Ha az automatikus regisztrálási funkció le van tiltva, SQL Server az előfizetéshez hozzáadott virtuális gépeket manuálisan kell regisztrálni az SQL VM erőforrás-szolgáltatóban. Ez nem törli a már regisztrált meglévő SQL Server virtuális gépek regisztrációját.



# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha le szeretné tiltani az automatikus regisztrációt az Azure CLI használatával, futtassa a következő parancsot: 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha Azure PowerShell használatával szeretné letiltani az automatikus regisztrációt, futtassa a következő parancsot: 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---


## <a name="next-steps"></a>Következő lépések

A kezelhetőségi módot [teljes mértékben](sql-vm-resource-provider-register.md#upgrade-to-full) kihasználva kihasználhatja az SQL VM erőforrás-szolgáltató által biztosított összes szolgáltatáskészlet előnyeit. 
