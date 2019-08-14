---
title: Azure Service Fabric – alkalmazás üzembe helyezése felhasználó által hozzárendelt felügyelt identitással | Microsoft Docs
description: Ez a cikk bemutatja, hogyan telepítheti Service Fabric alkalmazást egy felhasználó által hozzárendelt felügyelt identitással
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 08/09/2019
ms.author: atsenthi
ms.openlocfilehash: b99dbe3fd03b8854d7c1f54d17d5ced1f2534132
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963871"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity"></a>Service Fabric alkalmazás központi telepítése felhasználó által hozzárendelt felügyelt identitással

Service Fabric alkalmazás felügyelt identitással való üzembe helyezéséhez az alkalmazást Azure Resource Manager használatával kell telepíteni, általában egy Azure Resource Manager sablonnal. Az Service Fabric alkalmazásnak a Azure Resource Manager használatával történő központi telepítésével kapcsolatos további információkért lásd: [alkalmazások és szolgáltatások kezelése Azure Resource Manager erőforrásként](service-fabric-application-arm-resource.md).

> [!NOTE] 
> 
> Az Azure-erőforrásként nem telepített alkalmazások **nem** rendelkezhetnek felügyelt identitásokkal. 
>
> A felügyelt identitással Service Fabric alkalmazások telepítése API-verzióval `"2019-06-01-preview"`támogatott. Ugyanezt az API-verziót használhatja az alkalmazás típusa, az alkalmazás típusa és a szolgáltatás erőforrásai esetében is.
>

## <a name="user-assigned-identity"></a>Felhasználó által hozzárendelt identitás

Ha a felhasználó által hozzárendelt identitással szeretné engedélyezni az alkalmazást, először adja hozzá az **Identity** tulajdonságot az alkalmazás-erőforráshoz a következő típusú **userAssigned** és a hivatkozott felhasználó által hozzárendelt identitásokkal. Ezután adjon hozzá egy **managedIdentities** szakaszt a **Properties (Tulajdonságok** ) szakaszban az **alkalmazás** -erőforráshoz, amely a felhasználó által hozzárendelt identitások principalId leképezését tartalmazó rövid név listáját tartalmazza.

### <a name="application-template"></a>Alkalmazássablon

Ha a felhasználóhoz hozzárendelt identitással szeretné engedélyezni az alkalmazást, először adja hozzá az **Identity** tulajdonságot az alkalmazás-erőforráshoz a **userAssigned** típussal és a hivatkozott felhasználóhoz rendelt identitásokkal, majd adjon hozzá egy **managedIdentities** objektumot a **következőn belül a Properties (Tulajdonságok** ) szakasz, amely a felhasználóhoz hozzárendelt identitások leképezéséhez principalId rövid név listáját tartalmazza.

    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
      ],
      "identity": {
        "type" : "userAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]": {}
        }
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        },
        "managedIdentities": [
          {
            "name" : "[parameters('userAssignedIdentityName')]",
            "principalId" : "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName')), '2018-11-30').principalId]"
          }
        ]
      }
    }

A fenti példában a felhasználóhoz rendelt identitás erőforrásának neve megegyezik az alkalmazás felügyelt identitásának rövid nevével. A következő példák azt feltételezik, hogy a tényleges név a "AdminUser".

### <a name="application-package"></a>Alkalmazáscsomag

1. A Azure Resource Manager sablon `managedIdentities` szakaszában meghatározott minden identitáshoz adjon hozzá egy `<ManagedIdentity>` címkét az alkalmazás-jegyzékfájlban a rendszerbiztonsági tag szakaszban. Az `Name` attribútumnak meg kell `name` egyeznie a `managedIdentities` szakaszban meghatározott tulajdonsággal.

    **ApplicationManifest. XML**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. A **ServiceManifestImport** szakaszban adjon hozzá egy **IdentityBindingPolicy** a felügyelt identitást használó szolgáltatáshoz. Ez a szabályzat az `AdminUser` identitást egy olyan szolgáltatás-specifikus identitási névre képezi le, amelyet később fel kell venni a szolgáltatás-jegyzékfájlba.

    **ApplicationManifest. XML**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Frissítse a szolgáltatás jegyzékfájlját, és adjon hozzá egy **ManagedIdentity** az **erőforrások** szakaszban az alkalmazás jegyzékfájljának megfelelő `IdentityBindingPolicy` `ServiceIdentityRef` névvel:

    **ServiceManifest. XML**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>További lépések

* [Felügyelt identitás használata Service Fabric alkalmazás kódjában](how-to-managed-identity-service-fabric-app-code.md)
* [Service Fabric alkalmazás hozzáférésének biztosítása más Azure-erőforrásokhoz](how-to-grant-access-other-resources.md)