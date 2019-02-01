---
title: SAS-token és a PowerShell segítségével az Azure-sablon üzembe helyezése |} A Microsoft Docs
description: Azure Resource Manager és az Azure PowerShell használatával helyezheti üzembe az erőforrásokat az Azure-ban, amely a SAS-jogkivonat használatával védett sablon alapján.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
ms.openlocfilehash: 0935952011bf4cbcae9bf2e9ac218a9fc3be47ad
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55497655"
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-powershell"></a>Az SAS-jogkivonat és az Azure PowerShell saját Resource Manager-sablon üzembe helyezése

Ha a sablon a storage-fiókban található, korlátozza a hozzáférést a sablont, és adjon meg egy közös hozzáférésű jogosultságkód (SAS) üzembe helyezése során. Ez a témakör ismerteti az Azure PowerShell használata a Resource Manager-sablonok az üzembe helyezés során adja meg a SAS-jogkivonatát. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-private-template-to-storage-account"></a>Saját sablon hozzáadása a storage-fiókba

A sablonok hozzáadása egy tárfiók és a egy SAS-jogkivonat használatával üzembe helyezés során őket mutató hivatkozást.

> [!IMPORTANT]
> Az alábbi lépéseket követve a blob, a sablont tartalmazó könyvtárban elérhető, csak a fiók tulajdonosa. Viszont a BLOB SAS-token létrehozásakor a blob, bárki hozzáférhet az URI-ra. Ha egy másik felhasználó elfogja az URI-t, a felhasználó hozzáférjen a sablon. Egy SAS-jogkivonat használatával korlátozza a hozzáférést a sablonok egy jó módszer, de nem tartalmazhatja bizalmas adatokat – például jelszavakat közvetlenül a sablonban.
> 
> 

Az alábbi példa állít be egy személyes fiók tárolót, és feltölti egy sablont:
   
```powershell
# create a storage account for templates
New-AzResourceGroup -Name ManageGroup -Location "South Central US"
New-AzStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name} -Type Standard_LRS -Location "West US"
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# create a container and upload template
New-AzureStorageContainer -Name templates -Permission Off
Set-AzureStorageBlobContent -Container templates -File c:\MyTemplates\storage.json
```

## <a name="provide-sas-token-during-deployment"></a>Adja meg a SAS-jogkivonat üzembe helyezés során
A storage-fiókokban privát sablon üzembe helyezéséhez hozzon létre egy SAS-token és a sablon URI. Hagyjon elegendő időt a telepítés befejezéséhez a lejárati idő beállítása.
   
```powershell
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# get the URI with the SAS token
$templateuri = New-AzureStorageBlobSASToken -Container templates -Blob storage.json -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri $templateuri
```

Az SAS-jogkivonatot használó kapcsolt sablonokkal rendelkező egy példa: [kapcsolt sablonok használata az Azure Resource Manager](resource-group-linked-templates.md).


## <a name="next-steps"></a>További lépések
* Sablonok üzembe helyezése bevezetésért lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](resource-group-template-deploy.md).
* Teljes minta parancsfájl, amely üzembe helyezi a sablont, tekintse meg a [parancsfájl üzembe helyezése Resource Manager-sablon](resource-manager-samples-powershell-deploy.md)
* A paraméterek meghatározása sablonban, lásd: [sablonok készítése](resource-group-authoring-templates.md#parameters).
