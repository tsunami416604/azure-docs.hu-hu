---
title: Hozzon létre egy Azure Service Fabric-fürt sablon |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre a Service Fabric-fürt Resource Manager-sablonnal. Konfigurálja a biztonsági, az Azure Key Vault és az Azure Active Directory (Azure AD) az ügyfél-hitelesítéshez.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/16/2018
ms.author: aljo
ms.openlocfilehash: 2fdea1f088dd6eabdf7d72342c837d976133a1bc
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046181"
---
# <a name="create-a-service-fabric-cluster-resource-manager-template"></a>A Service Fabric fürt Resource Manager-sablon létrehozása

Egy [Azure Service Fabric-fürt](service-fabric-deploy-anywhere.md) mikroszolgáltatásokat helyezhet üzembe és felügyelhet, amelybe a virtuális gépek hálózaton keresztül csatlakozó készlete. Azure-ban futó Service Fabric-fürt az Azure-erőforrás, és ezt telepítjük, felügyelt és a Resource Manager használatával figyelt.  Ez a cikk azt ismerteti, hogyan hozzon létre egy Resource Manager-sablon az Azure-ban futó Service Fabric-fürtön.  Ha a sablon elkészült, [üzembe helyezése az Azure-beli fürt](service-fabric-cluster-creation-via-arm.md).

Fürtbiztonság van beállítva, amikor a fürt első be van állítva, és később nem módosítható. A fürt beállítása előtt olvassa el a [Service Fabric-fürtök biztonsági forgatókönyveit][service-fabric-cluster-security]. Az Azure-ban a Service Fabric használ x509 tanúsítványt a biztonságos a fürt és a végpontok, az ügyfelek hitelesítése, és adatok titkosításához. Az Azure Active Directory emellett javasoljuk, hogy a biztonságos hozzáférés a felügyeleti végpontok. Az Azure AD-bérlőt, és a felhasználók a fürt létrehozása előtt létre kell hozni.  További információkért olvassa el [állítsa be az Azure AD-ügyfelek hitelesítésére](service-fabric-cluster-creation-setup-aad.md).

Éles számítási feladatok futtatásához egy fürt üzembe helyezése előtt ügyeljen arra, hogy először olvassa el a [éles készültségi ellenőrzőlista](service-fabric-production-readiness-checklist.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-the-resource-manager-template"></a>A Resource Manager-sablon létrehozása
Mintául szolgáló Resource Manager-sablonok érhetők el a [Azure-minták a Githubon](https://github.com/Azure-Samples/service-fabric-cluster-templates). Ezek a sablonok fürtsablonhoz kiindulási pontként használható.

Ez a cikk a [biztonságos fürt ötcsomópontos] [ service-fabric-secure-cluster-5-node-1-nodetype] példasablonja és a sablon paramétereit. Töltse le *azuredeploy.json* és *azuredeploy.parameters.json* a számítógépre, és nyissa meg mindkét fájlt a kedvenc szövegszerkesztőjével.

> [!NOTE]
> Az országos felhők (Azure Government, Azure China, az Azure Germany), emellett adja hozzá a következő `fabricSettings` a sablonhoz: `AADLoginEndpoint`, `AADTokenEndpointFormat` és `AADCertEndpointFormat`.

## <a name="add-certificates"></a>Tanúsítványok hozzáadása
Adja hozzá a tanúsítványok a fürt Resource Manager-sablon, amely tartalmazza a tanúsítvány kulcsait a key vaulttal való hivatkozással. Adja meg ezeket kulcstartó paraméterek és értékek egy Resource Manager-sablon paraméterfájljának (*azuredeploy.parameters.json*).

### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>A virtual machine scale set osProfile minden tanúsítvány felvétele
Telepítve van a fürt minden tanúsítványt kell konfigurálni a **osProfile** szakaszban a skála beállítani az erőforrás (Microsoft.Compute/virtualMachineScaleSets). Ez a művelet arra utasítja az erőforrás-szolgáltató a tanúsítvány telepítése a virtuális gépeken. A telepítés a fürttanúsítvány és az alkalmazásokhoz használni kívánt alkalmazás biztonsági tanúsítványokat is tartalmazza:

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

### <a name="configure-the-service-fabric-cluster-certificate"></a>A Service Fabric-fürt tanúsítvány konfigurálása

Mind a Service Fabric fürterőforrás (Microsoft.ServiceFabric/clusters) a fürt hitelesítési tanúsítványt kell konfigurálni, és beállítja a virtuálisgép-méretezési csoport esetében a Service Fabric-bővítménnyel a virtuálisgép-méretezési készlet erőforrás. Ezzel az elrendezéssel fokozott lehetővé teszi, hogy a Service Fabric erőforrás-szolgáltató számára, konfigurálja a fürt és kiszolgálóhitelesítéssel felügyeleti végpontok használata.

#### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>A tanúsítvány adatait a virtuálisgép-méretezési csoport beállítani az erőforrás hozzáadása

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

#### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>A Tanúsítványadatok hozzáadása a Service Fabric-fürt erőforrás

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

## <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Azure AD használata az ügyfél hozzáférésének az Azure AD-konfiguráció hozzáadása

A fürt Resource Manager-sablon az Azure AD-konfiguráció hozzáadása, amely tartalmazza a tanúsítvány kulcsait a key vaulttal való hivatkozással. Adja hozzá ezeket az Azure AD-paramétereket és értékeket a egy Resource Manager-sablon paraméterfájljának (*azuredeploy.parameters.json*). 

> [!NOTE]
> Az Azure AD-bérlőt, és a felhasználók a fürt létrehozása előtt létre kell hozni.  További információkért olvassa el [állítsa be az Azure AD-ügyfelek hitelesítésére](service-fabric-cluster-creation-setup-aad.md).

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

## <a name="populate-the-parameter-file-with-the-values"></a>Töltse fel értékekkel alkalmazásparaméter-fájlt

Végül a kimeneti értékeket a key vault és az Azure AD PowerShell-parancsok használatával töltse fel a paramétereket tartalmazó fájlt.

Ha azt tervezi, használja az Azure service fabric RM PowerShell-modulok, majd nem kell a fürt Tanúsítványadatok feltöltése. Ha azt szeretné, hogy a rendszer hoz létre a saját aláírt fürt biztonsági tanúsítvány, csak null-ként tartja őket. 

> [!NOTE]
> Az erőforrás-kezelő modulok folytattuk a munkát, és ezek a üres paraméterértékek feltöltéséhez a paraméterek nevei sokkal meg az alábbi névvel

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

Ha alkalmazás tanúsítványokat használ, vagy egy meglévő fürthöz, a key vaulthoz feltöltött használja, meg kell ezt az információt, és feltöltheti.

Az erőforrás-kezelő modulok nem tudnak létrehozni az Azure ad-ben a konfigurációt, ezért ha azt tervezi, az Azure AD használata az ügyfél hozzáférésének, meg kell adnia azt.

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
A következő PowerShell-parancs használatával a Resource Manager-sablon tesztelés alkalmazásparaméter-fájlt:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Abban az esetben problémákat, és rejtélyes üzeneteket, majd a "-Debug" beállítás.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

A következő diagram azt ábrázolja, ahol a key vault és az Azure AD konfigurálása elférnek a Resource Manager-sablon.

![Erőforrás-kezelő függőségi térkép][cluster-security-arm-dependency-map]

## <a name="next-steps"></a>További lépések
Most, hogy a sablon a fürt számára, megtudhatja, hogyan [a fürt üzembe helyezéséhez az Azure-bA](service-fabric-cluster-creation-via-arm.md).  Ha még nem tette, olvassa el a [éles készültségi ellenőrzőlista](service-fabric-production-readiness-checklist.md) egy fürt üzembe helyezése előtt.

A JSON-szintaxist és a tulajdonságai ebben a cikkben üzembe helyezett erőforrásokkal kapcsolatos további információkért lásd:

* [Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters)
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
