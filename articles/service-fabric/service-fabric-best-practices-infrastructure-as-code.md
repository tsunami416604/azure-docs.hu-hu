---
title: Azure Service Fabric infrastruktúra kód gyakorlati tanácsok |} A Microsoft Docs
description: A Service Fabric infrastruktúra mint kód kezelésének ajánlott eljárásai.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 9224ecebed35a631514c5254703ad2694675d40e
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59049620"
---
# <a name="infrastructure-as-code"></a>Infrastruktúra mint kód

Hozzon létre egy éles forgatókönyvet Resource Manager-sablonok használata az Azure Service Fabric-fürtök. Resource Manager-sablonok adja meg az erőforrás-tulajdonságok nagyobb irányítását, és gondoskodjon arról, hogy egységes erőforrás-modellt.

Mintául szolgáló Resource Manager-sablonok érhetők el a Windows és Linux rendszerben az [Azure-minták a Githubon](https://github.com/Azure-Samples/service-fabric-cluster-templates). Ezek a sablonok fürtsablonhoz kiindulási pontként használható. Töltse le `azuredeploy.json` és `azuredeploy.parameters.json` és szerkeszti őket, hogy az egyéni igényeknek.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Üzembe helyezéséhez a `azuredeploy.json` és `azuredeploy.parameters.json` a fent letöltött sablonok használja a következő Azure CLI-parancsokat:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Powershell-lel erőforrás létrehozása

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Azure Service Fabric-erőforrások

Telepíthet alkalmazásokat és szolgáltatásokat az alakzatot a Service Fabric-fürtön az Azure Resource Manageren keresztül. Lásd: [alkalmazások és szolgáltatások Azure Resource Manager-erőforrásként kezelheti](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource) részleteiről. Az alábbi táblázat az ajánlott eljárás a Service Fabric application erőforrásoknál szerepeljenek a Resource Manager-sablon erőforrások.

```json
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

Az Azure Resource Manager az alkalmazás üzembe helyezéséhez először kell [hozzon létre egy sfpkg](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg) Service Fabric-alkalmazás-csomag. A következő python-szkript a következő példa bemutatja, hogyan hozhat létre egy sfpkg:

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="next-steps"></a>További lépések

* Fürt létrehozása a virtuális gépek vagy a Windows Server rendszert futtató számítógépeken: [A Service Fabric-fürt létrehozása a Windows Server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Fürt létrehozása a virtuális gépek vagy a Linux operációs rendszert futtató számítógépeken: [Linux-fürt létrehozása](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése
