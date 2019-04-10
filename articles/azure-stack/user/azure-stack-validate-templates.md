---
title: Egy sablon érvényesítése eszköz használatával ellenőrizze a sablonokat az Azure Stackhez |} A Microsoft Docs
description: A sablonok az Azure Stack való központi telepítésének ellenőrzése
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/08/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 650b868762299725927623134039e87bbee9f4c2
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59277510"
---
# <a name="check-your-templates-for-azure-stack-with-the-template-validation-tool"></a>Ellenőrizze a sablonokat az Azure Stack a sablon érvényesítése eszközzel

*A következőre érvényes Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

A sablon érvényesítése eszköz segítségével ellenőrizze, hogy az Azure Resource Manager [sablonok](azure-stack-arm-templates.md) készen áll az Azure Stack üzembe helyezése. A sablon érvényesítése eszközt az Azure Stack eszközök részeként érhető el. Töltse le az Azure Stack eszközök ismertetett lépések segítségével a [eszközök letöltése a githubról](azure-stack-powershell-download.md) cikk.

## <a name="overview"></a>Áttekintés

A sablon érvényesítése, először hozhat létre a felhőalapú képességek fájlt, és futtassa a fürtérvényesítési eszköz. Használja a következő PowerShell-modulok az Azure Stack-eszközök:

- Az a **CloudCapabilities** mappa: `AzureRM.CloudCapabilities.psm1` hoz létre a felhőalapú képességek jelölő JSON-fájlok a szolgáltatások és a egy Azure Stack-felhőben lévő verziók.
- Az a **TemplateValidator** mappa: `AzureRM.TemplateValidator.psm1` központi telepítési sablonok tesztelése az Azure Stack felhő képességeit JSON-fájlt használ.

## <a name="build-the-cloud-capabilities-file"></a>A felhőalapú képességek fájl létrehozása

Mielőtt használná a sablon érvényesítési, futtassa a **AzureRM.CloudCapabilities** PowerShell-modult hozhat létre egy JSON-fájlt.

>[!NOTE]
> Ha az integrált rendszer frissítésére, vagy adjon meg új szolgáltatás, vagy virtuális bővítmények, ez a modul újra kell futtatnia.

1. Győződjön meg arról, hogy rendelkezik az Azure Stackhez. Az Azure Stack development kit állomás hajthat végre ezeket a lépéseket, vagy használhatja egy [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) való csatlakozáshoz a munkaállomáson.
2. Importálás a **AzureRM.CloudCapabilities** PowerShell-modult:

    ```powershell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ```

3. Használja a `Get-CloudCapabilities` parancsmaggal szolgáltatásverziók lekérni, és hozzon létre egy felhőalapú képességek JSON-fájlt. Ha nem ad meg **- OutputPath**, a fájl AzureCloudCapabilities.Json jön létre az aktuális könyvtárban található. Az Azure-helyen használja:

    ```powershell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>Sablonok ellenőrzése

Sablonok használatával érvényesítéséhez kövesse az alábbi lépéseket a **AzureRM.TemplateValidator** PowerShell-modult. A saját sablonokat, vagy ellenőrizheti a [Azure Stack-gyorssablonok](https://github.com/Azure/AzureStack-QuickStart-Templates).

1. Importálás a **AzureRM.TemplateValidator.psm1** PowerShell-modult:

    ```powershell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2. A sablon érvényesítési futtassa:

    ```powershell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

A PowerShell-konzolon megjelenő és HTML-fájl a forráskönyvtár írt sablon érvényesítési figyelmeztetést vagy hibát. Az alábbi képernyőfelvételen látható egy példa az ellenőrzési jelentésben:

![Sablon-ellenőrzési jelentés](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Paraméterek

A sablon érvényesítési az alábbi paramétereket támogatja.

| Paraméter | Leírás | Szükséges |
| ----- | -----| ----- |
| TemplatePath | Itt adhatja meg az elérési út rekurzív keresése az Azure Resource Manager-sablonok. | Igen |
| TemplatePattern | Meghatározza a megfelelő sablon fájlok nevét. | Nem |
| CapabilitiesPath | A felhőalapú képességek JSON-fájl elérési útját adja meg. | Igen |
| IncludeComputeCapabilities | Az IaaS-erőforrások, például a Virtuálisgép-méretek és a Virtuálisgép-bővítmények értékelési tartalmazza. | Nem |
| IncludeStorageCapabilities | Magában foglalja a tárolási erőforrások, például a Termékváltozat-típusok kiértékelése. | Nem |
| Jelentés | Meghatározza a létrehozott HTML-jelentés nevére. | Nem |
| Részletes | Hibák és figyelmeztetések naplózza a konzolhoz. | Nem|

### <a name="examples"></a>Példák

Ebben a példában az összes érvényesíti a [Azure Stack-gyorssablonok](https://github.com/Azure/AzureStack-QuickStart-Templates) helyi tároló le. A példa azt is ellenőrzi, virtuálisgép-méretek és bővítmények ellen az Azure Stack fejlesztői készletének képességei:

```powershell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json `
-IncludeComputeCapabilities `
-Report TemplateReport.html
```

## <a name="next-steps"></a>További lépések

- [Sablonok üzembe helyezése az Azure Stackhez](azure-stack-arm-templates.md)
- [Sablonok fejlesztése az Azure Stackhez](azure-stack-develop-templates.md)
