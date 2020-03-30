---
title: Az Azure Service Fabric infrastruktúrája a kód ajánlott eljárások
description: Ajánlott eljárások és tervezési szempontok az Azure Service Fabric infrastruktúra ként kódként való kezeléséhez.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 1c044d5fd973d3c577088a887f2fac413d2ab79d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551828"
---
# <a name="infrastructure-as-code"></a>Infrastruktúra mint kód

Éles környezetben hozzon létre Azure Service Fabric-fürtöket erőforrás-kezelői sablonok használatával. Az Erőforrás-kezelő sablonjai nagyobb ellenőrzést biztosítanak az erőforrás-tulajdonságok felett, és biztosítják, hogy egységes erőforrásmodellel rendelkezzen.

Minta Erőforrás-kezelő sablonok érhetők el a Windows és a Linux az [Azure-minták at GitHub.](https://github.com/Azure-Samples/service-fabric-cluster-templates) Ezek a sablonok kiindulási pontként használhatók a fürtsablonhoz. Töltse `azuredeploy.json` `azuredeploy.parameters.json` le és szerkesztheti őket, hogy megfeleljenek az egyéni követelményeknek.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A fent `azuredeploy.json` `azuredeploy.parameters.json` letöltött sablonok és sablonok központi telepítéséhez használja a következő Azure CLI-parancsokat:

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

Az Azure Resource Manager en keresztül alkalmazásokat és szolgáltatásokat helyezhet üzembe a Service Fabric-fürtre. A részletekért tekintse [meg az Alkalmazások és szolgáltatások kezelése Azure Resource Manager-erőforrás-kezelőként készült erőforrásokat.](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource) Az alábbiakban ajánlott eljárás Service Fabric-alkalmazás specifikus erőforrásokat, amelyek et az Erőforrás-kezelő sablon erőforrások közé foglal.

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

Az alkalmazás üzembe helyezéséhez az Azure Resource Manager használatával először létre kell [hoznia egy sfpkg](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg) Service Fabric-alkalmazáscsomagot. A következő python szkript egy példa arra, hogyan lehet létrehozni egy sfpkg:

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

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Az Azure virtuális gép operációs rendszerének automatikus frissítési konfigurációja 
A virtuális gépek frissítése egy felhasználó által kezdeményezett művelet, és ajánlott a [Virtuálisgép-méretezési készlet automatikus operációs rendszer frissítése](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) az Azure Service Fabric fürtök gazdawebhely-kezeléséhez; Patch Orchestration alkalmazás egy alternatív megoldás, amely nek szánt, ha üzemelteti az Azure-on kívül, bár POA használható az Azure-ban, a terhelési POA az Azure-ban, hogy a közös oka, hogy inkább a virtuális gép operációs rendszer automatikus frissítése a POA helyett. Az automatikus operációsrendszer-frissítés engedélyezéséhez a számítási virtuálisgép-méretezési készlet erőforrás-kezelő sablon tulajdonságai a következők:

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
Automatikus operációsrendszer-frissítések service fabric használataesetén az új operációsrendszer-lemezkép egy frissítési tartomány egy időben, hogy fenntartsák a szolgáltatás fabric futó szolgáltatások magas rendelkezésre állását. Az automatikus operációsrendszer-frissítések szolgáltatásban a Service Fabric a fürt konfigurálni kell a Silver tartóssági szint vagy magasabb használata.

Győződjön meg arról, hogy a következő beállításkulcs hamis értékre van állítva, hogy a Windows gazdagépek ne lehessen koordinálatlan frissítéseket beadni: HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

A számítási virtuálisgép-méretezési erőforrás-kezelő sablon tulajdonságai a WindowsUpdate beállításkulcs false értékűre állításához:
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

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Az Azure Service Fabric fürtfrissítési konfigurációja
A Service Fabric fürterőforrás-kezelő sablontulajdonsága az automatikus frissítés engedélyezéséhez:
```json
"upgradeMode": "Automatic",
```
A fürt manuális frissítéséhez töltse le a cab/deb disztribúciót egy fürt virtuális gépére, majd hívja meg a következő PowerShellt:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>További lépések

* Fürt létrehozása virtuális gépeken vagy Windows Server rendszert futtató számítógépeken: [Service Fabric-fürt létrehozása Windows Server rendszerhez](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Fürt létrehozása virtuális gépeken vagy Linuxot futtató számítógépeken: [Linux-fürt létrehozása](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése
