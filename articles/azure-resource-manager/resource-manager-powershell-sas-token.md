---
title: "Az SAS-jogkivonat és PowerShell Azure-sablon üzembe helyezése |} Microsoft Docs"
description: "Azure Resource Manager és az Azure PowerShell használatával telepítse az erőforrások az Azure által védett SAS-jogkivonat sablonból."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
ms.openlocfilehash: 1e3cea027b599e2b1af1ced0fdf14e2cc8a0db82
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-powershell"></a>Az SAS-jogkivonat és Azure PowerShell titkos Resource Manager-sablon üzembe helyezése

Ha a sablon olyan tárfiókban található, korlátozza a hozzáférést a sablont, és adjon meg egy közös hozzáférésű jogosultságkód (SAS) token üzembe helyezése során. Ez a témakör ismerteti az Azure PowerShell használata a Resource Manager-sablonok központi telepítése során egy SAS-jogkivonat biztosításához. 

## <a name="add-private-template-to-storage-account"></a>A tárfiók saját sablon hozzáadása

A sablonok hozzáadása egy tárfiókot, és hivatkozzon őket egy SAS-jogkivonat a telepítés során.

> [!IMPORTANT]
> Az alábbi lépéseket követve sablont tartalmazó blob elérhető, csak a fiók tulajdonosa. Amikor létrehoz egy SAS-jogkivonat a blob, a blob azonban, hogy az URI-azonosítójú bárki hozzáférhet. Ha egy másik felhasználó elfogja az URI, a felhasználó hozzáférjen a sablon lesz. A SAS-jogkivonat használatával egy jó módszer a sablonok való hozzáférés korlátozása, de kell nem tartalmazhat bizalmas adatok, például jelszavak közvetlenül a sablonban.
> 
> 

Az alábbi példa állít be egy személyes fiók tárolót, és feltölti a sablont:
   
```powershell
# create a storage account for templates
New-AzureRmResourceGroup -Name ManageGroup -Location "South Central US"
New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name} -Type Standard_LRS -Location "West US"
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# create a container and upload template
New-AzureStorageContainer -Name templates -Permission Off
Set-AzureStorageBlobContent -Container templates -File c:\MyTemplates\storage.json
```

## <a name="provide-sas-token-during-deployment"></a>Adja meg a SAS-jogkivonat központi telepítése során
A tárfiókban lévő titkos sablon telepítése, a SAS-token létrehozásához, és adja hozzá a sablon az URI azonosító. Állítsa be a lejárati időpont hagyjon elegendő időt a telepítés befejezéséhez.
   
```powershell
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# get the URI with the SAS token
$templateuri = New-AzureStorageBlobSASToken -Container templates -Blob storage.json -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzureRmResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri $templateuri
```

Például egy SAS-jogkivonat használatával a csatolt sablonokkal, [kapcsolt sablonok használata az Azure Resource Manager](resource-group-linked-templates.md).


## <a name="next-steps"></a>Következő lépések
* Megismerkedhet a sablonok központi telepítése, lásd: [erőforrások a Resource Manager-sablonok és Azure PowerShell telepítése](resource-group-template-deploy.md).
* Egy teljes parancsfájlt, amely telepít egy sablon, lásd: [sablonparancsfájlt erőforrás-kezelő telepítése](resource-manager-samples-powershell-deploy.md)
* Sablon paraméterek megadásához tekintse meg a [sablonok készítése](resource-group-authoring-templates.md#parameters).
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.

