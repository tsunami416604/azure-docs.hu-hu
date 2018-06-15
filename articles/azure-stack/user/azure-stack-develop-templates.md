---
title: Sablonok az Azure-verem fejlesztése |} Microsoft Docs
description: További tudnivalók az Azure verem sablon gyakorlati tanácsok
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 8a5bc713-6f51-49c8-aeed-6ced0145e07b
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 046866d9ed7ce65e3b46be1c67b4ab2058cefa4d
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34304147"
---
# <a name="azure-resource-manager-template-considerations"></a>Az Azure Resource Manager sablon kapcsolatos szempontok

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Az alkalmazás fejlesztése, fontos annak biztosítása érdekében a sablon hordozhatóság Azure és az Azure-verem között. Ez a cikk ismerteti az Azure Resource Manager fejlesztése szempontjai [sablonok](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf), így a prototípus az alkalmazás teszt központi és az Azure-ban Azure verem környezetben való hozzáférés nélkül is.

## <a name="resource-provider-availability"></a>Erőforrás-szolgáltató elérhetőség

A sablont szeretne telepíteni, csak kell használnia, amelyek már a rendelkezésre álló vagy Azure verem Preview Microsoft Azure-szolgáltatások.

## <a name="public-namespaces"></a>Nyilvános névterek

Mivel Azure verem az adatközpontban található, mint az Azure nyilvános felhőjében rendelkezik másik végpont névterei. Ennek eredményeképpen szoftveresen kötött nyilvános végpontok Azure Resource Manager-sablonok nem telepítheti őket az Azure-verem megkísérlésekor. Dinamikusan Szolgáltatásvégpontok használatával hozhat létre a *hivatkozás* és *összefűzésére* funkciók értékek lekérése az erőforrás-szolgáltató telepítése során. Például helyett hardcoding *blob.core.windows.net* a sablonban beolvasni a [primaryEndpoints.blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-simple-windows-vm/azuredeploy.json#L201) dinamikusan beállítása a *osDisk.URI* végpont:

     "osDisk": {"name": "osdisk","vhd": {"uri":
     "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
      '/',variables('OSDiskName'),'.vhd')]"}}

## <a name="api-versioning"></a>API-verziószámozás

Azure-szolgáltatások verziók Azure és az Azure-verem eltérőek. Az egyes erőforrásokra van szükség a **apiVersion** attribútum, amely meghatározza a biztosított képességeket. Ahhoz, hogy az Azure-verem API verziókompatibilitás ellenőrzése érdekében, a következő API-verziók érvényesek minden egyes erőforrás-szolgáltató:

| Erőforrás-szolgáltató | apiVersion |
| --- | --- |
| Compute |`'2015-06-15'` |
| Network (Hálózat) |`'2015-06-15'`, `'2015-05-01-preview'` |
| Storage |`'2016-01-01'`, `'2015-06-15'`, `'2015-05-01-preview'` |
| KeyVault | `'2015-06-01'` |
| App Service |`'2015-08-01'` |

## <a name="template-functions"></a>Sablonfüggvények

Az Azure Resource Manager [funkciók](../../azure-resource-manager/resource-group-template-functions.md) dinamikus sablonok létrehozásához szükséges képességek biztosítása. Példaként használható funkciók feladatokat, például:

* Hozzáfűzésével vagy karakterláncok díszítésre.
* Egyéb erőforrások hivatkozó értékeit.
* A több példány üzembe helyezendő erőforrásokat léptetés.

Ezek a funkciók Azure verem nem érhetők el:

* Kihagyás
* hajtsa végre a megfelelő

## <a name="resource-location"></a>Erőforrás helye

Az Azure Resource Manager-sablonok segítségével egy helyen attribútum erőforrások üzembe helyezése során. Azure-helyek a például az USA nyugati régiója vagy Délkelet-amerikai régió alatt. Azure verem más helyen mert Azure verem az adatközpontban található. Annak érdekében sablonok továbbítható Azure és az Azure-verem között, az erőforráscsoport helye kell hivatkozik, az egyes erőforrások központi telepítésekor. Ehhez használja `[resourceGroup().Location]` összes erőforrás öröklik az erőforráscsoport helye biztosításához. A következő cikkből egyik példája egy tárfiókot másikban ezen függvény használata:

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
* [Sablonok az Azure parancssori felület telepítése](azure-stack-deploy-template-command-line.md)
* [Sablonok üzembe helyezése a Visual Studióval](azure-stack-deploy-template-visual-studio.md)
