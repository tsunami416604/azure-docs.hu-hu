---
title: Felügyelt identitásokat támogató Azure-szolgáltatások – Azure AD
description: Az Azure-erőforrások és az Azure AD-hitelesítés felügyelt identitásait támogató szolgáltatások listája
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0576a70b1b345d31ffc11c55f7fa5cbd288acd5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246447"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Az Azure-erőforrások felügyelt identitásait támogató szolgáltatások

Az Azure-erőforrások felügyelt identitásai Azure-szolgáltatásokat biztosítanak a Azure Active Directory automatikusan felügyelt identitással. Felügyelt identitás használatával bármely olyan szolgáltatás hitelesítése végezhető, amely támogatja az Azure AD-hitelesítést, és nem rendelkezik hitelesítő adatokkal a kódban. Folyamatban van a felügyelt identitások integrálása az Azure-erőforrások és az Azure AD-hitelesítés között az Azure-ban. Térjen vissza gyakran a frissítésekhez.

> [!NOTE]
> Az Azure-erőforrások felügyelt identitásai a Managed Service Identity (MSI) szolgáltatás új neve.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Az Azure-erőforrások felügyelt identitásait támogató Azure-szolgáltatások

A következő Azure-szolgáltatások támogatják az Azure-erőforrások felügyelt identitásait:

### <a name="azure-virtual-machines"></a>Azure-alapú virtuális gépek

| Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer hozzárendelve | Elérhető | Előzetes verzió | Előzetes verzió | Előzetes verzió | 
| Felhasználó által hozzárendelt | Elérhető | Előzetes verzió | Előzetes verzió | Előzetes verzió |

Tekintse át az alábbi listát az Azure Virtual Machines felügyelt identitásának konfigurálásához (az elérhető régiókban):

- [Azure Portalra](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablonok](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure-Virtual Machine Scale Sets

|Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer hozzárendelve | Elérhető | Előzetes verzió | Előzetes verzió | Előzetes verzió |
| Felhasználó által hozzárendelt | Elérhető | Előzetes verzió | Előzetes verzió | Előzetes verzió |

Tekintse át az alábbi listát az Azure Virtual Machine Scale Sets felügyelt identitásának konfigurálásához (az elérhető régiókban):

- [Azure Portalra](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablonok](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer hozzárendelve | Elérhető | Elérhető | Elérhető | Elérhető |
| Felhasználó által hozzárendelt | Elérhető | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse át az alábbi listát a felügyelt identitás konfigurálásához Azure App Service (a régiókban, ahol elérhető):

- [Azure Portalra](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-sablon](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprints

|Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer hozzárendelve | Elérhető | Elérhető | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Elérhető | Elérhető | Nem érhető el | Nem érhető el |

Tekintse át az alábbi listát a felügyelt identitás [Azure-tervezetekkel](../../governance/blueprints/overview.md)való használatához:

- [Azure Portal – tervezet-hozzárendelés](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API – tervezet-hozzárendelés](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Azure Functions

Felügyelt identitás típusa |Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer hozzárendelve | Elérhető | Elérhető | Elérhető | Elérhető |
| Felhasználó által hozzárendelt | Elérhető | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse át az alábbi listát a felügyelt identitás konfigurálásához Azure Functions (a régiókban, ahol elérhető):

- [Azure Portalra](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-sablon](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer hozzárendelve | Előzetes verzió | Előzetes verzió | Nem érhető el | Előzetes verzió |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse át az alábbi listát a felügyelt identitás konfigurálásához Azure Logic Apps (a régiókban, ahol elérhető):

- [Azure Portalra](/azure/logic-apps/create-managed-service-identity#enable-system-assigned-identity-in-azure-portal)
- [Azure Resource Manager-sablon](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer hozzárendelve | Elérhető | Elérhető | Nem érhető el | Elérhető |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse át az alábbi listát a felügyelt identitás konfigurálásához Azure Data Factory v2 (a régiókban, ahol elérhető):

- [Azure Portalra](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer hozzárendelve | Elérhető | Elérhető | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse át az alábbi listát az Azure API Management felügyelt identitásának konfigurálásához (az elérhető régiókban):

- [Azure Resource Manager-sablon](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer hozzárendelve | Linux: előzetes verzió<br>Windows: nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Linux: előzetes verzió<br>Windows: nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse át az alábbi listát a felügyelt identitás konfigurálásához Azure Container Instances (a régiókban, ahol elérhető):

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager-sablon](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Azure Container Registry Tasks

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer hozzárendelve | Elérhető | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Előzetes verzió | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse át az alábbi listát a felügyelt identitás konfigurálásához Azure Container Registry feladatokhoz (a régiókban, ahol elérhető):

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-service-fabric"></a>Azure Service Fabric
[Service Fabric alkalmazások felügyelt identitása](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) előzetes verzióban érhető el, és minden régióban elérhető.

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer hozzárendelve | Elérhető | Nem érhető el | Nem érhető el | nem érhető el |
| Felhasználó által hozzárendelt | Elérhető | Nem érhető el | Nem érhető el |Nem érhető el |

A következő listában megtekintheti az Azure Service Fabric-alkalmazások felügyelt identitásának konfigurálását az összes régióban:
- [Azure Resource Manager-sablon](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure AD-hitelesítést támogató Azure-szolgáltatások

A következő szolgáltatások támogatják az Azure AD-hitelesítést, és az Azure-erőforrásokhoz felügyelt identitásokat használó ügyféloldali szolgáltatásokkal lettek tesztelve.

### <a name="azure-resource-manager"></a>Azure Resource Manager

A Azure Resource Manager elérésének konfigurálásához tekintse meg a következő listát:

- [Hozzáférés kiosztása Azure Portal használatával](howto-assign-access-portal.md)
- [Hozzáférés kiosztása a PowerShell használatával](howto-assign-access-powershell.md)
- [Hozzáférés kiosztása az Azure CLI-n keresztül](howto-assign-access-CLI.md)
- [Hozzáférés kiosztása Azure Resource Manager sablonon keresztül](../../role-based-access-control/role-assignments-template.md)

| Felhő | Erőforrás-azonosító | status |
|--------|------------|--------|
| Azure globális | `https://management.azure.com/`| Elérhető |
| Azure Government | `https://management.usgovcloudapi.net/` | Elérhető |
| Azure Germany | `https://management.microsoftazure.de/` | Elérhető |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | Elérhető |

### <a name="azure-key-vault"></a>Azure Key Vault

| Felhő | Erőforrás-azonosító | status |
|--------|------------|--------|
| Azure globális | `https://vault.azure.net`| Elérhető |
| Azure Government | `https://vault.usgovcloudapi.net` | Elérhető |
| Azure Germany |  `https://vault.microsoftazure.de` | Elérhető |
| Azure China 21Vianet | `https://vault.azure.cn` | Elérhető |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Felhő | Erőforrás-azonosító | status |
|--------|------------|--------|
| Azure globális | `https://datalake.azure.net/` | Elérhető |
| Azure Government |  | Nem érhető el |
| Azure Germany |   | Nem érhető el |
| Azure China 21Vianet |  | Nem érhető el |

### <a name="azure-sql"></a>Azure SQL 

| Felhő | Erőforrás-azonosító | status |
|--------|------------|--------|
| Azure globális | `https://database.windows.net/` | Elérhető |
| Azure Government | `https://database.usgovcloudapi.net/` | Elérhető |
| Azure Germany | `https://database.cloudapi.de/` | Elérhető |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | Elérhető |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Felhő | Erőforrás-azonosító | status |
|--------|------------|--------|
| Azure globális | `https://eventhubs.azure.net` | Elérhető |
| Azure Government |  | Nem érhető el |
| Azure Germany |   | Nem érhető el |
| Azure China 21Vianet |  | Nem érhető el |

### <a name="azure-service-bus"></a>Azure Service Bus

| Felhő | Erőforrás-azonosító | status |
|--------|------------|--------|
| Azure globális | `https://servicebus.azure.net`  | Elérhető |
| Azure Government |  | Elérhető |
| Azure Germany |   | Nem érhető el |
| Azure China 21Vianet |  | Nem érhető el |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage-blobok és-várólisták

| Felhő | Erőforrás-azonosító | status |
|--------|------------|--------|
| Azure globális | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Elérhető |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | Elérhető |
| Azure Germany | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | Elérhető |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | Elérhető |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| Felhő | Erőforrás-azonosító | status |
|--------|------------|--------|
| Azure globális | `https://*.asazure.windows.net` | Elérhető |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | Elérhető |
| Azure Germany | `https://*.asazure.cloudapi.de` | Elérhető |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | Elérhető |
