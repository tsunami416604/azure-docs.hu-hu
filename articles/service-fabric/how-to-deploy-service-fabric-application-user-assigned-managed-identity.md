---
title: Alkalmazás központi telepítése felhasználó által hozzárendelt felügyelt identitással
description: Ez a cikk bemutatja, hogyan telepítheti Service Fabric alkalmazást egy felhasználó által hozzárendelt felügyelt identitással
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 9aef81db7a455b72c83cf96898a0c228f1c382fd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81415629"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity"></a>Service Fabric alkalmazás központi telepítése felhasználó által hozzárendelt felügyelt identitással

Service Fabric alkalmazás felügyelt identitással való üzembe helyezéséhez az alkalmazást Azure Resource Manager használatával kell telepíteni, általában egy Azure Resource Manager sablonnal. Az Service Fabric alkalmazásnak a Azure Resource Manager használatával történő központi telepítésével kapcsolatos további információkért lásd: [alkalmazások és szolgáltatások kezelése Azure Resource Manager erőforrásként](service-fabric-application-arm-resource.md).

> [!NOTE] 
> 
> Az Azure-erőforrásként nem telepített alkalmazások **nem** rendelkezhetnek felügyelt identitásokkal. 
>
> A felügyelt identitással Service Fabric alkalmazások telepítése API-verzióval támogatott `"2019-06-01-preview"` . Ugyanezt az API-verziót használhatja az alkalmazás típusa, az alkalmazás típusa és a szolgáltatás erőforrásai esetében is.
>

## <a name="user-assigned-identity"></a>Felhasználó által hozzárendelt identitás

Ha a felhasználó által hozzárendelt identitással szeretné engedélyezni az alkalmazást, először adja hozzá az **Identity** tulajdonságot az alkalmazás-erőforráshoz a következő típusú **userAssigned** és a hivatkozott felhasználó által hozzárendelt identitásokkal. Ezután adjon hozzá egy **managedIdentities** szakaszt a **Properties (Tulajdonságok** ) szakaszban az **alkalmazás** -erőforráshoz, amely a felhasználó által hozzárendelt identitások principalId leképezését tartalmazó rövid név listáját tartalmazza. A felhasználó által hozzárendelt identitásokkal kapcsolatos további információkért lásd: [felhasználó által hozzárendelt felügyelt identitás létrehozása, listázása vagy törlése](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell).

### <a name="application-template"></a>Alkalmazássablon

Ha engedélyezni szeretné az alkalmazáshoz a felhasználó által hozzárendelt identitást, először adja hozzá a **userAssigned** és a hivatkozott felhasználóhoz rendelt identitásokhoz tartozó **Identity** tulajdonságot az alkalmazás-erőforráshoz, majd vegyen fel egy **managedIdentities** -objektumot a **Tulajdonságok** szakaszba, amely tartalmazza az egyes felhasználókhoz rendelt identitások principalId leképezéséhez szükséges rövid nevet.

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

1. A Azure Resource Manager sablon szakaszában meghatározott minden identitáshoz `managedIdentities` adjon hozzá egy `<ManagedIdentity>` címkét az alkalmazás-jegyzékfájlban a **rendszerbiztonsági** tag szakaszban. Az `Name` attribútumnak meg kell egyeznie a `name` szakaszban meghatározott tulajdonsággal `managedIdentities` .

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. A **ServiceManifestImport** szakaszban adjon hozzá egy **IdentityBindingPolicy** a felügyelt identitást használó szolgáltatáshoz. Ez a szabályzat az `AdminUser` identitást egy olyan szolgáltatás-specifikus identitási névre képezi le, amelyet később fel kell venni a szolgáltatás-jegyzékfájlba.

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Frissítse a szolgáltatás jegyzékfájlját, és adjon hozzá egy **ManagedIdentity** az **erőforrások** szakaszban az `ServiceIdentityRef` `IdentityBindingPolicy` alkalmazás jegyzékfájljának megfelelő névvel:

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

* [Felügyelt identitás használata Service Fabric alkalmazás kódjában](how-to-managed-identity-service-fabric-app-code.md)
* [Service Fabric alkalmazás hozzáférésének biztosítása más Azure-erőforrásokhoz](how-to-grant-access-other-resources.md)
