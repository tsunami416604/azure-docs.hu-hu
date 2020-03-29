---
title: Egyéni lemezkép létrehozása vHD-fájlból az Azure PowerShell használatával
description: Egyéni lemezkép létrehozásának automatizálása az Azure DevTest Labs ben egy virtuális merevlemez-fájlból a PowerShell használatával
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169574"
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>Egyéni lemezkép létrehozása Virtuális merevlemez-fájlból a PowerShell használatával

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="step-by-step-instructions"></a>Lépésenkénti utasítások

A következő lépések végigvezetik egy egyéni lemezkép létrehozásán egy Virtuális merevlemez-fájlból a PowerShell használatával:

1. Egy PowerShell-parancssorból jelentkezzen be az Azure-fiókjába a **Connect-AzAccount** parancsmag következő hívásával.

    ```powershell
    Connect-AzAccount
    ```

1.  Válassza ki a kívánt **Azure-előfizetést a Select-AzSubscription** parancsmag meghívásával. Cserélje le a következő helyőrzőt a **$subscriptionId** változóhoz egy érvényes Azure-előfizetés-azonosítóra.

    ```powershell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzSubscription -SubscriptionId $subscriptionId
    ```

1.  A laborobjektum lehívása a **Get-AzResource** parancsmag hívásával. Cserélje le a következő helyőrzőket a **$labRg** és **$labName** változókhoz a környezetének megfelelő értékekre.

    ```powershell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```

1.  Cserélje le a következő helyőrzőa **$vhdUri** változó az URI a feltöltött VHD-fájlba. A VHD-fájl URI-ját a tárfiók blobpaneljéről szerezheti be az Azure Portalon.

    ```powershell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  Hozza létre az egyéni lemezképet a **New-AzResourceGroupDeployment** parancsmag használatával. Cserélje le a következő helyőrzőket a **$customImageName** és **$customImageDescription** változókat a környezet értelmezhető neveire.

    ```powershell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>PowerShell-parancsfájl egyéni lemezkép létrehozásához vhd-fájlból

A következő PowerShell-parancsfájl segítségével hozhat létre egyéni lemezképet egy VHD-fájlból. Cserélje le a helyőrzőket (szögletes zárójelekkel kezdve és végződésben) az igényeinek megfelelő értékekre.

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

- [Egyéni képek vagy képletek?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Egyéni lemezképek másolása az Azure DevTest Labs között](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>További lépések

- [Virtuális gép hozzáadása a laborhoz](devtest-lab-add-vm.md)
