---
title: "Az Azure házirend json-mintát - storage-fiókok és a virtuális gépek engedélyezett Termékváltozatait |} Microsoft Docs"
description: "A json-mintát házirend verziójához jóváhagyott termékváltozatok storage-fiókok és a virtuális gépek használni."
services: azure-policy
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 
ms.service: azure-policy
ms.devlang: 
ms.topic: sample
ms.tgt_pltfrm: 
ms.workload: 
ms.date: 10/30/2017
ms.author: banders
ms.custom: mvc
ms.openlocfilehash: 9936af72dc7babfe8935dac1b49c25695e827042
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2017
---
# <a name="allowed-skus-for-storage-accounts-and-virtual-machines"></a>A storage-fiókok és a virtuális gépek engedélyezett termékváltozatok

Ez a házirend-hez engedélyezett termékváltozatok storage-fiókok és a virtuális gépek használnia. Beépített házirendet használja, hogy jóvá SKU. Megadhatja, hogy engedélyezett virtuális gépek termékváltozatok tömbje és jóváhagyott tárfiók termékváltozatok tömbjét.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Minta sablon

[!code-json[main](../../../policy-templates/samples/PolicyInitiatives/skus-for-mutiple-types/azurepolicyset.json "Allowed SKUs for Storage Accounts and Virtual Machines")]

A sablon használatával telepíthető a [Azure-portálon](#deploy-with-the-portal) vagy [PowerShell](#deploy-with-powershell).

## <a name="deploy-with-the-portal"></a>Üzembe helyezés a portállal

[![Üzembe helyezés az Azure-ban](http://azuredeploy.net/deploybutton.png)](https://aka.ms/getpolicy)

## <a name="deploy-with-powershell"></a>Üzembe helyezés a PowerShell-lel

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

```powershell
$policydefinitions = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/skus-for-mutiple-types/azurepolicyset.definitions.json"
$policysetparameters = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/skus-for-mutiple-types/azurepolicyset.parameters.json"

$policyset= New-AzureRmPolicySetDefinition -Name "skus-for-mutiple-types" -DisplayName "Allowed SKUs for Storage Accounts and Virtual Machines" -Description "This policy allows you to speficy what skus are allowed for storage accounts and virtual machines" -PolicyDefinition $policydefinitions -Parameter $policysetparameters

New-AzureRmPolicyAssignment -PolicySetDefinition $policyset -Name <assignmentname> -Scope <scope>  -LISTOFALLOWEDSKUS_1 <VM SKUs> -LISTOFALLOWEDSKUS_2 <Storage Account SKUs >  -Sku @{"Name"="A1";"Tier"="Standard"}
```

### <a name="clean-up-powershell-deployment"></a>PowerShell központi telepítés tisztítása

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

- További Azure csoportházirend-sablon minták erővel [sablonok Azure házirend](../json-samples.md).
