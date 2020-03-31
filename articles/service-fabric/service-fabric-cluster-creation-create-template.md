---
title: Azure Service Fabric-fürtsablon létrehozása
description: Megtudhatja, hogy miként hozhat létre Erőforrás-kezelő sablont egy Service Fabric-fürthöz. Konfigurálja a biztonságot, az Azure Key Vault és az Azure Active Directory (Azure AD) az ügyfél-hitelesítés.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: a00f2bc505acd89d9fb9488565b6235bf7d146ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258875"
---
# <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Service Fabric-fürterőforrás-kezelő sablon létrehozása

Az [Azure Service Fabric-fürt](service-fabric-deploy-anywhere.md) olyan virtuális gépek hálózathoz csatlakoztatott készlete, amelybe a mikroszolgáltatásokat üzembe helyeziésés kezeli. Az Azure-ban futó Service Fabric-fürt egy Azure-erőforrás, amely üzembe helyezése, kezelése és figyelése az Erőforrás-kezelő használatával történik.  Ez a cikk ismerteti, hogyan hozzon létre egy Erőforrás-kezelő sablont az Azure-ban futó Service Fabric-fürthöz.  Amikor a sablon elkészült, [telepítheti a fürtöt az Azure-ban.](service-fabric-cluster-creation-via-arm.md)

A fürt biztonsága a fürt első beállításakor van konfigurálva, és később nem módosítható. Fürt beállítása előtt olvassa el a [Service Fabric-fürt biztonsági forgatókönyveit.][service-fabric-cluster-security] Az Azure-ban a Service Fabric x509-es tanúsítványt használ a fürt és a végpontok védelmére, az ügyfelek hitelesítésére és az adatok titkosítására. Az Azure Active Directory is ajánlott a felügyeleti végpontokhoz való biztonságos hozzáférés. Az Azure AD-bérlők és a felhasználók létre kell hozni a fürt létrehozása előtt.  További információért olvassa el [az Azure AD beállítása az ügyfelek hitelesítéséhez](service-fabric-cluster-creation-setup-aad.md)című olvassa el.

Mielőtt éles fürtöt telepítene éles számítási feladatok futtatásához, először olvassa el az [éles készenléti ellenőrzőlistát.](service-fabric-production-readiness-checklist.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-the-resource-manager-template"></a>A Resource Manager-sablon létrehozása
Minta Erőforrás-kezelő sablonok érhetők el az [Azure-minták a GitHubon.](https://github.com/Azure-Samples/service-fabric-cluster-templates) Ezek a sablonok kiindulási pontként használhatók a fürtsablonhoz.

Ez a cikk az [ötcsomópontos biztonságos fürt][service-fabric-secure-cluster-5-node-1-nodetype] példasablonját és sablonparamétereit használja. Töltse le *az azuredeploy.json* és *az azuredeploy.parameters.json fájlt* a számítógépre, és nyissa meg mindkét fájlt a kedvenc szövegszerkesztőben.

> [!NOTE]
> Nemzeti felhők (Azure Government, Azure China, Azure Germany) `fabricSettings` esetén a `AADLoginEndpoint` `AADTokenEndpointFormat` következőt is hozzá kell adnia a sablonhoz: és `AADCertEndpointFormat`.

## <a name="add-certificates"></a>Tanúsítványok hozzáadása
Tanúsítványokat adhat hozzá egy fürterőforrás-kezelő sablonhoz a tanúsítványkulcsokat tartalmazó kulcstartóra hivatkozva. Adja hozzá ezeket a key-vault paramétereket és értékeket egy Resource Manager-sablon paraméterfájlban (*azuredeploy.parameters.json*).

### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Az összes tanúsítvány hozzáadása az osProfile virtuálisgép-méretezési csoporthoz
A fürtben telepített összes tanúsítványt a méretezési csoport **erőforrásának osProfile** szakaszában (Microsoft.Compute/virtualMachineScaleSets) kell konfigurálni. Ez a művelet arra utasítja az erőforrás-szolgáltatót, hogy telepítse a tanúsítványt a virtuális gépekre. Ez a telepítés tartalmazza a fürttanúsítványt és az alkalmazásokhoz használni kívánt alkalmazásbiztonsági tanúsítványokat is:

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

### <a name="configure-the-service-fabric-cluster-certificate"></a>A Service Fabric fürttanúsítványkonfigurálása

A fürt hitelesítési tanúsítványát konfigurálni kell mind a Service Fabric fürterőforrásban (Microsoft.ServiceFabric/clusters), mind a Service Fabric bővítményben a virtuálisgép-méretezési készlet erőforrás ban lévő virtuálisgép-méretezési készletekhez. Ez az elrendezés lehetővé teszi, hogy a Service Fabric erőforrás-szolgáltató konfigurálja azt a fürt hitelesítéséhez és a kiszolgáló hitelesítéséhez a felügyeleti végpontokhoz.

#### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Adja hozzá a tanúsítványadatait a virtuálisgép méretezési csoport erőforrás

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "commonNames": ["[parameters('certificateCommonName')]"],
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

#### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>A tanúsítványadatok hozzáadása a Service Fabric fürterőforráshoz

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
  }
}
```

## <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Azure AD-konfiguráció hozzáadása az Azure AD ügyfélhozzáféréshez való használatához

Az Azure AD-konfiguráció t hozzá adja egy fürt erőforrás-kezelő sablonhoz a tanúsítványkulcsokat tartalmazó kulcstartóra hivatkozva. Adja hozzá ezeket az Azure AD-paramétereket és értékeket egy Resource Manager-sablon paraméterfájlban (*azuredeploy.parameters.json).* 

> [!NOTE]
> Linuxon az Azure AD-bérlők és a felhasználók létre kell hozni a fürt létrehozása előtt.  További információért olvassa el [az Azure AD beállítása az ügyfelek hitelesítéséhez](service-fabric-cluster-creation-setup-aad.md)című olvassa el.

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

## <a name="populate-the-parameter-file-with-the-values"></a>A paraméterfájl feltöltése az értékekkel

Végül használja a kimeneti értékeket a key vault és az Azure AD PowerShell-parancsok a paraméterfájl feltöltéséhez.

Ha azt tervezi, hogy az Azure-szolgáltatás fabric RM PowerShell-modulok, majd nem kell feltölteni a fürt tanúsítványadatait. Ha azt szeretné, hogy a rendszer létrehozza az önaláírt tanúsítványt a fürt biztonsága, csak tartsa őket null. 

> [!NOTE]
> Ahhoz, hogy az RM modulok felvegyék és feltöltsék ezeket az üres paraméterértékeket, a paraméterek nevei sokkal megegyeznek az alábbi nevekkel

```json
"clusterCertificateThumbprint": {
    "value": ""
},
"certificateCommonName": {
    "value": ""
},
"clusterCertificateUrlValue": {
    "value": ""
},
"sourceVaultvalue": {
    "value": ""
},
```

Ha alkalmazáscerts, vagy egy meglévő fürt, amely feltöltötte a key vault, be kell szereznie ezt az információt, és töltse fel.

Az RM-modulok nem képesek az Azure AD-konfiguráció létrehozására, így ha az Azure AD-t szeretné használni az ügyfél-hozzáféréshez, fel kell nagyítania azt.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

## <a name="test-your-template"></a>A sablon tesztelése
Az erőforrás-kezelő sablon jának paraméterfájllal való teszteléséhez használja a következő PowerShell-parancsot:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Abban az esetben, ha problémákba ütközik, és rejtélyes üzeneteket kap, használja a "-Debug" opciót.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

Az alábbi ábra bemutatja, hogy a key vault és az Azure AD-konfiguráció hol illeszkedik az Erőforrás-kezelő sablonba.

![Erőforrás-kezelő függőségi leképezése][cluster-security-arm-dependency-map]

## <a name="next-steps"></a>További lépések
Most, hogy rendelkezik egy sablont a fürthöz, ismerje meg, hogyan [telepítheti a fürtöt az Azure-ba.](service-fabric-cluster-creation-via-arm.md)  Ha még nem tette meg, olvassa el az [éles készültségi ellenőrzőlistát](service-fabric-production-readiness-checklist.md) egy éles fürt telepítése előtt.

A json szintaxisáról és a cikkben üzembe helyezett erőforrások tulajdonságairól az alábbi témakörökben olvashat:

* [Microsoft.ServiceFabric/fürtök](/azure/templates/microsoft.servicefabric/clusters)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)

<!-- Links -->
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-create-template/cluster-security-arm-dependency-map.png
