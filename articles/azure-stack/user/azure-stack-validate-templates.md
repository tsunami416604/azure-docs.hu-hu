---
title: "Sablon érvényesítő segítségével ellenőrizze a sablonok Azure verem |} Microsoft Docs"
description: "Sablonok az Azure-verem való központi telepítésének ellenőrzése"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: c30b0a78cf3421554cf8f7c887c7973c7b9f4b9c
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="check-your-templates-for-azure-stack-with-template-validator"></a>A sablonok Azure verem sablon érvényesítő és ellenőrzése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A sablon ellenőrzése eszköz segítségével ellenőrizze, hogy az Azure Resource Manager [sablonok](azure-stack-arm-templates.md) készen áll az Azure-verem. A sablon ellenőrzése eszköz az Azure-verem eszközök részeként érhető el. Töltse le az Azure-verem eszközök ismertetett lépések segítségével a [eszközök letölthető a GitHub](azure-stack-powershell-download.md) cikk. 

A JSON-fájl lévő és sablonok ellenőrzéséhez használja a következő PowerShell-modulok **TemplateValidator** és **CloudCapabilities** mappák: 

 - AzureRM.CloudCapabilities.psm1 létrehoz egy felhőalapú képességek JSON képviselő a szolgáltatások és a felhőben működő Azure verem például verziók.
 - AzureRM.TemplateValidator.psm1 sablonok központi telepítés tesztelése az Azure-verem felhőalapú képességek JSON-fájlt használ.
 - AzureStackCloudCapabilities_with_AddOns_20170627.json is a default cloud capabilities file.  Hozza létre a sajátját, vagy használja ezt a fájlt a kezdéshez. 

Ebben a témakörben érvényesítési futtatni a sablonokat, és opcionálisan a felhő capabilities-fájl létrehozása.

## <a name="validate-templates"></a>Sablonok ellenőrzése
Ezeket a lépéseket a sablonok AzureRM.TemplateValidator PowerShell modul használatával ellenőrzése. Használhatja a saját sablonokat, vagy ellenőrizze a [Azure verem gyorsindítási sablonok](https://github.com/Azure/AzureStack-QuickStart-Templates).

1.  A AzureRM.TemplateValidator.psm1 PowerShell modul importálása:
    
    ```PowerShell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2.  A sablon ellenőrző futtatása:

    ```PowerShell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

Sablon érvényesítési figyelmeztetést vagy hibát naplózza a PowerShell-konzolba, és a forráskönyvtárban HTML-fájlba is naplózza. Az ellenőrzési jelentés kimenete például így néz ki:

![a minta ellenőrzési jelentés](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Paraméterek

| Paraméter | Leírás | Szükséges |
| ----- | -----| ----- |
| TemplatePath | Megadja a rekurzív módon elérési található a Resource Manager-sablonok | Igen | 
| TemplatePattern | A neve a sablon fájlok kereséséhez. | Nem |
| CapabilitiesPath | Felhőalapú képességek JSON-fájl elérési útja | Igen | 
| IncludeComputeCapabilities | IaaS-erőforrásokhoz, mint a Virtuálisgép-méretek és a Virtuálisgép-bővítmények értékelése tartalmazza | Nem |
| IncludeStorageCapabilities | Magában foglalja a tárolási erőforrások, például a Termékváltozat típusok kiértékelése | Nem |
| Jelentés | A generált HTML-jelentés neve | Nem |
| Részletes | Hibák és figyelmeztetések naplózza a konzolhoz | Nem|


### <a name="examples"></a>Példák
Ebben a példában az összes érvényesíti a [Azure verem gyorsindítási sablonok](https://github.com/Azure/AzureStack-QuickStart-Templates) tölt le helyileg, és azt is ellenőrzi a Virtuálisgép-méretek és bővítmények Azure verem szoftverfejlesztői készlet képességek ellen.

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json.json `
-TemplatePattern MyStandardTemplateName.json`
-IncludeComputeCapabilities`
-Report TemplateReport.html
```

## <a name="build-cloud-capabilities-file"></a>Felhő capabilities-fájl létrehozása
A letöltött fájlok közé tartoznak az alapértelmezett *AzureStackCloudCapabilities_with_AddOns_20170627.json* fájl, amely ismerteti a Azure verem Development Kit szolgáltatásverziók PaaS-szolgáltatások telepítve.  Ha további erőforrás-szolgáltatók telepítéséhez használhatja a AzureRM.CloudCapabilities PowerShell-modul egy JSON-fájl, beleértve az új szolgáltatások létrehozásához.  

1.  Ellenőrizze, hogy a kapcsolat Azure verem.  Ezek a lépések akkor hajthatók végre a Azure verem development kit gazdagépről, vagy használhat [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) csatlakozni a munkaállomáson. 
2.  A AzureRM.CloudCapabilities PowerShell modul importálása:

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ``` 

3.  A Get-CloudCapabilities parancsmag szolgáltatásverziók lekérdezéséhez, és hozzon létre egy felhőalapú képességek JSON-fájlt használja:

    ```PowerShell
    Get-AzureRMCloudCapability -Location 'local' -Verbose
    ```             


## <a name="next-steps"></a>További lépések
 - [Sablonok telepítése Azure verem](azure-stack-arm-templates.md)
 - [Az Azure-verem sablonok fejlesztése](azure-stack-develop-templates.md)

