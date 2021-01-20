---
title: Azure diagnosztikai monitorozás – Azure-igazolás
description: Azure diagnosztikai monitorozás az Azure-hoz – igazolás
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 40bc76f839cf6757b8f874112504249e611c3e1a
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/20/2021
ms.locfileid: "98606106"
---
# <a name="setting-up-diagnostics-with-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Diagnosztika beállítása az Azure igazolás platformmegbízhatósági modul (TPM) végpontján

Az Azure [platform-naplói](/azure/azure-monitor/platform/platform-logs-overview) , beleértve az Azure-tevékenység naplóját és az erőforrás-naplókat, részletes diagnosztikai és naplózási információkat biztosítanak az Azure-erőforrásokhoz és az Azure-platformtól függenek. A [platform metrikáit](/azure/azure-monitor/platform/data-platform-metrics) a rendszer alapértelmezés szerint gyűjti, és általában a Azure monitor metrikai adatbázisban tárolja. Ez a cikk a diagnosztikai beállítások létrehozásával és konfigurálásával kapcsolatos részletes információkat tartalmaz a platform metrikáinak és a platformok naplóinak különböző célhelyekre küldéséhez. 

A TPM-végpont szolgáltatás diagnosztikai beállítással van engedélyezve, és a tevékenységek figyelésére használható. Ha a TPM szolgáltatás végpontjának [Azure-figyelését](/azure/azure-monitor/overview) a PowerShell használatával szeretné beállítani, kövesse az alábbi lépéseket. 

Az Azure igazolási szolgáltatás beállítása. 

[Azure-igazolás beállítása Azure PowerShell](/azure/attestation/quickstart-powershell#:~:text=%20Quickstart%3A%20Set%20up%20Azure%20Attestation%20with%20Azure,Register%20Microsoft.Attestation%20resource%20provider.%20Register%20the...%20More%20)

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```
A tevékenységi naplók a Storage-fiók tárolók szakaszában találhatók. Részletes információ: az erőforrás- [naplók összegyűjtése egy Azure-erőforrásból, és a Azure monitor-Azure monitor elemzése](/azure/azure-monitor/learn/tutorial-resource-logs)
