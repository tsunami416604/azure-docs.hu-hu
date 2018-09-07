---
title: Azure-szolgáltatások, amelyek támogatják a felügyelt identitások az Azure-erőforrásokhoz
description: Felügyelt identitások Azure-erőforrások és az Azure AD-hitelesítést támogató szolgáltatások listája
services: active-directory
author: daveba
ms.author: daveba
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: 4f7e672aa75d8344f0c4ae35ff2f573d1f48a0ea
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028355"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Az Azure-erőforrások felügyelt identitást támogató szolgáltatások

Felügyelt identitások az Azure-erőforrások Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Egy felügyelt identitás használata esetén elvégezheti a hitelesítést minden olyan szolgáltatás, amely támogatja az Azure AD-hitelesítés hitelesítő adatok nélkül a kódban. Jelenleg is zajlik integráló felügyelt identitások Azure-erőforrások és az Azure AD-hitelesítés, Azure-ban. Ellenőrizze gyakran frissítéseit.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Az Azure-erőforrások felügyelt identitásait támogató Azure-szolgáltatások

Az alábbi Azure-szolgáltatások támogatják a felügyelt identitások az Azure-erőforrások:

| Szolgáltatás | Alapértelmezett állapot | felhasználó által hozzárendelt állapota| Konfigurálás | Egy token beszerzése |
| ------- | ------ | ---- | --------- | ----------- |
| Azure-alapú virtuális gépek | Előzetes verzió | Előzetes verzió | [Azure Portal](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[Azure CLI](qs-configure-cli-windows-vm.md)<br>[Az Azure Resource Manager-sablonok](qs-configure-template-windows-vm.md)<br>[REST](qs-configure-rest-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Virtual Machine Scale Sets | Előzetes verzió | Előzetes verzió | [Azure Portal](qs-configure-portal-windows-vmss.md)<br>[PowerShell](qs-configure-powershell-windows-vmss.md)<br>[Azure CLI](qs-configure-cli-windows-vmss.md)<br>[Az Azure Resource Manager-sablonok](qs-configure-template-windows-vmss.md)<br>[REST](qs-configure-rest-vmss.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell)
| Azure App Service | Elérhető | Nem érhető el | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure CLI](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Azure Resource Manager-sablon](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell)  |
| Azure Functions | Elérhető | Nem érhető el | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure CLI](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Azure Resource Manager-sablon](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell) |
| Azure Data Factory V2 | Előzetes verzió | Nem érhető el | [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |
| Azure API Management | Elérhető | Nem érhető el | [Azure Resource Manager-sablon](/azure/api-management/api-management-howto-use-managed-service-identity) | 


## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-szolgáltatások, hogy a támogatás az Azure AD-hitelesítés

A következő szolgáltatásokat az Azure AD-hitelesítés támogatásához, és az Azure-erőforrások felügyelt identitást használó ügyfél szolgáltatások teszteltük.

| Szolgáltatás | Erőforrás-azonosító | status | Dátum | Hozzáférés hozzárendelése |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com/ | Elérhető | 2017. szeptember | [Azure Portal](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Azure CLI](howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net | Elérhető | 2017. szeptember | |
| Azure Data Lake | https://datalake.azure.net/ | Elérhető | 2017. szeptember | |
| Azure SQL | https://database.windows.net/ | Elérhető | 2017. október | |
| Azure Event Hubs | https://eventhubs.azure.net | Elérhető | 2017. december | |
| Azure Service Bus | https://servicebus.azure.net | Elérhető | 2017. december | |
| Azure Storage | https://storage.azure.com/ | Előzetes verzió | 2018. május | |