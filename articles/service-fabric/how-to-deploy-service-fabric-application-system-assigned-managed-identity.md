---
title: Service Fabric alkalmazás üzembe helyezése rendszer által hozzárendelt MI
description: Ez a cikk bemutatja, hogyan rendelhet hozzá egy rendszerhez rendelt felügyelt identitást egy Azure Service Fabric-alkalmazáshoz
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: d5a14722363d642957904f9c7c699d3cf1d66c0f
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614825"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity-preview"></a>Service Fabric alkalmazás üzembe helyezése rendszer által hozzárendelt felügyelt identitással (előzetes verzió)

Az Azure Service Fabric-alkalmazások felügyelt identitás funkciójának eléréséhez először engedélyeznie kell a felügyelt Identity token szolgáltatást a fürtön. Ez a szolgáltatás felelős a felügyelt identitások használatával Service Fabric alkalmazások hitelesítéséhez, valamint a hozzáférési jogkivonatok nevében való beszerzéséhez. Ha a szolgáltatás engedélyezve van, a bal oldali ablaktábla **System (rendszer** ) szakaszának Service Fabric Explorer alatt láthatja, hogy az egyéb rendszerszolgáltatások mellett a **háló:/System/ManagedIdentityTokenService** néven fut.

> [!NOTE] 
> Service Fabric alkalmazások felügyelt identitásokkal való üzembe helyezése a `"2019-06-01-preview"`API-verziótól kezdődően támogatott. Ugyanezt az API-verziót használhatja az alkalmazás típusa, az alkalmazás típusa és a szolgáltatás erőforrásai esetében is. A Service Fabric futtatókörnyezet minimálisan támogatott 6,5 CU2. A additoin-ben a Build/Package környezetnek az SF .net SDK-t is meg kell adni a CU2 vagy újabb verziónál

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

### <a name="application-template"></a>Alkalmazássablon

Ha egy rendszer által hozzárendelt felügyelt identitással szeretné engedélyezni az alkalmazást, adja hozzá az **Identity** tulajdonságot az alkalmazás-erőforráshoz az alábbi példában látható módon: **systemAssigned** .

```json
    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
      ],
      "identity": {
        "type" : "systemAssigned"
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        }
      }
    }
```
Ez a tulajdonság deklarálja (Azure Resource Manager, valamint a felügyelt identitást és Service Fabric erőforrás-szolgáltatót, hogy az erőforrásnak implicit (`system assigned`) felügyelt identitással kell rendelkeznie.

### <a name="application-and-service-package"></a>Alkalmazás-és szolgáltatáscsomag

1. Frissítse az alkalmazás jegyzékfájlját egy **ManagedIdentity** elem hozzáadásához a **rendszerbiztonsági tag** szakaszban, amely egyetlen bejegyzést tartalmaz az alábbi ábrán látható módon:

    **ApplicationManifest. XML**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    Ez leképezi az alkalmazáshoz rendelt identitást erőforrásként egy rövid névre, az alkalmazást tartalmazó szolgáltatásokhoz való további hozzárendeléshez. 

2. A felügyelt identitást hozzárendelő szolgáltatáshoz tartozó **ServiceManifestImport** szakaszban adjon hozzá egy **IdentityBindingPolicy** elemet az alábbiak szerint:

    **ApplicationManifest. XML**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    Ez az elem az alkalmazás identitását rendeli hozzá a szolgáltatáshoz. a hozzárendelés nélkül a szolgáltatás nem fogja tudni elérni az alkalmazás identitását. A fenti kódrészletben a `SystemAssigned` identitás (amely egy foglalt kulcsszó) a szolgáltatás definíciójában van leképezve a felhasználóbarát név `WebAdmin`alatt.

3. Frissítse a szolgáltatás jegyzékfájlját egy **ManagedIdentity** elemnek az **erőforrások** szakaszban való hozzáadásához az alkalmazás jegyzékfájljának `IdentityBindingPolicy` definíciójában található `ServiceIdentityRef` beállítás értékének megfelelő névvel:

    **ServiceManifest. XML**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    Ez egy, a fentiekben leírtak szerint a szolgáltatás azonosságának megfelelő leképezése, de a szolgáltatás definíciójának szemszögéből. Az identitást itt találja az alkalmazás jegyzékfájljában deklarált, rövid névvel (`WebAdmin`).

## <a name="next-steps"></a>Következő lépések
* [Felügyelt identitások támogatásának](./concepts-managed-identity.md) áttekintése az Azure Service Fabric
* [Új telepítése](./configure-new-azure-service-fabric-enable-managed-identity.md) Azure Service Fabric-fürt felügyelt identitások támogatásával 
* [Felügyelt identitás engedélyezése](./configure-existing-cluster-enable-managed-identity-token-service.md) meglévő Azure Service Fabric-fürtben
* Service Fabric alkalmazás [felügyelt identitásának kihasználása a forráskódból](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure Service Fabric-alkalmazás üzembe helyezése felhasználó által hozzárendelt felügyelt identitással](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Azure Service Fabric-alkalmazások hozzáférésének biztosítása más Azure-erőforrásokhoz](./how-to-grant-access-other-resources.md)
