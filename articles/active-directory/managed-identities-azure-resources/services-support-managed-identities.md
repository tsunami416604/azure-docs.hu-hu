---
title: Az Azure-erőforrások felügyelt identitásait támogató Azure-szolgáltatások
description: Az Azure-erőforrások és az Azure AD-hitelesítés felügyelt identitásait támogató szolgáltatások listája
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 09/24/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0731510977c01b08d9aa557246dce3bd92b2f826
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473217"
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

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager sablonok](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure-Virtual Machine Scale Sets

|Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer hozzárendelve | Elérhető | Előzetes verzió | Előzetes verzió | Előzetes verzió |
| Felhasználó által hozzárendelt | Elérhető | Előzetes verzió | Előzetes verzió | Előzetes verzió |

Tekintse át az alábbi listát az Azure Virtual Machine Scale Sets felügyelt identitásának konfigurálásához (az elérhető régiókban):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager sablonok](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer hozzárendelve | Elérhető | Elérhető | Elérhető | Elérhető |
| Felhasználó által hozzárendelt | Elérhető | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse át az alábbi listát a felügyelt identitás konfigurálásához Azure App Service (a régiókban, ahol elérhető):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
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

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-sablon](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer hozzárendelve | Előzetes verzió | Előzetes verzió | Nem érhető el | Előzetes verzió |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse át az alábbi listát a felügyelt identitás konfigurálásához Azure Logic Apps (a régiókban, ahol elérhető):

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#azure-portal-system-logic-app)
- [Azure Resource Manager-sablon](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Rendszer hozzárendelve | Elérhető | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse át az alábbi listát a felügyelt identitás konfigurálásához Azure Data Factory v2 (a régiókban, ahol elérhető):

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
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

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure AD-hitelesítést támogató Azure-szolgáltatások

A következő szolgáltatások támogatják az Azure AD-hitelesítést, és az Azure-erőforrásokhoz felügyelt identitásokat használó ügyféloldali szolgáltatásokkal lettek tesztelve.

### <a name="azure-resource-manager"></a>Azure Resource Manager

A Azure Resource Manager elérésének konfigurálásához tekintse meg a következő listát:

- [Hozzáférés kiosztása Azure Portal használatával](howto-assign-access-portal.md)
- [Hozzáférés kiosztása a PowerShell használatával](howto-assign-access-powershell.md)
- [Hozzáférés kiosztása az Azure CLI-n keresztül](howto-assign-access-CLI.md)
- [Hozzáférés kiosztása Azure Resource Manager sablonon keresztül](../../role-based-access-control/role-assignments-template.md)

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|--------|
| Azure globális | `https://management.azure.com/`| Elérhető |
| Azure Government | `https://management.usgovcloudapi.net/` | Elérhető |
| Azure Germany | `https://management.microsoftazure.de/` | Elérhető |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | Elérhető |

### <a name="azure-key-vault"></a>Azure Key Vault

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|--------|
| Azure globális | `https://vault.azure.net`| Elérhető |
| Azure Government | `https://vault.usgovcloudapi.net` | Elérhető |
| Azure Germany |  `https://vault.microsoftazure.de` | Elérhető |
| Azure China 21Vianet | `https://vault.azure.cn` | Elérhető |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|--------|
| Azure globális | `https://datalake.azure.net/` | Elérhető |
| Azure Government |  | Nincs |
| Azure Germany |   | Nincs |
| Azure China 21Vianet |  | Nincs |

### <a name="azure-sql"></a>Azure SQL 

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|--------|
| Azure globális | `https://database.windows.net/` | Elérhető |
| Azure Government | `https://database.usgovcloudapi.net/` | Elérhető |
| Azure Germany | `https://database.cloudapi.de/` | Elérhető |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | Elérhető |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|--------|
| Azure globális | `https://eventhubs.azure.net` | Elérhető |
| Azure Government |  | Nincs |
| Azure Germany |   | Nincs |
| Azure China 21Vianet |  | Nincs |

### <a name="azure-service-bus"></a>Azure Service Bus

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|--------|
| Azure globális | `https://servicebus.azure.net`  | Elérhető |
| Azure Government |  | Elérhető |
| Azure Germany |   | Nincs |
| Azure China 21Vianet |  | Nincs |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage-blobok és-várólisták

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|--------|
| Azure globális | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Elérhető |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | Elérhető |
| Azure Germany | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | Elérhető |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | Elérhető |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|--------|
| Azure globális | `https://*.asazure.windows.net` | Elérhető |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | Elérhető |
| Azure Germany | `https://*.asazure.cloudapi.de` | Elérhető |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | Elérhető |
