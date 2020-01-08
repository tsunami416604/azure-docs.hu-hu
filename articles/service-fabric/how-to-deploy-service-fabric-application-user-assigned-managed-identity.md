---
title: Alkalmazás központi telepítése felhasználó által hozzárendelt felügyelt identitással
description: Ez a cikk bemutatja, hogyan telepítheti Service Fabric alkalmazást egy felhasználó által hozzárendelt felügyelt identitással
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: a5eeaf0d6420fa36c0a78f7553ddfd82197d8ec4
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610335"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity-preview"></a>Service Fabric alkalmazás üzembe helyezése felhasználó által hozzárendelt felügyelt identitással (előzetes verzió)

Service Fabric alkalmazás felügyelt identitással való üzembe helyezéséhez az alkalmazást Azure Resource Manager használatával kell telepíteni, általában egy Azure Resource Manager sablonnal. Az Service Fabric alkalmazásnak a Azure Resource Manager használatával történő központi telepítésével kapcsolatos további információkért lásd: [alkalmazások és szolgáltatások kezelése Azure Resource Manager erőforrásként](service-fabric-application-arm-resource.md).

> [!NOTE] 
> 
> Az Azure-erőforrásként nem telepített alkalmazások **nem** rendelkezhetnek felügyelt identitásokkal. 
>
> A felügyelt identitással Service Fabric alkalmazások telepítése az API-verzióval `"2019-06-01-preview"`támogatott. Ugyanezt az API-verziót használhatja az alkalmazás típusa, az alkalmazás típusa és a szolgáltatás erőforrásai esetében is.
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

1. A Azure Resource Manager sablon `managedIdentities` szakaszában definiált minden identitáshoz vegyen fel egy `<ManagedIdentity>` címkét az alkalmazás jegyzékfájljában a **rendszerbiztonsági** tag szakaszban. A `Name` attribútumnak meg kell egyeznie a `managedIdentities` szakaszban meghatározott `name` tulajdonsággal.

    **ApplicationManifest. XML**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. A **ServiceManifestImport** szakaszban adjon hozzá egy **IdentityBindingPolicy** a felügyelt identitást használó szolgáltatáshoz. Ez a szabályzat a `AdminUser` identitást egy olyan szolgáltatás-specifikus identitási névre képezi, amelyet később hozzá kell adni a szolgáltatás jegyzékfájljában.

    **ApplicationManifest. XML**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Frissítse a szolgáltatás jegyzékfájlját, hogy egy **ManagedIdentity** adjon hozzá az **erőforrások** szakaszban az alkalmazás jegyzékfájljának `IdentityBindingPolicy` `ServiceIdentityRef`nek megfelelő névvel:

    **ServiceManifest. XML**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>Következő lépések

* [Felügyelt identitás használata Service Fabric alkalmazás kódjában](how-to-managed-identity-service-fabric-app-code.md)
* [Service Fabric alkalmazás hozzáférésének biztosítása más Azure-erőforrásokhoz](how-to-grant-access-other-resources.md)
