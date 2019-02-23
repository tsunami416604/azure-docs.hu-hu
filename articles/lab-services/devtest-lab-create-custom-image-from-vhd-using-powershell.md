---
title: Az Azure DevTest Labs egyéni lemezkép készítése VHD-fájlból PowerShell-lel |} A Microsoft Docs
description: Az Azure DevTest Labs szolgáltatásban, egy PowerShell-lel VHD-fájlból egy egyéni rendszerkép létrehozásának automatizálása
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
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 7c18ac13b9663ad541ae206347a8df17ff06297c
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737382"
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>Egy egyéni lemezkép készítése VHD-fájlból PowerShell-lel

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="step-by-step-instructions"></a>Részletes útmutató

A következő lépések végigvezetik egy egyéni rendszerkép létrehozása a PowerShell-lel VHD-fájlból:

1. Egy PowerShell-parancssorba, jelentkezzen be az Azure-fiókjába a következő hívást a **Connect-AzAccount** parancsmagot.  
    
    ```PowerShell
    Connect-AzAccount
    ```

1.  Válassza ki a kívánt Azure-előfizetés meghívásával a **Select-AzSubscription** parancsmagot. Következő helyőrzőjét cserélje le a **$subscriptionId** változót egy érvényes Azure-előfizetés-azonosítót. 

    ```PowerShell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzSubscription -SubscriptionId $subscriptionId
    ```

1.  A lab-objektum első meghívásával a **Get-AzResource** parancsmagot. Cserélje le a helyőrzőket a **$labRg** és **$labName** változók a környezetének megfelelő értékekkel. 

    ```PowerShell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```
 
1.  A labor storage-fiók és a tesztkörnyezet tárolási kulcsára lekérheti a labor objektum. 

    ```PowerShell
    $labStorageAccount = Get-AzResource -ResourceId $lab.Properties.defaultStorageAccount 
    $labStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $labStorageAccount.ResourceGroupName -Name $labStorageAccount.ResourceName)[0].Value
    ```

1.  Következő helyőrzőjét cserélje le a **$vhdUri** változó, az URI-t a feltöltött VHD-fájl. A VHD-fájl URI-t kaphat a tárfiók blob panelen az Azure Portalon.

    ```PowerShell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  Hozzon létre az egyéni rendszerkép használatával a **New-AzResourceGroupDeployment** parancsmagot. Cserélje le a helyőrzőket a **$customImageName** és **$customImageDescription** adjon kifejező nevet a környezetnek a változókat.

    ```PowerShell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/Samples/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>PowerShell-parancsprogram egy egyéni lemezkép készítése VHD-fájlból

A következő PowerShell-parancsfájl segítségével egy egyéni lemezkép készítése VHD-fájlból. Cserélje le a zárójelben (kezdő és a csúcsos zárójeleket) az igényeinek megfelelő értékeivel. 

```PowerShell
# Log in to your Azure account.  
Connect-AzAccount

# Select the desired Azure subscription. 
$subscriptionId = '<Specify your subscription ID here>'
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<Specify your lab resource group name here>'
$labName = '<Specify your lab name here>'
$lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the lab storage account and lab storage account key values.
$labStorageAccount = Get-AzResource -ResourceId $lab.Properties.defaultStorageAccount 
$labStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $labStorageAccount.ResourceGroupName -Name $labStorageAccount.ResourceName)[0].Value

# Set the URI of the VHD file.  
$vhdUri = '<Specify the VHD URI here>'

# Set the custom image name and description values.
$customImageName = '<Specify the custom image name>'
$customImageDescription = '<Specify the custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image. 
New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/Samples/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések

- [Egyéni rendszerképek vagy képleteket?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Egyéni rendszerképek az Azure DevTest Labs közötti másolása](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>További lépések

- [A labor virtuális gép hozzáadása](devtest-lab-add-vm.md)
