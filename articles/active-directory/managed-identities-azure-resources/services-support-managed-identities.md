---
title: Azure-szolgáltatások, amelyek támogatják a felügyelt identitások az Azure-erőforrásokhoz
description: Felügyelt identitások Azure-erőforrások és az Azure AD-hitelesítést támogató szolgáltatások listája
services: active-directory
author: MarkusVi
ms.author: priyamo
ms.date: 05/09/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0809423472bde0240145d43f4c28d9594b517928
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65518688"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Az Azure-erőforrások felügyelt identitást támogató szolgáltatások

Felügyelt identitások az Azure-erőforrások Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást adja meg. Egy felügyelt identitás használata esetén elvégezheti a hitelesítést minden olyan szolgáltatás, amely támogatja az Azure AD-hitelesítés hitelesítő adatok nélkül a kódban. Jelenleg is zajlik integráló felügyelt identitások Azure-erőforrások és az Azure AD-hitelesítés, Azure-ban. Ellenőrizze gyakran frissítéseit.

> [!NOTE]
> Az Azure-erőforrások felügyelt identitásai a Managed Service Identity (MSI) szolgáltatás új neve.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Az Azure-erőforrások felügyelt identitásait támogató Azure-szolgáltatások

Az alábbi Azure-szolgáltatások támogatják a felügyelt identitások az Azure-erőforrások:

### <a name="azure-virtual-machines"></a>Azure-alapú virtuális gépek

| Felügyelt identitás típusa | Általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer által hozzárendelt | Elérhető | Előzetes verzió | Előzetes verzió | Előzetes verzió | 
| Hozzárendelt felhasználó | Előzetes verzió | Előzetes verzió | Előzetes verzió | Előzetes verzió |

Tekintse meg az alábbi lista a felügyelt identitás konfigurálása az Azure Virtual Machines (régióban, ha elérhetők):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Az Azure Resource Manager-sablonok](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Az Azure Virtual Machine Scale Sets

|Felügyelt identitás típusa | Általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer által hozzárendelt | Elérhető | Előzetes verzió | Előzetes verzió | Előzetes verzió |
| Hozzárendelt felhasználó | Előzetes verzió | Előzetes verzió | Előzetes verzió | Előzetes verzió |

Tekintse meg az alábbi lista a felügyelt identitás konfigurálása az Azure Virtual Machine Scale Sets (régióban, ha elérhetők):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Az Azure Resource Manager-sablonok](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| Felügyelt identitás típusa | Általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer által hozzárendelt | Elérhető | Elérhető | Elérhető | Elérhető |
| Hozzárendelt felhasználó | Előzetes verzió | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse meg az alábbi lista a felügyelt identitás konfigurálása az Azure App Service-ben (régióban, ha elérhetők):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-sablon](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprints

|Felügyelt identitás típusa | Általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer által hozzárendelt | Előzetes verzió | Nem érhető el | Nem érhető el | Nem érhető el |
| Hozzárendelt felhasználó | Előzetes verzió | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse meg az alábbi lista a felügyelt identitás használatára [Azure tervezetek](../../governance/blueprints/overview.md):

- [Az Azure portal – a tervezet-hozzárendelést](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API - tervezet-hozzárendelést](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Azure Functions

Felügyelt identitás típusa |Általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer által hozzárendelt | Elérhető | Elérhető | Elérhető | Elérhető |
| Hozzárendelt felhasználó | Előzetes verzió | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse meg az alábbi lista a felügyelt identitás konfigurálása az Azure Functions (régióban, ha elérhetők):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-sablon](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Felügyelt identitás típusa | Általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer által hozzárendelt | Előzetes verzió | Előzetes verzió | Nem érhető el | Előzetes verzió |
| Hozzárendelt felhasználó | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse meg az alábbi lista a felügyelt identitás konfigurálása az Azure Logic Apps (régióban, ha elérhetők):

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Azure Resource Manager-sablon](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Felügyelt identitás típusa | Általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer által hozzárendelt | Elérhető | Nem érhető el | Nem érhető el | Nem érhető el |
| Hozzárendelt felhasználó | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse meg az alábbi lista a felügyelt identitás konfigurálása az Azure Data Factory V2 (régióban, ha elérhetők):

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

Felügyelt identitás típusa | Általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer által hozzárendelt | Elérhető | Elérhető | Nem érhető el | Nem érhető el |
| Hozzárendelt felhasználó | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse meg az alábbi lista a felügyelt identitás konfigurálása az Azure API Management (régióban, ha elérhetők):

- [Azure Resource Manager-sablon](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Felügyelt identitás típusa | Általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer által hozzárendelt | Linux: Előzetes verzió<br>Windows: Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |
| Hozzárendelt felhasználó | Linux: Előzetes verzió<br>Windows: Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse meg az alábbi lista a felügyelt identitás konfigurálása az Azure Container Instances (régióban, ha elérhetők):

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager-sablon](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-szolgáltatások, hogy a támogatás az Azure AD-hitelesítés

A következő szolgáltatásokat az Azure AD-hitelesítés támogatásához, és az Azure-erőforrások felügyelt identitást használó ügyfél szolgáltatások teszteltük.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Tekintse meg az alábbi lista az Azure Resource Manager-hozzáférés konfigurálása:

- [Az Azure Portalon keresztül hozzáférés hozzárendelése](howto-assign-access-portal.md)
- [PowerShell-lel hozzáférés hozzárendelése](howto-assign-access-powershell.md)
- [Azure CLI-n keresztül hozzáférés hozzárendelése](howto-assign-access-CLI.md)
- [Az Azure Resource Manager-sablon keresztüli hozzáférés hozzárendelése](../../role-based-access-control/role-assignments-template.md)

| Felhő | Erőforrás-azonosító | Állapot |
|--------|------------|--------|
| Azure Global | `https://management.azure.com/`| Elérhető |
| Azure Government | `https://management.usgovcloudapi.net/` | Elérhető |
| Azure Germany | `https://management.microsoftazure.de/` | Elérhető |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | Elérhető |

### <a name="azure-key-vault"></a>Azure Key Vault

| Felhő | Erőforrás-azonosító | Állapot |
|--------|------------|--------|
| Azure Global | `https://vault.azure.net`| Elérhető |
| Azure Government | `https://vault.usgovcloudapi.net` | Elérhető |
| Azure Germany |  `https://vault.microsoftazure.de` | Elérhető |
| Azure China 21Vianet | `https://vault.azure.cn` | Elérhető |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Felhő | Erőforrás-azonosító | Állapot |
|--------|------------|--------|
| Azure Global | `https://datalake.azure.net/` | Elérhető |
| Azure Government |  | Nincs |
| Azure Germany |   | Nincs |
| Azure China 21Vianet |  | Nincs |

### <a name="azure-sql"></a>Azure SQL 

| Felhő | Erőforrás-azonosító | Állapot |
|--------|------------|--------|
| Azure Global | `https://database.windows.net/` | Elérhető |
| Azure Government | `https://database.usgovcloudapi.net/` | Elérhető |
| Azure Germany | `https://database.cloudapi.de/` | Elérhető |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | Elérhető |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Felhő | Erőforrás-azonosító | Állapot |
|--------|------------|--------|
| Azure Global | `https://eventhubs.azure.net` | Előzetes verzió |
| Azure Government |  | Nincs |
| Azure Germany |   | Nincs |
| Azure China 21Vianet |  | Nincs |

### <a name="azure-service-bus"></a>Azure Service Bus

| Felhő | Erőforrás-azonosító | Állapot |
|--------|------------|--------|
| Azure Global | `https://servicebus.azure.net`  | Előzetes verzió |
| Azure Government |  | Nincs |
| Azure Germany |   | Nincs |
| Azure China 21Vianet |  | Nincs |

### <a name="azure-storage-blobs-and-queues"></a>Az Azure Storage-blobok és üzenetsorok

| Felhő | Erőforrás-azonosító | Állapot |
|--------|------------|--------|
| Azure Global | `https://storage.azure.com/` | Elérhető |
| Azure Government | `https://storage.azure.com/` | Elérhető |
| Azure Germany | `https://storage.azure.com/` | Elérhető |
| Azure China 21Vianet | `https://storage.azure.com/` | Elérhető |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Felhő | Erőforrás-azonosító | Állapot |
|--------|------------|--------|
| Azure Global | `https://*.asazure.windows.net` | Elérhető |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | Elérhető |
| Azure Germany | `https://*.asazure.cloudapi.de` | Elérhető |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | Elérhető |