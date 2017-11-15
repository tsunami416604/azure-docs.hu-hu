---
title: "Sablonok az Azure-verem fejlesztése |} Microsoft Docs"
description: "További tudnivalók az Azure verem sablon gyakorlati tanácsok"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: 8a5bc713-6f51-49c8-aeed-6ced0145e07b
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: helaw
ms.openlocfilehash: b9109c58b29d5f09f1a86068a87c5e7f839228af
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2017
---
# <a name="azure-resource-manager-template-considerations"></a>Az Azure Resource Manager sablon kapcsolatos szempontok

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Az alkalmazás fejlesztése, fontos annak biztosítása érdekében a sablon hordozhatóság Azure és az Azure-verem között.  Ez a témakör fejlesztése az Azure Resource Manager szempontjai [sablonok](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf), így a prototípus az alkalmazás teszt központi és az Azure-ban Azure verem környezetben való hozzáférés nélkül is.

## <a name="resource-provider-availability"></a>Erőforrás-szolgáltató elérhetőség
A sablont, hogy telepíteni szeretne egy Microsoft Azure-szolgáltatás, amely már érhető el, vagy Azure verem megtekintés kell használnia.

## <a name="public-namespaces"></a>Nyilvános névterek
Mivel Azure verem az adatközpontban található, mint az Azure nyilvános felhőjében rendelkezik másik végpont névterei. Ennek eredményeképpen szoftveresen kötött nyilvános végpontok a Resource Manager-sablonok nem telepítheti őket az Azure-verem megkísérlésekor. Ehelyett használhatja a *hivatkozás* és *összefűzésére* működnek, mint a szolgáltatási végpont értékek alapján dinamikusan build beolvasni az erőforrás-szolgáltató telepítése során. Például ahelyett, hogy megadása *blob.core.windows.net* a sablonban beolvasni a [primaryEndpoints.blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-simple-windows-vm/azuredeploy.json#L201) dinamikusan beállítása a *osDisk.URI* végpont:

     "osDisk": {"name": "osdisk","vhd": {"uri": 
     "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
      '/',variables('OSDiskName'),'.vhd')]"}}

## <a name="api-versioning"></a>API-versioning
Azure-szolgáltatások verziók Azure és az Azure-verem eltérőek. Az egyes erőforrásokra van szükség a apiVersion attribútum, amely meghatározza a biztosított képességeket. Ahhoz, hogy az Azure-verem API verziókompatibilitás ellenőrzése érdekében, az alábbiakban mindegyik erőforrás-szolgáltató érvénytelen API-verziók:

| Erőforrás-szolgáltató | apiVersion |
| --- | --- |
| Számítás |`'2015-06-15'` |
| Network (Hálózat) |`'2015-06-15'`, `'2015-05-01-preview'` |
| Storage |`'2016-01-01'`, `'2015-06-15'`, `'2015-05-01-preview'` |
| KeyVault | `'2015-06-01'` |
| App Service |`'2015-08-01'` |
| MySQL |`'2015-09-01'` |
| SQL |`'2014-04-01-preview'` |

## <a name="template-functions"></a>Sablonfüggvények
Erőforrás-kezelő [funkciók](../../azure-resource-manager/resource-group-template-functions.md) dinamikus sablonok létrehozásához szükséges képességek biztosítása. Példaként használható funkciók feladatokat, például:

* Hozzáfűzésével vagy díszítésre karakterláncok 
* Hivatkozási érték más forrásokból
* A több példány üzembe helyezendő erőforrásokat léptetés 

A sablonok létrehozása, egyes funkciók nem érhetők el az Azure verem Development Kit, és nem használható. Ezek a funkciók a következők:

* Kihagyás
* hajtsa végre a megfelelő

## <a name="resource-location"></a>Erőforrás helye
Resource Manager-sablonok segítségével egy helyen attribútum erőforrások üzembe helyezése során. Azure-helyek a például az USA nyugati régiója vagy Délkelet-amerikai régió alatt. Azure verem más helyen mert Azure verem az adatközpontban található.  Annak érdekében sablonok továbbítható Azure és az Azure-verem között, az erőforráscsoport helye kell hivatkozik, az egyes erőforrások központi telepítésekor. Ehhez használja `[resourceGroup().Location]` összes erőforrás öröklik az erőforráscsoport helye biztosításához.  A következő Resource Manager sablon kivonata látható egy példa a storage-fiók üzembe helyezése során ez a funkció használatával:

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

## <a name="next-steps"></a>Következő lépések
* [Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
* [Sablonok az Azure parancssori felület telepítése](azure-stack-deploy-template-command-line.md)
* [Sablonok üzembe helyezése a Visual Studióval](azure-stack-deploy-template-visual-studio.md)

