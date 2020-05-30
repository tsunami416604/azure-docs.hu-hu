---
title: Felügyelt identitásokat támogató Azure-szolgáltatások – Azure AD
description: Az Azure-erőforrások és az Azure AD-hitelesítés felügyelt identitásait támogató szolgáltatások listája
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 05/12/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.custom: references_regions
ms.openlocfilehash: 62d1f8d0e6fb5b6b6afb28f5db4ae8b818cc4705
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84194860"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Az Azure-erőforrások felügyelt identitásait támogató szolgáltatások

Az Azure-erőforrások felügyelt identitásai Azure-szolgáltatásokat biztosítanak a Azure Active Directory automatikusan felügyelt identitással. Felügyelt identitás használatával bármely olyan szolgáltatás hitelesítése végezhető, amely támogatja az Azure AD-hitelesítést, és nem rendelkezik hitelesítő adatokkal a kódban. Folyamatban van a felügyelt identitások integrálása az Azure-erőforrások és az Azure AD-hitelesítés között az Azure-ban. Térjen vissza gyakran a frissítésekhez.

> [!NOTE]
> Az Azure-erőforrások felügyelt identitásai a Managed Service Identity (MSI) szolgáltatás új neve.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Az Azure-erőforrások felügyelt identitásait támogató Azure-szolgáltatások

A következő Azure-szolgáltatások támogatják az Azure-erőforrások felügyelt identitásait:


### <a name="azure-api-management"></a>Azure API Management

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer hozzárendelve | ![Elérhető][check] | ![Elérhető][check] | Nem érhető el | ![Elérhető][check] |
| Felhasználó által hozzárendelt | Előnézet | Előnézet | Nem érhető el | Előnézet |

Tekintse át az alábbi listát az Azure API Management felügyelt identitásának konfigurálásához (az elérhető régiókban):

- [Azure Resource Manager sablon](/azure/api-management/api-management-howto-use-managed-service-identity)


### <a name="azure-app-service"></a>Azure App Service

| Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer hozzárendelve | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] |
| Felhasználó által hozzárendelt | ![Elérhető][check] | ![Elérhető][check]  | ![Elérhető][check]  | ![Elérhető][check] |

Tekintse át az alábbi listát a felügyelt identitás konfigurálásához Azure App Service (a régiókban, ahol elérhető):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager sablon](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)


### <a name="azure-blueprints"></a>Azure Blueprints

|Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer hozzárendelve | ![Elérhető][check] | ![Elérhető][check] | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem érhető el | Nem érhető el |

Tekintse át az alábbi listát a felügyelt identitás [Azure-tervezetekkel](../../governance/blueprints/overview.md)való használatához:

- [Azure Portal – tervezet-hozzárendelés](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API – tervezet-hozzárendelés](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)


### <a name="azure-container-instances"></a>Azure Container Instances

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer hozzárendelve | Linux: előzetes verzió<br>Windows: nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Linux: előzetes verzió<br>Windows: nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse át az alábbi listát a felügyelt identitás konfigurálásához Azure Container Instances (a régiókban, ahol elérhető):

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager sablon](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


### <a name="azure-container-registry-tasks"></a>Azure Container Registry Tasks

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer hozzárendelve | ![Elérhető][check] | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználó által hozzárendelt | Előnézet | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse át az alábbi listát a felügyelt identitás konfigurálásához Azure Container Registry feladatokhoz (a régiókban, ahol elérhető):

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)


### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer hozzárendelve | ![Elérhető][check] | ![Elérhető][check] | Nem érhető el | ![Elérhető][check] |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse át az alábbi listát a felügyelt identitás konfigurálásához Azure Data Factory v2 (a régiókban, ahol elérhető):

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)


### <a name="azure-functions"></a>Azure Functions

Felügyelt identitás típusa |Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer hozzárendelve | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] | ![Elérhető][check] |
| Felhasználó által hozzárendelt | ![Elérhető][check] | ![Elérhető][check]  | ![Elérhető][check]  | ![Elérhető][check]  |

Tekintse át az alábbi listát a felügyelt identitás konfigurálásához Azure Functions (a régiókban, ahol elérhető):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager sablon](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-iot-hub"></a>Azure IoT Hub

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer hozzárendelve | ![Elérhető][check] | ![Elérhető][check] | Nem érhető el | ![Elérhető][check] |
| Felhasználó által hozzárendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |

Tekintse át az alábbi listát a felügyelt identitás konfigurálásához Azure Data Factory v2 (a régiókban, ahol elérhető):

- [Azure Portal](../../iot-hub/virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

| Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer hozzárendelve | ![Elérhető][check] | - | - | - | 
| Felhasználó által hozzárendelt | ![Elérhető][check] | - | - | - |


További információ: [felügyelt identitások használata az Azure Kubernetes szolgáltatásban](https://docs.microsoft.com/azure/aks/use-managed-identity).


### <a name="azure-logic-apps"></a>Azure Logic Apps

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer hozzárendelve | ![Elérhető][check] | ![Elérhető][check] | Nem érhető el | ![Elérhető][check] |
| Felhasználó által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Nem érhető el | ![Elérhető][check] |


Tekintse át az alábbi listát a felügyelt identitás konfigurálásához Azure Logic Apps (a régiókban, ahol elérhető):

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#enable-system-assigned-identity-in-azure-portal)
- [Azure Resource Manager sablon](https://docs.microsoft.com/azure/logic-apps/logic-apps-azure-resource-manager-templates-overview)


### <a name="azure-service-fabric"></a>Azure Service Fabric
[Service Fabric alkalmazások felügyelt identitása](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) előzetes verzióban érhető el, és minden régióban elérhető.

Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer hozzárendelve | ![Elérhető][check] | Nem érhető el | Nem érhető el | nem érhető el |
| Felhasználó által hozzárendelt | ![Elérhető][check] | Nem érhető el | Nem érhető el |Nem érhető el |

A következő listában megtekintheti az Azure Service Fabric-alkalmazások felügyelt identitásának konfigurálását az összes régióban:
- [Azure Resource Manager sablon](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)



### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer hozzárendelve | ![Elérhető][check] | Előnézet | Előnézet | Előnézet |
| Felhasználó által hozzárendelt | ![Elérhető][check] | Előnézet | Előnézet | Előnézet |

Tekintse át az alábbi listát az Azure Virtual Machine Scale Sets felügyelt identitásának konfigurálásához (az elérhető régiókban):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablonok](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)



### <a name="azure-virtual-machines"></a>Azure-alapú virtuális gépek

| Felügyelt identitás típusa | Az összes általánosan elérhető<br>Globális Azure-régiók | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Rendszer hozzárendelve | ![Elérhető][check] | ![Elérhető][check] | Előnézet | Előnézet | 
| Felhasználó által hozzárendelt | ![Elérhető][check] | ![Elérhető][check] | Előnézet | Előnézet |

Tekintse át az alábbi listát az Azure Virtual Machines felügyelt identitásának konfigurálásához (az elérhető régiókban):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablonok](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)




## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure AD-hitelesítést támogató Azure-szolgáltatások

A következő szolgáltatások támogatják az Azure AD-hitelesítést, és az Azure-erőforrásokhoz felügyelt identitásokat használó ügyféloldali szolgáltatásokkal lettek tesztelve.

### <a name="azure-resource-manager"></a>Azure Resource Manager

A Azure Resource Manager elérésének konfigurálásához tekintse meg a következő listát:

- [Hozzáférés kiosztása Azure Portal használatával](howto-assign-access-portal.md)
- [Hozzáférés kiosztása a PowerShell használatával](howto-assign-access-powershell.md)
- [Hozzáférés kiosztása az Azure CLI-n keresztül](howto-assign-access-CLI.md)
- [Hozzáférés kiosztása Azure Resource Manager sablonon keresztül](../../role-based-access-control/role-assignments-template.md)

| Felhő | Erőforrás-azonosító | Állapot |
|--------|------------|:-:|
| Azure globális | `https://management.azure.com/`| ![Elérhető][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![Elérhető][check] |
| Azure Germany | `https://management.microsoftazure.de/` | ![Elérhető][check] |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | ![Elérhető][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| Felhő | Erőforrás-azonosító | Állapot |
|--------|------------|:-:|
| Azure globális | `https://vault.azure.net`| ![Elérhető][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![Elérhető][check] |
| Azure Germany |  `https://vault.microsoftazure.de` | ![Elérhető][check] |
| Azure China 21Vianet | `https://vault.azure.cn` | ![Elérhető][check] |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Felhő | Erőforrás-azonosító | Állapot |
|--------|------------|:-:|
| Azure globális | `https://datalake.azure.net/` | ![Elérhető][check] |
| Azure Government |  | Nem érhető el |
| Azure Germany |   | Nem érhető el |
| Azure China 21Vianet |  | Nem érhető el |

### <a name="azure-sql"></a>Azure SQL 

| Felhő | Erőforrás-azonosító | Állapot |
|--------|------------|:-:|
| Azure globális | `https://database.windows.net/` | ![Elérhető][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![Elérhető][check] |
| Azure Germany | `https://database.cloudapi.de/` | ![Elérhető][check] |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | ![Elérhető][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Felhő | Erőforrás-azonosító | Állapot |
|--------|------------|:-:|
| Azure globális | `https://eventhubs.azure.net` | ![Elérhető][check] |
| Azure Government |  | Nem érhető el |
| Azure Germany |   | Nem érhető el |
| Azure China 21Vianet |  | Nem érhető el |

### <a name="azure-service-bus"></a>Azure Service Bus

| Felhő | Erőforrás-azonosító | Állapot |
|--------|------------|:-:|
| Azure globális | `https://servicebus.azure.net`  | ![Elérhető][check] |
| Azure Government |  | ![Elérhető][check] |
| Azure Germany |   | Nem érhető el |
| Azure China 21Vianet |  | Nem érhető el |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage-blobok és-várólisták

| Felhő | Erőforrás-azonosító | Állapot |
|--------|------------|:-:|
| Azure globális | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Elérhető][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Elérhető][check] |
| Azure Germany | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Elérhető][check] |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Elérhető][check] |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| Felhő | Erőforrás-azonosító | Állapot |
|--------|------------|:-:|
| Azure globális | `https://*.asazure.windows.net` | ![Elérhető][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![Elérhető][check] |
| Azure Germany | `https://*.asazure.cloudapi.de` | ![Elérhető][check] |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![Elérhető][check] |

> [!Note]
> A Microsoft Power BI [támogatja a felügyelt identitásokat](https://docs.microsoft.com/azure/stream-analytics/powerbi-output-managed-identity)is.


[check]: media/services-support-managed-identities/check.png "Elérhető"
