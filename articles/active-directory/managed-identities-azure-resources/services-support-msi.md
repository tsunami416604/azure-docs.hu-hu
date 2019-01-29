---
title: Azure-szolgáltatások, amelyek támogatják a felügyelt identitások az Azure-erőforrásokhoz
description: Felügyelt identitások Azure-erőforrások és az Azure AD-hitelesítést támogató szolgáltatások listája
services: active-directory
author: priyamohanram
ms.author: priyamo
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: daveba
ms.openlocfilehash: 2ca70805a0806078613bb37ac1d2836dd8d626c8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180738"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Az Azure-erőforrások felügyelt identitást támogató szolgáltatások

Felügyelt identitások az Azure-erőforrások Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást adja meg. Egy felügyelt identitás használata esetén elvégezheti a hitelesítést minden olyan szolgáltatás, amely támogatja az Azure AD-hitelesítés hitelesítő adatok nélkül a kódban. Jelenleg is zajlik integráló felügyelt identitások Azure-erőforrások és az Azure AD-hitelesítés, Azure-ban. Ellenőrizze gyakran frissítéseit.

> [!NOTE]
> Az Azure-erőforrások felügyelt identitásai a Managed Service Identity (MSI) szolgáltatás új neve.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Az Azure-erőforrások felügyelt identitásait támogató Azure-szolgáltatások

Az alábbi Azure-szolgáltatások támogatják a felügyelt identitások az Azure-erőforrások:

### <a name="azure-virtual-machines"></a>Azure-alapú virtuális gépek

|Felügyelt identitás típusa |  Általánosan elérhető<br>Globális Azure-régiók | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Rendszerhez rendelt | Elérhető | Előzetes verzió | Előzetes verzió | Előzetes verzió | Előzetes verzió |
| Felhasználóhoz rendelt | Előzetes verzió | Előzetes verzió | Előzetes verzió | Előzetes verzió | Előzetes verzió

Tekintse meg az alábbi lista a felügyelt identitás konfigurálása az Azure Virtual Machines (régióban, ha elérhetők):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Az Azure Resource Manager-sablonok](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Az Azure Virtual Machine Scale Sets

|Felügyelt identitás típusa |  Általánosan elérhető<br>Globális Azure-régiók | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Rendszerhez rendelt | Elérhető | Előzetes verzió | Előzetes verzió | Előzetes verzió |
| Felhasználóhoz rendelt | Előzetes verzió | Előzetes verzió | Előzetes verzió | Előzetes verzió

Tekintse meg az alábbi lista a felügyelt identitás konfigurálása az Azure Virtual Machine Scale Sets (régióban, ha elérhetők):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Az Azure Resource Manager-sablonok](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

|Felügyelt identitás típusa |  Általánosan elérhető<br>Globális Azure-régiók | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Rendszerhez rendelt | Elérhető | Elérhető | Elérhető | Elérhető |
| Felhasználóhoz rendelt | Előzetes verzió | Nem érhető el | Nem érhető el | Nem érhető el

Tekintse meg az alábbi lista a felügyelt identitás konfigurálása az Azure App Service-ben (régióban, ha elérhetők):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-sablon](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-functions"></a>Azure Functions

Felügyelt identitás típusa |  Általánosan elérhető<br>Globális Azure-régiók | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Rendszerhez rendelt | Elérhető | Elérhető | Elérhető | Elérhető |
| Felhasználóhoz rendelt | Előzetes verzió | Nem érhető el | Nem érhető el | Nem érhető el

Tekintse meg az alábbi lista a felügyelt identitás konfigurálása az Azure Functions (régióban, ha elérhetők):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-sablon](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Felügyelt identitás típusa |  Általánosan elérhető<br>Globális Azure-régiók | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Rendszerhez rendelt | Elérhető | Elérhető | Elérhető | Elérhető |
| Felhasználóhoz rendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el

Tekintse meg az alábbi lista a felügyelt identitás konfigurálása az Azure Logic Apps (régióban, ha elérhetők):

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Azure Resource Manager-sablon](/azure/app-service/overview-managed-identity#deployment-template)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Felügyelt identitás típusa |  Általánosan elérhető<br>Globális Azure-régiók | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Rendszerhez rendelt | Elérhető | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználóhoz rendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el

Tekintse meg az alábbi lista a felügyelt identitás konfigurálása az Azure Data Factory V2 (régióban, ha elérhetők):

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

Felügyelt identitás típusa |  Általánosan elérhető<br>Globális Azure-régiók | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Rendszerhez rendelt | Elérhető | Elérhető | Nem érhető el | Nem érhető el |
| Felhasználóhoz rendelt | Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el

Tekintse meg az alábbi lista a felügyelt identitás konfigurálása az Azure API Management (régióban, ha elérhetők):

- [Azure Resource Manager-sablon](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Felügyelt identitás típusa |  Általánosan elérhető<br>Globális Azure-régiók | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Rendszerhez rendelt | Linux: Előzetes verzió<br>Windows: Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el |
| Felhasználóhoz rendelt | Linux: Előzetes verzió<br>Windows: Nem érhető el | Nem érhető el | Nem érhető el | Nem érhető el

Tekintse meg az alábbi lista a felügyelt identitás konfigurálása az Azure Container Instances (régióban, ha elérhetők):

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager-sablon](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-szolgáltatások, hogy a támogatás az Azure AD-hitelesítés

A következő szolgáltatásokat az Azure AD-hitelesítés támogatásához, és az Azure-erőforrások felügyelt identitást használó ügyfél szolgáltatások teszteltük.

| Szolgáltatás | Erőforrás-azonosító | status | Hozzáférés hozzárendelése |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | `https://management.azure.com/` | Elérhető | [Azure Portal](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Azure CLI](howto-assign-access-CLI.md) <br>[Azure Resource Manager-sablon](../../role-based-access-control/role-assignments-template.md) |
| Azure Key Vault | `https://vault.azure.net` | Elérhető |  
| Azure Data Lake | `https://datalake.azure.net/` | Elérhető |
| Azure SQL | `https://database.windows.net/` | Elérhető |
| Azure Event Hubs | `https://eventhubs.azure.net` | Előzetes verzió |
| Azure Service Bus | `https://servicebus.azure.net` | Előzetes verzió |
| Azure Storage | `https://storage.azure.com/` | Előzetes verzió |
