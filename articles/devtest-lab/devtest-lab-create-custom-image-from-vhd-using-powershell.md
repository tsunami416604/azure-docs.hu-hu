---
title: Azure DevTest Labs egyéni lemezkép létrehozása a PowerShell használatával VHD-fájl |} Microsoft Docs
description: Egyéni lemezképként az Azure DevTest Labs szolgáltatásban, a PowerShell használatával VHD-fájl létrehozásának automatizálása
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: v-craic
ms.openlocfilehash: 6bfb34c05a03ed02694210a5901f4f367e8a2fd3
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>Egyéni lemezkép létrehozása a PowerShell használatával VHD-fájl

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Részletes útmutató

A következő lépések végigvezetik egyéni lemezkép létrehozása a PowerShell használatával VHD-fájlt:

1. Egy PowerShell-parancssorba, jelentkezzen be az Azure-fiókjával a következő hívást a **Connect-AzureRmAccount** parancsmag.  
    
    ```PowerShell
    Connect-AzureRmAccount
    ```

1.  Válassza ki a kívánt Azure-előfizetés meghívásával a **Select-AzureRmSubscription** parancsmag. A következő helyőrzőt cserélje le a **$subscriptionId** változó, egy érvényes Azure-előfizetéssel. 

    ```PowerShell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzureRmSubscription -SubscriptionId $subscriptionId
    ```

1.  A tesztkörnyezet objektum beolvasása meghívásával a **Get-AzureRmResource** parancsmag. Cserélje le a helyőrzőket a **$labRg** és **$labName** változók a környezetének megfelelő értékekkel. 

    ```PowerShell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzureRmResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```
 
1.  A labor tárolási fiók és a tesztkörnyezet tárolási fiók kulcs értékek lekérése a labor objektumból. 

    ```PowerShell
    $labStorageAccount = Get-AzureRmResource -ResourceId $lab.Properties.defaultStorageAccount 
    $labStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $labStorageAccount.ResourceGroupName -Name $labStorageAccount.ResourceName)[0].Value
    ```

1.  A következő helyőrzőt cserélje le a **$vhdUri** változó pedig a feltöltött VHD-fájl URI-azonosítójú. A VHD-fájl URI lekérheti a tárfiók a blob panel az Azure portálon.

    ```PowerShell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  Az egyéni lemezképet létrehozni a **New-AzureRmResourceGroupDeployment** parancsmag. Cserélje le a helyőrzőket a **$customImageName** és **$customImageDescription** változókat, a környezetnek kifejező nevet.

    ```PowerShell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzureRmResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/Samples/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>PowerShell parancsfájl egyéni lemezkép a VHD-fájl létrehozása

A következő PowerShell-parancsfájl segítségével létrehozhat egyéni rendszerképeket a VHD-fájl. Cserélje le a helyőrzőket (kezdő és Záró csúcsos zárójelek rendelkező) az igényeinek megfelelő értékeket. 

```PowerShell
# Log in to your Azure account.  
Connect-AzureRmAccount

# Select the desired Azure subscription. 
$subscriptionId = '<Specify your subscription ID here>'
Select-AzureRmSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<Specify your lab resource group name here>'
$labName = '<Specify your lab name here>'
$lab = Get-AzureRmResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the lab storage account and lab storage account key values.
$labStorageAccount = Get-AzureRmResource -ResourceId $lab.Properties.defaultStorageAccount 
$labStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $labStorageAccount.ResourceGroupName -Name $labStorageAccount.ResourceName)[0].Value

# Set the URI of the VHD file.  
$vhdUri = '<Specify the VHD URI here>'

# Set the custom image name and description values.
$customImageName = '<Specify the custom image name>'
$customImageDescription = '<Specify the custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image. 
New-AzureRmResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/Samples/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések

- [Egyéni lemezképek vagy képletek?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Az Azure DevTest Labs között egyéni lemezképek másolása](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>További lépések

- [A virtuális gépek hozzáadása a tesztkörnyezet](devtest-lab-add-vm.md)
