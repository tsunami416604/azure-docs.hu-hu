---
title: Alkalmazás központi telepítése felhasználó által hozzárendelt felügyelt identitással
description: Ez a cikk bemutatja, hogyan telepítheti a Service Fabric-alkalmazást egy felhasználó által hozzárendelt felügyelt identitással
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 9aef81db7a455b72c83cf96898a0c228f1c382fd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415629"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity"></a>Service Fabric-alkalmazás központi telepítése felhasználó által hozzárendelt felügyelt identitással

A Service Fabric-alkalmazás felügyelt identitással való üzembe helyezéséhez az alkalmazást az Azure Resource Manageren keresztül kell üzembe helyezni, általában egy Azure Resource Manager-sablonnal. A Service Fabric-alkalmazás Azure Resource Manageren keresztüli üzembe helyezéséről az [Alkalmazások és szolgáltatások kezelése Azure Resource Manager-erőforrásokként című](service-fabric-application-arm-resource.md)témakörben talál további információt.

> [!NOTE] 
> 
> Az Azure-erőforrásként nem telepített alkalmazások **nem** rendelkezhetnek felügyelt identitásokkal. 
>
> A Service Fabric alkalmazás felügyelt identitással `"2019-06-01-preview"`történő telepítését az API-verzió támogatja. Ugyanazt az API-verziót is használhatja az alkalmazástípushoz, az alkalmazástípus-verzióhoz és a szolgáltatás-erőforrásokhoz.
>

## <a name="user-assigned-identity"></a>Felhasználóhoz rendelt identitás

Ha engedélyezni szeretné az alkalmazást a Felhasználóhoz rendelt identitással, először adja hozzá az **identitástulajdonságot** az alkalmazáserőforráshoz **userAssigned** típussal és a hivatkozott felhasználó által hozzárendelt identitásokkal. Ezután adjon hozzá egy **managedIdentities** szakaszt az **alkalmazás-erőforrás** **tulajdonságszakaszában,** amely tartalmazza a felhasználó által hozzárendelt identitások számára a rendszeri azonosítók hoztartozók rövid nevének listáját. A Felhasználó által rendelt identitásokról további információt a [Felhasználó által hozzárendelt felügyelt identitás létrehozása vagy törlése](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell)című témakörben talál.

### <a name="application-template"></a>Alkalmazássablon

A User Assigned identitással rendelkező alkalmazás engedélyezéséhez először adjon **hozzá identitástulajdonságot** az alkalmazáserőforráshoz **a UserAssigned** és a hivatkozott felhasználó által hozzárendelt identitások típusával, majd adjon hozzá egy **managedIdentities** objektumot a **tulajdonságok** szakaszon belül, amely tartalmazza az egyes hozzárendelt identitások rövid nevének listáját.

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

A fenti példában a felhasználó által hozzárendelt identitás erőforrásneve az alkalmazás felügyelt identitásának rövid neveként használatos. A következő példák feltételezik, hogy a tényleges rövid név "AdminUser".

### <a name="application-package"></a>Alkalmazáscsomag

1. Az Azure Resource Manager-sablon `managedIdentities` szakaszában meghatározott minden egyes `<ManagedIdentity>` identitáshoz adjon hozzá egy címkét az alkalmazásjegyzékben a **Rendszerbiztonsági tagok** szakaszban. Az `Name` attribútumnak meg `name` kell egyeznie `managedIdentities` a szakaszban meghatározott tulajdonsággal.

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. A **ServiceManifestImport** szakaszban adjon hozzá egy **IdentityBindingPolicy** a felügyelt identitást használó szolgáltatáshoz. Ez a `AdminUser` házirend leképezi az identitást egy szolgáltatás-specifikus identitásnév, amely később hozzá kell adni a szolgáltatásjegyzékbe.

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Frissítse a szolgáltatásjegyzéket egy **ManagedIdentity** hozzáadásához az `ServiceIdentityRef` **Erőforrások** szakaszban az alkalmazásjegyzékben `IdentityBindingPolicy` lévő névvel:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>További lépések

* [Felügyelt identitás használata a Service Fabric alkalmazáskódjában](how-to-managed-identity-service-fabric-app-code.md)
* [A Service Fabric-alkalmazások más Azure-erőforrásokhoz való hozzáférése](how-to-grant-access-other-resources.md)
