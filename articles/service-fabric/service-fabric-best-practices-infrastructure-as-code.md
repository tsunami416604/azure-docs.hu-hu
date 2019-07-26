---
title: Az Azure Service Fabric-infrastruktúra kódja – ajánlott eljárások | Microsoft Docs
description: Ajánlott eljárások a Service Fabric infrastruktúraként való kezeléséhez.
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
ms.openlocfilehash: ae1cd0912733116dce1b550dd937cc9fc5f8737b
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359774"
---
# <a name="infrastructure-as-code"></a>Infrastruktúra mint kód

Éles környezetben hozzon létre Azure Service Fabric-fürtöket Resource Manager-sablonok használatával. A Resource Manager-sablonok nagyobb mértékben szabályozzák az erőforrás-tulajdonságokat, és gondoskodnak arról, hogy konzisztens erőforrás-modell álljon rendelkezésre.

A Windows és Linux rendszerhez készült Resource Manager-sablonok a [githubon elérhető Azure](https://github.com/Azure-Samples/service-fabric-cluster-templates)-mintákon érhetők el. Ezek a sablonok a fürt sablonjának kiindulási pontként használhatók. Töltse `azuredeploy.json` le `azuredeploy.parameters.json` és szerkessze őket, hogy megfeleljenek az egyéni követelményeknek.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A `azuredeploy.json` fent letöltött és `azuredeploy.parameters.json` sablonok üzembe helyezéséhez használja az alábbi Azure CLI-parancsokat:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Erőforrás létrehozása a PowerShell használatával

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Azure Service Fabric-erőforrások

Segítségével az Azure Resource Manageren keresztül helyezhet üzembe alkalmazásokat és szolgáltatásokat a Service Fabric-fürtön. A részletekért lásd: [alkalmazások és szolgáltatások kezelése Azure Resource Manager erőforrásként](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource) . A következőkben az ajánlott eljárások Service Fabric a Resource Manager-sablonok erőforrásaiba felvenni kívánt alkalmazás-specifikus erőforrások.

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

Az alkalmazás Azure Resource Manager használatával történő üzembe helyezéséhez először [létre kell hoznia egy sfpkg](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg) Service Fabric alkalmazáscsomag. A következő Python-szkript egy példa arra, hogyan hozhat létre sfpkg:

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(
    self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(
            root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Azure-beli virtuális gép operációs rendszerének automatikus frissítési konfigurációja 
A virtuális gépek frissítése egy felhasználó által kezdeményezett művelet, ezért javasolt a [virtuálisgép-méretezési csoport automatikus verziófrissítése](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) az Azure Service Fabric-fürtökön, amelyeken a javítások kezelése történik. A patch-előkészítési alkalmazás egy alternatív megoldás, amely az Azure-on kívül üzemeltetett szolgáltatásokhoz készült, bár a POA az Azure-ban használható, és az Azure-ban az Azure-ban futtatott cselekvési terv általános oka a virtuális gép operációs rendszerének automatikus frissítése. több mint cselekvési terv. A következő a számítási virtuálisgép-méretezési csoport Resource Manager-sablonjának tulajdonságai az automatikus operációs rendszer frissítésének engedélyezéséhez:

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
Ha Service Fabric-val automatikus operációsrendszer-frissítést használ, az új operációsrendszer-rendszerkép egyszerre egy frissítési tartományba kerül, hogy fenntartsa a Service Fabric futó szolgáltatások magas rendelkezésre állását. Ha az operációs rendszer automatikus frissítését szeretné használni Service Fabric a fürtöt úgy kell konfigurálni, hogy az ezüst tartóssági szintet vagy ennél nagyobbat használjon.

Győződjön meg arról, hogy a következő beállításkulcs hamis értékre van állítva, hogy megakadályozza, hogy a Windows-gazdagépek ne kezdeményezzenek összehangolatlan frissítéseket: HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

A következő a számítási virtuálisgép-méretezési csoport Resource Manager-sablonjának tulajdonságai a WindowsUpdate beállításkulcs hamis értékre állításához:
```json
"osProfile": {
        "computerNamePrefix": "{vmss-name}",
        "adminUsername": "{your-username}",
        "secrets": [],
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "enableAutomaticUpdates": false
        }
      },
```

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Azure Service Fabric-fürt frissítési konfigurációja
A következő a Service Fabric fürterőforrás-kezelő sablon tulajdonsága az automatikus frissítés engedélyezéséhez:
```json
"upgradeMode": "Automatic",
```
A fürt manuális frissítéséhez töltse le a CAB/deb-eloszlást egy fürtözött virtuális gépre, majd hívja meg a következő PowerShell-t:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>További lépések

* Hozzon létre egy fürtöt a virtuális gépeken vagy a Windows Servert futtató számítógépeken: [Service Fabric fürt létrehozása a Windows Serverhez](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Fürt létrehozása virtuális gépeken vagy Linuxon futó számítógépeken: [Linux-fürt létrehozása](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése
