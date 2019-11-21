---
title: Azure Services that support managed identities - Azure AD
description: List of services that support managed identities for Azure resources and Azure AD authentication
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 09/24/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ead9b53f530a309d6bdb3bd384c29650bf5c8e6
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224306"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Services that support managed identities for Azure resources

Managed identities for Azure resources provide Azure services with an automatically managed identity in Azure Active Directory. Using a managed identity, you can authenticate to any service that supports Azure AD authentication without having credentials in your code. We are in the process of integrating managed identities for Azure resources and Azure AD authentication across Azure. Check back often for updates.

> [!NOTE]
> Az Azure-erőforrások felügyelt identitásai a Managed Service Identity (MSI) szolgáltatás új neve.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Az Azure-erőforrások felügyelt identitásait támogató Azure-szolgáltatások

The following Azure services support managed identities for Azure resources:

### <a name="azure-virtual-machines"></a>Azure-alapú virtuális gépek

| Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Elérhető | Előzetes verzió | Előzetes verzió | Előzetes verzió | 
| User assigned | Elérhető | Előzetes verzió | Előzetes verzió | Előzetes verzió |

Refer to the following list to configure managed identity for Azure Virtual Machines (in regions where available):

- [Azure Portalra](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager templates](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Elérhető | Előzetes verzió | Előzetes verzió | Előzetes verzió |
| User assigned | Elérhető | Előzetes verzió | Előzetes verzió | Előzetes verzió |

Refer to the following list to configure managed identity for Azure Virtual Machine Scale Sets (in regions where available):

- [Azure Portalra](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager templates](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Elérhető | Elérhető | Elérhető | Elérhető |
| User assigned | Elérhető | Nincs | Nincs | Nincs |

Refer to the following list to configure managed identity for Azure App Service (in regions where available):

- [Azure Portalra](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-sablon](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprints

|Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Elérhető | Elérhető | Nincs | Nincs |
| User assigned | Elérhető | Elérhető | Nincs | Nincs |

Refer to the following list to use a managed identity with [Azure Blueprints](../../governance/blueprints/overview.md):

- [Azure portal - blueprint assignment](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API - blueprint assignment](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Azure Functions

Managed identity type |All Generally Available<br>Global Azure Regions | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Elérhető | Elérhető | Elérhető | Elérhető |
| User assigned | Elérhető | Nincs | Nincs | Nincs |

Refer to the following list to configure managed identity for Azure Functions (in regions where available):

- [Azure Portalra](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-sablon](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Előzetes verzió | Előzetes verzió | Nincs | Előzetes verzió |
| User assigned | Nincs | Nincs | Nincs | Nincs |

Refer to the following list to configure managed identity for Azure Logic Apps (in regions where available):

- [Azure Portalra](/azure/logic-apps/create-managed-service-identity#azure-portal-system-logic-app)
- [Azure Resource Manager-sablon](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Elérhető | Nincs | Nincs | Nincs |
| User assigned | Nincs | Nincs | Nincs | Nincs |

Refer to the following list to configure managed identity for Azure Data Factory V2 (in regions where available):

- [Azure Portalra](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Elérhető | Elérhető | Nincs | Nincs |
| User assigned | Nincs | Nincs | Nincs | Nincs |

Refer to the following list to configure managed identity for Azure API Management (in regions where available):

- [Azure Resource Manager-sablon](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Linux: Preview<br>Windows: Not available | Nincs | Nincs | Nincs |
| User assigned | Linux: Preview<br>Windows: Not available | Nincs | Nincs | Nincs |

Refer to the following list to configure managed identity for Azure Container Instances (in regions where available):

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager-sablon](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Azure Container Registry Tasks

Managed identity type | All Generally Available<br>Global Azure Regions | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| System assigned | Elérhető | Nincs | Nincs | Nincs |
| User assigned | Előzetes verzió | Nincs | Nincs | Nincs |

Refer to the following list to configure managed identity for Azure Container Registry Tasks (in regions where available):

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure services that support Azure AD authentication

The following services support Azure AD authentication, and have been tested with client services that use managed identities for Azure resources.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Refer to the following list to configure access to Azure Resource Manager:

- [Assign access via Azure portal](howto-assign-access-portal.md)
- [Assign access via Powershell](howto-assign-access-powershell.md)
- [Assign access via Azure CLI](howto-assign-access-CLI.md)
- [Assign access via Azure Resource Manager template](../../role-based-access-control/role-assignments-template.md)

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|--------|
| Azure Global | `https://management.azure.com/`| Elérhető |
| Azure Government | `https://management.usgovcloudapi.net/` | Elérhető |
| Azure Germany | `https://management.microsoftazure.de/` | Elérhető |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | Elérhető |

### <a name="azure-key-vault"></a>Azure Key Vault

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|--------|
| Azure Global | `https://vault.azure.net`| Elérhető |
| Azure Government | `https://vault.usgovcloudapi.net` | Elérhető |
| Azure Germany |  `https://vault.microsoftazure.de` | Elérhető |
| Azure China 21Vianet | `https://vault.azure.cn` | Elérhető |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|--------|
| Azure Global | `https://datalake.azure.net/` | Elérhető |
| Azure Government |  | Nincs |
| Azure Germany |   | Nincs |
| Azure China 21Vianet |  | Nincs |

### <a name="azure-sql"></a>Azure SQL 

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|--------|
| Azure Global | `https://database.windows.net/` | Elérhető |
| Azure Government | `https://database.usgovcloudapi.net/` | Elérhető |
| Azure Germany | `https://database.cloudapi.de/` | Elérhető |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | Elérhető |

### <a name="azure-event-hubs"></a>Azure Event Hubs-eseményközpontok

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|--------|
| Azure Global | `https://eventhubs.azure.net` | Elérhető |
| Azure Government |  | Nincs |
| Azure Germany |   | Nincs |
| Azure China 21Vianet |  | Nincs |

### <a name="azure-service-bus"></a>Azure Service Bus

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|--------|
| Azure Global | `https://servicebus.azure.net`  | Elérhető |
| Azure Government |  | Elérhető |
| Azure Germany |   | Nincs |
| Azure China 21Vianet |  | Nincs |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage blobs and queues

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|--------|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Elérhető |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | Elérhető |
| Azure Germany | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | Elérhető |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | Elérhető |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| Felhőbeli | Erőforrás-azonosító | Állapot |
|--------|------------|--------|
| Azure Global | `https://*.asazure.windows.net` | Elérhető |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | Elérhető |
| Azure Germany | `https://*.asazure.cloudapi.de` | Elérhető |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | Elérhető |
