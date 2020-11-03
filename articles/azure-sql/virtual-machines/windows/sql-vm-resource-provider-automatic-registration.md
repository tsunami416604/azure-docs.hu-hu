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
ms.openlocfilehash: 23ecc3bdfb0ca85caf219fc262348937923f53c3
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286128"
---
# <a name="automatic-registration-with-sql-vm-resource-provider"></a>Automatikus regisztráció az SQL VM erőforrás-szolgáltatóval
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

A Azure Portal automatikus regisztráció funkciójának engedélyezésével automatikusan regisztrálhatja az összes aktuális és jövőbeli SQL Server az Azure Virtual Machines (VM) szolgáltatásban az SQL VM erőforrás-szolgáltatóval egyszerűsített módban. Az SQL virtuális gép erőforrás-szolgáltatójának regisztrálása telepíti az [SQL IaaS-ügynök bővítményt](sql-server-iaas-agent-extension-automate-management.md).

Ebből a cikkből megtudhatja, hogyan engedélyezheti az automatikus regisztrálási funkciót. Azt is megteheti, hogy [egyetlen virtuális gépet regisztrál](sql-vm-resource-provider-register.md), vagy a virtuális [gépeket tömegesen regisztrálja](sql-vm-resource-provider-bulk-register.md) az SQL VM erőforrás-szolgáltatóval. 

## <a name="overview"></a>Áttekintés

A SQL Server VM regisztrálása az SQL VM erőforrás-szolgáltató telepítésekor az [SQL IaaS-ügynök bővítményt](sql-server-iaas-agent-extension-automate-management.md)telepíti. 

Ha engedélyezve van az automatikus regisztráció, a feladatok naponta futnak annak észleléséhez, hogy SQL Server telepítve van-e az előfizetésben lévő összes nem regisztrált virtuális gépre. Ezt úgy teheti meg, hogy az SQL IaaS-ügynök bővítmény bináris fájljait másolja a virtuális gépre, majd egy egyszeri segédprogramot futtat, amely a SQL Server beállításjegyzék-struktúrát ellenőrzi. Ha a rendszer a SQL Server struktúra észlelését észleli, a virtuális gép egyszerűsített módban van regisztrálva az [SQL VM erőforrás-szolgáltatóban](sql-vm-resource-provider-register.md) . Ha nem létezik SQL Server struktúra a beállításjegyzékben, a bináris fájlok törlődnek.

Ha engedélyezve van az automatikus regisztráció egy előfizetéshez, a SQL Server telepített összes jelenlegi és jövőbeli virtuális gép egyszerűsített módban lesz regisztrálva az SQL VM erőforrás-szolgáltatóban. Továbbra is [manuálisan kell frissítenie a teljes kezelhetőségi módra](sql-vm-resource-provider-register.md#upgrade-to-full) , hogy kihasználhassa a teljes szolgáltatáskészlet előnyeit. 

> [!IMPORTANT]
> Az SQL IaaS-ügynök bővítmény olyan adatokat gyűjt az expressz célra, amelyek az ügyfelek számára opcionális előnyöket biztosítanak SQL Server Azure-beli Virtual Machines való használatakor. A Microsoft ezeket az adatszolgáltatásokat nem használja fel a licencelési naplózáshoz az ügyfél előzetes belefoglalása nélkül. További információért tekintse meg a [SQL Server adatvédelmi kiegészítését](/sql/sql-server/sql-server-privacy#non-personal-data) .

## <a name="prerequisites"></a>Előfeltételek

A SQL Server VM erőforrás-szolgáltatóval való regisztrálásához a következőkre lesz szüksége: 

- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- Egy Azure Resource Model [Windows Server 2008 R2 (vagy újabb) rendszerű virtuális gép](../../../virtual-machines/windows/quick-create-portal.md) , amelyen [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) központilag vagy Azure Government felhőben üzembe helyezhetők. A Windows Server 2008 nem támogatott. 


## <a name="enable"></a>Engedélyezés

Ha engedélyezni szeretné a SQL Server virtuális gépek automatikus regisztrációját a Azure Portalban, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com).
1. Navigáljon az [**SQL Virtual Machines**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) erőforrás oldalára. 
1. Válassza az **automatikus SQL Server VM regisztráció** lehetőséget az **automatikus regisztráció** lap megnyitásához. 

   :::image type="content" source="media/sql-vm-resource-provider-automatic-registration/automatic-registration.png" alt-text="Az automatikus regisztráció lap megnyitásához válassza az automatikus SQL Server VM regisztráció lehetőséget.":::

1. Válassza ki az előfizetést a legördülő menüből. 
1. Olvassa el a feltételeket, és ha elfogadja, válassza az **Elfogadom** lehetőséget. 
1. Válassza a **regisztráció** lehetőséget a funkció engedélyezéséhez és az összes jelenlegi és jövőbeli SQL Server virtuális gép automatikus regisztrálásához az SQL VM erőforrás-szolgáltatóval. Ez nem indítja újra a SQL Server szolgáltatást a virtuális gépeken. 

## <a name="disable"></a>Letiltás

Az automatikus regisztrációs funkció letiltásához használja az [Azure CLI](/cli/azure/install-azure-cli) -t vagy a [Azure PowerShell](/powershell/azure/install-az-ps) . Ha az automatikus regisztrálási funkció le van tiltva, SQL Server az előfizetéshez hozzáadott virtuális gépeket manuálisan kell regisztrálni az SQL VM erőforrás-szolgáltatóban. Ez nem törli a már regisztrált meglévő SQL Server virtuális gépek regisztrációját.



# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha le szeretné tiltani az automatikus regisztrációt az Azure CLI használatával, futtassa a következő parancsot: 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Ha Azure PowerShell használatával szeretné letiltani az automatikus regisztrációt, futtassa a következő parancsot: 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="enable-for-multiple-subscriptions"></a>Több előfizetés engedélyezése

A PowerShell használatával több Azure-előfizetéshez is engedélyezheti az automatikus regisztráció funkciót. 

Ehhez kövesse az alábbi lépéseket:

1. Mentse a [parancsfájlt](https://github.com/microsoft/tigertoolbox/blob/master/AzureSQLVM/RegisterSubscriptionsToSqlVmAutomaticRegistration.ps1) `.ps1` fájlba, például: `EnableBySubscription.ps1` . 
1. Navigáljon arra a helyre, ahová a parancsfájlt rendszergazdai parancssor vagy PowerShell-ablak használatával mentette. 
1. Kapcsolódjon az Azure-hoz ( `az login` ).
1. Futtassa a szkriptet, és adja át a SubscriptionIds paraméterként, például:   
   `.\EnableBySubscription.ps1 -SubscriptionList SubscriptionId1,SubscriptionId2`

   Például: 

   ```console
   .\EnableBySubscription.ps1 -SubscriptionList a1a1a-aa11-11aa-a1a1-a11a111a1,b2b2b2-bb22-22bb-b2b2-b2b2b2bb
   ```

A sikertelen regisztrációs hibák tárolása `RegistrationErrors.csv` ugyanabban a címtárban történik, ahol mentette és futtatta a `.ps1` parancsfájlt. 

## <a name="next-steps"></a>Következő lépések

A kezelhetőségi módot [teljes mértékben](sql-vm-resource-provider-register.md#upgrade-to-full) kihasználva kihasználhatja az SQL VM erőforrás-szolgáltató által biztosított összes szolgáltatáskészlet előnyeit. 
