---
title: Azure-szolgáltatásokat, amely támogatja a felügyelt identitás
description: Szolgáltatásidentitás felügyelt és az Azure AD-alapú hitelesítés támogató szolgáltatások listája
services: active-directory
author: daveba
ms.author: daveba
ms.date: 03/28/2018
ms.topic: reference
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: 2d896b11ae94355eb2bdcfa8bc3a647f96fd8caf
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34723762"
---
# <a name="services-that-support-managed-service-identity"></a>Felügyelt Szolgáltatásidentitás támogató szolgáltatások 

Felügyelt Szolgáltatásidentitást az Azure Active Directoryban automatikusan felügyelt identitással Azure szolgáltatásokat biztosít. Egy felügyelt identitást használja, elvégezheti a hitelesítést bármely szolgáltatás, amely támogatja az Azure AD-alapú hitelesítés anélkül, hogy a hitelesítő adatokat a kódban. Végezzük MSI és az Azure AD hitelesítési integrálása az Azure között. Ellenőrizze gyakran frissítéseit.

## <a name="azure-services-that-support-managed-service-identity"></a>Azure-szolgáltatásokat, amely támogatja a felügyelt identitás

Az Azure-szolgáltatásokat támogatja a felügyelt szolgáltatás identitást.

| Szolgáltatás | status | Dátum | Konfigurálás | A jogkivonat beolvasása |
| ------- | ------ | ---- | --------- | ----------- |
| Azure-alapú virtuális gépek | Előzetes verzió | 2017. szeptember | [Azure Portal](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[Azure CLI](qs-configure-cli-windows-vm.md)<br>[Az Azure Resource Manager-sablonok](qs-configure-template-windows-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Azure App Service | Előzetes verzió | 2017. szeptember | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure Resource Manager-sablon](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Functions | Előzetes verzió | 2017. szeptember | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure Resource Manager-sablon](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Data Factory V2 | Előzetes verzió | 2017. november | [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |


## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-szolgáltatások, a támogatás az Azure AD-hitelesítés

A következő szolgáltatásokat támogatja az Azure AD-alapú hitelesítés, és felügyelt Szolgáltatásidentitás használó ügyfél szolgáltatással lettek tesztelve.

| Szolgáltatás | Erőforrás-azonosító | status | Dátum | Hozzáférés hozzárendelése |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com/ | Elérhető | 2017. szeptember | [Azure Portal](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Azure CLI](howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net | Elérhető | 2017. szeptember | |
| Azure Data Lake | https://datalake.azure.net/ | Elérhető | 2017. szeptember | |
| Azure SQL | https://database.windows.net/ | Elérhető | 2017. október | |
| Azure Event Hubs | https://eventhubs.azure.net | Elérhető | 2017. december | |
| Azure Service Bus | https://servicebus.azure.net | Elérhető | 2017. december | |
| Azure Storage | https://storage.azure.com/ | Előzetes verzió | 2018. május | |
