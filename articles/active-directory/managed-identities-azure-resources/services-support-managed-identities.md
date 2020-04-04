---
title: Felügyelt identitásokat támogató Azure-szolgáltatások – Azure AD
description: Az Azure-erőforrások felügyelt identitásait és az Azure AD-hitelesítést támogató szolgáltatások listája
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 04/03/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c7a65df100cd58561ce12ac2ae01281eebd419a
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656051"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Felügyelt identitásokat támogató szolgáltatások az Azure-erőforrásokhoz

Az Azure-erőforrások felügyelt identitásai automatikusan felügyelt identitást biztosítanak az Azure-szolgáltatások számára az Azure Active Directoryban. Felügyelt identitás használatával hitelesítheti magát minden olyan szolgáltatás, amely támogatja az Azure AD-hitelesítést anélkül, hogy hitelesítő adatokat a kódot. Folyamatban van az Azure-erőforrások és az Azure AD-hitelesítés felügyelt identitásainak integrálása az Azure-ban. Látogasson vissza gyakran a frissítéseket.

> [!NOTE]
> Az Azure-erőforrások felügyelt identitásai a Managed Service Identity (MSI) szolgáltatás új neve.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Az Azure-erőforrások felügyelt identitásait támogató Azure-szolgáltatások

A következő Azure-szolgáltatások támogatják az Azure-erőforrások felügyelt identitásait:

### <a name="azure-virtual-machines"></a>Azure-alapú virtuális gépek

| Felügyelt identitás típusa | Minden általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| A rendszer hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Előzetes verzió | Előzetes verzió | 
| Felhasználó hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Előzetes verzió | Előzetes verzió |

Az alábbi lista az Azure virtuális gépek felügyelt identitásának konfigurálásához (ahol elérhető):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablonok](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|Felügyelt identitás típusa | Minden általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| A rendszer hozzárendelt | ![Elérhető][check] | Előzetes verzió | Előzetes verzió | Előzetes verzió |
| Felhasználó hozzárendelt | ![Elérhető][check] | Előzetes verzió | Előzetes verzió | Előzetes verzió |

Az alábbi listában konfigurálhatja az Azure virtuálisgép-méretezési készletek felügyelt identitását (ahol elérhető):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablonok](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| Felügyelt identitás típusa | Minden általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| A rendszer hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] |
| Felhasználó hozzárendelt | ![Elérhető][check] | ![Elérhető][check]  | ![Elérhető][check]  | ![Elérhető][check] |

Az alábbi listában konfigurálhatja az Azure App Service felügyelt identitását (ahol elérhető:

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-sablon](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprints

|Felügyelt identitás típusa | Minden általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| A rendszer hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem érhető el | Nem érhető el |
| Felhasználó hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem érhető el | Nem érhető el |

Tekintse meg az alábbi listát, ha felügyelt identitást szeretne használni az [Azure Blueprints-szel:](../../governance/blueprints/overview.md)

- [Azure Portal – tervezethozzárendelés](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API – tervezethozzárendelés](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Azure Functions

Felügyelt identitás típusa |Minden általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| A rendszer hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] |
| Felhasználó hozzárendelt | ![Elérhető][check] | ![Elérhető][check]  | ![Elérhető][check]  | ![Elérhető][check]  |

Az alábbi listában konfigurálhatja az Azure Functions felügyelt identitását (ahol elérhető:

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-sablon](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Felügyelt identitás típusa | Minden általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| A rendszer hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem érhető el | ![Elérhető][check] |
| Felhasználó hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem érhető el | ![Elérhető][check] |


Az alábbi listában konfigurálhatja az Azure Logic Apps felügyelt identitását (ahol elérhető régiókban):

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#enable-system-assigned-identity-in-azure-portal)
- [Azure Resource Manager-sablon](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Felügyelt identitás típusa | Minden általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| A rendszer hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem érhető el | ![Elérhető][check] |
| Felhasználó hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Az alábbi listában konfigurálhatja az Azure Data Factory V2 felügyelt identitását (ahol elérhető:

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

Felügyelt identitás típusa | Minden általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| A rendszer hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem érhető el | ![Elérhető][check] |
| Felhasználó hozzárendelt | Előzetes verzió | Előzetes verzió | Nem érhető el | Előzetes verzió |

Az alábbi listában konfigurálhatja az Azure API Management felügyelt identitását (ahol elérhető:

- [Azure Resource Manager-sablon](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Felügyelt identitás típusa | Minden általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| A rendszer hozzárendelt | Linux: Előnézet<br>Windows: Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó hozzárendelt | Linux: Előnézet<br>Windows: Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Az alábbi listában konfigurálhatja az Azure Container Instances felügyelt identitását (ahol elérhető régiókban):

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager-sablon](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Azure Container Registry Tasks

Felügyelt identitás típusa | Minden általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| A rendszer hozzárendelt | ![Elérhető][check] | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó hozzárendelt | Előzetes verzió | Nem érhető el | Nem érhető el | Nem érhető el |

Az alábbi listában konfigurálhatja az Azure Container registry feladatok felügyelt identitását (ahol elérhető:

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-service-fabric"></a>Azure Service Fabric
[A Service Fabric-alkalmazások felügyelt identitása](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) előzetes verzióban érhető el, és minden régióban elérhető.

Felügyelt identitás típusa | Minden általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| A rendszer hozzárendelt | ![Elérhető][check] | Nem érhető el | Nem érhető el | nem érhető el |
| Felhasználó hozzárendelt | ![Elérhető][check] | Nem érhető el | Nem érhető el |Nem érhető el |

Az alábbi listában konfigurálhatja az Azure Service Fabric-alkalmazások felügyelt identitását az összes régióban:
- [Azure Resource Manager-sablon](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Az Azure AD-hitelesítést támogató Azure-szolgáltatások

A következő szolgáltatások támogatják az Azure AD-hitelesítést, és tesztelték az Azure-erőforrások felügyelt identitásokat használó ügyfélszolgáltatásokkal.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Az Azure Resource Manager elérésének konfigurálásához az alábbi listában tájékozott:

- [Hozzáférés hozzárendelése az Azure Portalon keresztül](howto-assign-access-portal.md)
- [Hozzáférés hozzárendelése a PowerShellen keresztül](howto-assign-access-powershell.md)
- [Hozzáférés hozzárendelése az Azure CLI-n keresztül](howto-assign-access-CLI.md)
- [Hozzáférés hozzárendelése az Azure Resource Manager sablonon keresztül](../../role-based-access-control/role-assignments-template.md)

| Felhő | Erőforrás-azonosító | status |
|--------|------------|:-:|
| Azure Globális | `https://management.azure.com/`| ![Elérhető][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![Elérhető][check] |
| Azure Germany | `https://management.microsoftazure.de/` | ![Elérhető][check] |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | ![Elérhető][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| Felhő | Erőforrás-azonosító | status |
|--------|------------|:-:|
| Azure Globális | `https://vault.azure.net`| ![Elérhető][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![Elérhető][check] |
| Azure Germany |  `https://vault.microsoftazure.de` | ![Elérhető][check] |
| Azure China 21Vianet | `https://vault.azure.cn` | ![Elérhető][check] |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Felhő | Erőforrás-azonosító | status |
|--------|------------|:-:|
| Azure Globális | `https://datalake.azure.net/` | ![Elérhető][check] |
| Azure Government |  | Nem érhető el |
| Azure Germany |   | Nem érhető el |
| Azure China 21Vianet |  | Nem érhető el |

### <a name="azure-sql"></a>Azure SQL 

| Felhő | Erőforrás-azonosító | status |
|--------|------------|:-:|
| Azure Globális | `https://database.windows.net/` | ![Elérhető][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![Elérhető][check] |
| Azure Germany | `https://database.cloudapi.de/` | ![Elérhető][check] |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | ![Elérhető][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Felhő | Erőforrás-azonosító | status |
|--------|------------|:-:|
| Azure Globális | `https://eventhubs.azure.net` | ![Elérhető][check] |
| Azure Government |  | Nem érhető el |
| Azure Germany |   | Nem érhető el |
| Azure China 21Vianet |  | Nem érhető el |

### <a name="azure-service-bus"></a>Azure Service Bus

| Felhő | Erőforrás-azonosító | status |
|--------|------------|:-:|
| Azure Globális | `https://servicebus.azure.net`  | ![Elérhető][check] |
| Azure Government |  | ![Elérhető][check] |
| Azure Germany |   | Nem érhető el |
| Azure China 21Vianet |  | Nem érhető el |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage-blobok és -várólisták

| Felhő | Erőforrás-azonosító | status |
|--------|------------|:-:|
| Azure Globális | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Elérhető][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Elérhető][check] |
| Azure Germany | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Elérhető][check] |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Elérhető][check] |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| Felhő | Erőforrás-azonosító | status |
|--------|------------|:-:|
| Azure Globális | `https://*.asazure.windows.net` | ![Elérhető][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![Elérhető][check] |
| Azure Germany | `https://*.asazure.cloudapi.de` | ![Elérhető][check] |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![Elérhető][check] |


[check]: media/services-support-managed-identities/check.png "Elérhető"
