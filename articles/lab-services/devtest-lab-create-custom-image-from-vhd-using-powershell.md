---
title: Egyéni rendszerkép létrehozása VHD-fájlból Azure PowerShell használatával
description: Egyéni rendszerkép létrehozásának automatizálása Azure DevTest Labs VHD-fájlból a PowerShell használatával
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: cd144659dd8a8e981e267be998c9c783b7482840
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76169574"
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>Egyéni rendszerkép létrehozása VHD-fájlból a PowerShell használatával

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="step-by-step-instructions"></a>Lépésenkénti utasítások

A következő lépések végigvezetik egy egyéni rendszerkép létrehozásán egy VHD-fájlból a PowerShell használatával:

1. Egy PowerShell-parancssorban jelentkezzen be az Azure-fiókjába a következő hívással a **AzAccount** parancsmaggal.

    ```powershell
    Connect-AzAccount
    ```

1.  A **Select-AzSubscription** parancsmag meghívásával válassza ki a kívánt Azure-előfizetést. Cserélje le a következő helyőrzőt a **$subscriptionId** változóra érvényes Azure-előfizetés-azonosítóval.

    ```powershell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzSubscription -SubscriptionId $subscriptionId
    ```

1.  Kérje le a labor objektumot a **Get-AzResource** parancsmag meghívásával. Cserélje le a következő helyőrzőket a **$labRgre** , és **$labName** változókat a környezetének megfelelő értékekkel.

    ```powershell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```

1.  Cserélje le a **$vhdUri** változó következő helyőrzőjét a feltöltött vhd-fájl URI-fájljára. A VHD-fájl URI-JÁT a Storage-fiók blob paneljéről kérheti le a Azure Portal.

    ```powershell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  Hozza létre az egyéni rendszerképet a **New-AzResourceGroupDeployment** parancsmag használatával. Cserélje le a következő helyőrzőket a **$customImageNamere** , és **$customImageDescription** a változókat a környezete szempontjából hasznos nevekre.

    ```powershell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>PowerShell-parancsfájl egy VHD-fájlból származó egyéni rendszerkép létrehozásához

A következő PowerShell-szkripttel létrehozhat egy VHD-fájlból származó egyéni rendszerképet. Cserélje le a helyőrzőket (szögletes zárójelekkel kezdődik és végződik) az igényeinek megfelelő értékekkel.

```powershell
# Log in to your Azure account.
Connect-AzAccount

# Select the desired Azure subscription.
$subscriptionId = '<Specify your subscription ID here>'
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<Specify your lab resource group name here>'
$labName = '<Specify your lab name here>'
$lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Set the URI of the VHD file.
$vhdUri = '<Specify the VHD URI here>'

# Set the custom image name and description values.
$customImageName = '<Specify the custom image name>'
$customImageDescription = '<Specify the custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image.
New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések

- [Egyéni lemezképek vagy képletek?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Egyéni rendszerképek másolása Azure DevTest Labs között](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>További lépések

- [Virtuális gép hozzáadása a laborhoz](devtest-lab-add-vm.md)
