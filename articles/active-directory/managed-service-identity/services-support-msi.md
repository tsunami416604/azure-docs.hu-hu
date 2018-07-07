---
title: Azure-szolgáltatások, amelyek támogatják a Felügyeltszolgáltatás-identitás
description: Felügyeltszolgáltatás-identitást, és az Azure AD-hitelesítést támogató szolgáltatások listája
services: active-directory
author: daveba
ms.author: daveba
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: cdef4cf16fc688b61f367a77267be821ee84bf56
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902010"
---
# <a name="services-that-support-managed-service-identity"></a>A Felügyeltszolgáltatás-identitást támogató szolgáltatások 

Felügyeltszolgáltatás-identitás az Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Egy felügyelt identitás használata esetén elvégezheti a hitelesítést minden olyan szolgáltatás, amely támogatja az Azure AD-hitelesítés hitelesítő adatok nélkül a kódban. Jelenleg folyamatban MSI és az Azure AD-hitelesítés integrálása az Azure-ban. Ellenőrizze gyakran frissítéseit.

## <a name="azure-services-that-support-managed-service-identity"></a>Azure-szolgáltatások, amelyek támogatják a Felügyeltszolgáltatás-identitás

Az alábbi Azure-szolgáltatásokat támogatja a Felügyeltszolgáltatás-identitást.

| Szolgáltatás | status | Dátum | Konfigurálás | Egy token beszerzése |
| ------- | ------ | ---- | --------- | ----------- |
| Azure-alapú virtuális gépek | Előzetes verzió | 2017. szeptember | [Azure Portal](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[Azure CLI](qs-configure-cli-windows-vm.md)<br>[Az Azure Resource Manager-sablonok](qs-configure-template-windows-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Azure App Service | Elérhető | 2018. június | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure CLI](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Azure Resource Manager-sablon](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell)  |
| Azure Functions | Elérhető | 2018. június | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure CLI](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Azure Resource Manager-sablon](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell) |
| Azure Data Factory V2 | Előzetes verzió | 2017. november | [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |


## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-szolgáltatások, hogy a támogatás az Azure AD-hitelesítés

A következő szolgáltatásokat az Azure AD-hitelesítés támogatásához, és a Felügyeltszolgáltatás-identitást használó ügyfél szolgáltatások teszteltük.

| Szolgáltatás | Erőforrás-azonosító | status | Dátum | Hozzáférés hozzárendelése |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com/ | Elérhető | 2017. szeptember | [Azure Portal](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Azure CLI](howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net | Elérhető | 2017. szeptember | |
| Azure Data Lake | https://datalake.azure.net/ | Elérhető | 2017. szeptember | |
| Azure SQL | https://database.windows.net/ | Elérhető | 2017. október | |
| Azure Event Hubs | https://eventhubs.azure.net | Elérhető | 2017. december | |
| Azure Service Bus | https://servicebus.azure.net | Elérhető | 2017. december | |
| Azure Storage | https://storage.azure.com/ | Előzetes verzió | 2018. május | |
