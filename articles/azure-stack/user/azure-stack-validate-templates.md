---
title: Egy sablon érvényesítési eszköz segítségével ellenőrizze a sablonok Azure verem |} Microsoft Docs
description: Sablonok az Azure-verem való központi telepítésének ellenőrzése
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 88fac41ce2c9fa0c5569beae02ab90a507c89a34
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="check-your-templates-for-azure-stack-with-the-template-validation-tool"></a>A sablonok Azure verem egyeztetni a sablon ellenőrzése eszköz

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A sablon ellenőrzése eszköz segítségével ellenőrizze, hogy az Azure Resource Manager [sablonok](azure-stack-arm-templates.md) készen áll az Azure-verem központi telepítéséhez. A sablon ellenőrzése eszköz az Azure-verem eszközök részeként érhető el. Töltse le az Azure-verem eszközök ismertetett lépések segítségével a [eszközök letölthető a GitHub](azure-stack-powershell-download.md) cikk.

## <a name="overview"></a>Áttekintés

Ellenőrizze a sablont, be kell összeállítása a felhőalapú képességek első fájlt, és futtassa az ellenőrzési eszközt. A következő PowerShell-moduljai a Azure verem eszközök használhatja:

- Az a **TemplateValidator** mappába:<br>         AzureRM.CloudCapabilities.psm1 létrehoz egy felhőalapú képességek JSON szolgáltatások és egy Azure verem felhőben verziók jelölő.
- Az a **CloudCapabilities** mappába:<br>
AzureRM.TemplateValidator.psm1 sablonok központi telepítés tesztelése az Azure-verem felhőalapú képességek JSON-fájlt használ.

## <a name="build-the-cloud-capabilities-file"></a>A felhő capabilities-fájl létrehozása

A sablon érvényesítő használatához futtassa a AzureRM.CloudCapabilities PowerShell-modul egy JSON-fájl létrehozásához.

>[!NOTE]
>Ha az integrált rendszert, vagy adjon meg új szolgáltatások vagy virtuális bővítmények, ez a modul újra kell futtatnia.

1. Ellenőrizze, hogy a kapcsolat Azure verem. Ezek a lépések akkor hajthatók végre a Azure verem development kit gazdagépről, vagy használhat egy [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) csatlakozni a munkaállomáson.
2. A AzureRM.CloudCapabilities PowerShell modul importálása:

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ```

3. A Get-CloudCapabilities parancsmag segítségével szolgáltatásverziók beolvasása és a felhőalapú képességek JSON-fájl létrehozása. Ha nem adja meg a **- OutputPath**, a fájl AzureCloudCapabilities.Json jön létre az aktuális könyvtárban található. A tényleges helyet használja:

    ```PowerShell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>Sablonok ellenőrzése

Ezek a lépések segítségével ellenőrizheti a sablonok AzureRM.TemplateValidator PowerShell modul használatával. Használhatja a saját sablonokat, vagy ellenőrizze a [Azure verem gyorsindítási sablonok](https://github.com/Azure/AzureStack-QuickStart-Templates).

1. A AzureRM.TemplateValidator.psm1 PowerShell modul importálása:

    ```PowerShell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2. A sablon ellenőrző futtatása:

    ```PowerShell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

Sablon érvényesítési figyelmeztetések vagy hibák naplózása a PowerShell-konzolban és a forráskönyvtár a HTML-fájlba. A következő képernyőfelvétel-készítés ellenőrzési jelentésben példáját mutatja be:

![Sablon ellenőrzési jelentés](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Paraméterek

A sablon érvényesítő az alábbi paramétereket támogatja.

| Paraméter | Leírás | Szükséges |
| ----- | -----| ----- |
| TemplatePath | Megadja a rekurzív módon elérési található Azure Resource Manager-sablonok | Igen | 
| TemplatePattern | A neve a sablon fájlok kereséséhez. | Nem |
| CapabilitiesPath | Felhőalapú képességek JSON-fájl elérési útja | Igen | 
| IncludeComputeCapabilities | IaaS-erőforrásokhoz, mint a Virtuálisgép-méretek és a Virtuálisgép-bővítmények értékelése tartalmazza | Nem |
| IncludeStorageCapabilities | Magában foglalja a tárolási erőforrások, például a Termékváltozat típusok kiértékelése | Nem |
| Jelentés | A generált HTML-jelentés neve | Nem |
| Részletes | Hibák és figyelmeztetések naplózza a konzolhoz | Nem|

### <a name="examples"></a>Példák

Ebben a példában az összes érvényesíti a [Azure verem gyorsindítási sablonok](https://github.com/Azure/AzureStack-QuickStart-Templates) letöltött helyi tárterület. A példa azt is ellenőrzi, virtuálisgép-méretek és bővítmények Azure verem szoftverfejlesztői készlet képességek ellen.

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json.json `
-TemplatePattern MyStandardTemplateName.json`
-IncludeComputeCapabilities`
-Report TemplateReport.html
```

## <a name="next-steps"></a>További lépések

- [Sablonok telepítése Azure verem](azure-stack-arm-templates.md)
- [Az Azure-verem sablonok fejlesztése](azure-stack-develop-templates.md)
