---
title: Sablonok fejlesztése az Azure Stackhez |} A Microsoft Docs
description: Ajánlott eljárások az Azure Stack-sablon
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 8a5bc713-6f51-49c8-aeed-6ced0145e07b
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: jeffgo
ms.openlocfilehash: d09dec2f327d8b5911a4e55832ba106838c7ebc3
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42058428"
---
# <a name="azure-resource-manager-template-considerations"></a>Az Azure Resource Manager-sablon kapcsolatos szempontok

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Az alkalmazás fejlesztését, fontos annak biztosítása érdekében az Azure és az Azure Stack közötti sablon hordozhatóság. Ez a cikk ismerteti az Azure Resource Manager fejlesztési szempontok [sablonok](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf), így az alkalmazás- és vizsgálati üzembe helyezés az Azure-ban az Azure Stack környezettel való hozzáférés nélkül is prototípusát.

## <a name="resource-provider-availability"></a>Az erőforrás-szolgáltató elérhetőség

A sablont szeretné telepíteni, csak kell használnia a Microsoft Azure-szolgáltatások, amelyek már elérhető, vagy az Azure Stack előzetes verzióban érhető el.

## <a name="public-namespaces"></a>Nyilvános névterek

Mivel a helyi adatközpontban Azure Stack, azt különböző szolgáltatási végpont névtérrel rendelkezik, mint az Azure nyilvános felhő. Ennek eredményeképpen szoftveresen kötött nyilvános végpontokat az Azure Resource Manager-sablonokban sikertelen üzembe helyezheti őket az Azure Stack megkísérlésekor. Dinamikusan hozhat létre Szolgáltatásvégpontok használatával a *referencia* és *összefűzni* funkciók értékeket beolvasni az erőforrás-szolgáltató üzembe helyezése során. Hardcoding helyett például *blob.core.windows.net* a sablonban lekérni a [primaryEndpoints.blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-simple-windows-vm/azuredeploy.json#L201) dinamikus beállításához az *osDisk.URI* végpont:

     "osDisk": {"name": "osdisk","vhd": {"uri":
     "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
      '/',variables('OSDiskName'),'.vhd')]"}}

## <a name="api-versioning"></a>API-verziószámozás

Azure-szolgáltatás verziói között az Azure és az Azure Stack eltérhet. Az egyes erőforrások igényel a **apiVersion** attribútum, amely meghatározza a funkció által kínált lehetőségeket. Az Azure Stackben API-verzió kompatibilitás biztosítása érdekében a következő API-verziók esetében érvényesek minden egyes erőforrás-szolgáltató:

| Erőforrás-szolgáltató | apiVersion |
| --- | --- |
| Compute |`'2015-06-15'` |
| Network (Hálózat) |`'2015-06-15'`, `'2015-05-01-preview'` |
| Storage |`'2016-01-01'`, `'2015-06-15'`, `'2015-05-01-preview'` |
| KeyVault | `'2015-06-01'` |
| App Service |`'2015-08-01'` |

## <a name="template-functions"></a>Sablonfüggvények

Az Azure Resource Manager [funkciók](../../azure-resource-manager/resource-group-template-functions.md) dinamikus sablonok létrehozásához szükséges képességeket biztosít. Tegyük fel a feladatokat, mint a functions használhatja:

* Összetűzésének, vagy karakterlánc-csonkolás.
* Egyéb erőforrások hivatkozó értékeit.
* A több példány üzembe helyezendő erőforrások léptetés.

Ezek a függvények nem érhetők el az Azure Stack:

* Kihagyás
* hajtsa végre a megfelelő

## <a name="resource-location"></a>Erőforrás helye

Az Azure Resource Manager-sablonok egy helyen attribútum használatával helyezze el az erőforrások üzembe helyezése során. Az Azure-ban mint például az USA nyugati RÉGIÓJA vagy Délkelet-amerikai régió helyekre utalnak. Az Azure Stackben más helyen, mert az Azure Stack az adatközpontjában. Annak érdekében, hogy a sablonok olyan adatközpontomba között az Azure és az Azure Stack, az erőforráscsoport helyét kell hivatkoznia, az egyéni erőforrások üzembe helyezése során. Ezt megteheti használatával `[resourceGroup().Location]` annak érdekében, hogy az összes erőforrás öröklik az erőforráscsoport helyét. A következő cikkből szerint példaként szolgál a storage-fiók üzembe helyezése során ez a függvény használatával:

    "resources": [
    {
      "name": "[variables('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "[variables('apiVersionStorage')]",
      "location": "[resourceGroup().location]",
      "comments": "This storage account is used to store the VM disks",
      "properties": {
      "accountType": "Standard_GRS"
      }
    }
    ]

## <a name="next-steps"></a>További lépések

* [Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
* [Az Azure CLI-vel sablonok üzembe helyezése](azure-stack-deploy-template-command-line.md)
* [Sablonok üzembe helyezése a Visual Studióval](azure-stack-deploy-template-visual-studio.md)
