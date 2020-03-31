---
title: Service Fabric-alkalmazás központi telepítése rendszeráltal hozzárendelt hibajelzővel
description: Ez a cikk bemutatja, hogyan rendelhet hozzá egy rendszer által hozzárendelt felügyelt identitást egy Azure Service Fabric-alkalmazáshoz
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: d5a14722363d642957904f9c7c699d3cf1d66c0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614825"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity-preview"></a>Service Fabric-alkalmazás központi telepítése rendszerhez rendelt felügyelt identitással (előzetes verzió)

Az Azure Service Fabric-alkalmazások felügyelt identitásfunkciójának eléréséhez először engedélyeznie kell a felügyelt identitásjogkivonat-szolgáltatást a fürtön. Ez a szolgáltatás felelős a Service Fabric-alkalmazások felügyelt identitások használatával történő hitelesítésért, valamint a hozzáférési jogkivonatok nevükben történő beszerzéséért. Ha a szolgáltatás engedélyezve van, láthatja, hogy a Service Fabric Explorer a **rendszer** szakaszban a bal oldali ablaktáblában, fut a neve **fabric:/System/ManagedIdentityTokenService** mellett más rendszerszolgáltatások.

> [!NOTE] 
> A Felügyelt identitású Service Fabric-alkalmazások telepítése `"2019-06-01-preview"`az API-verzióval kezdve támogatott. Ugyanazt az API-verziót is használhatja az alkalmazástípushoz, az alkalmazástípus-verzióhoz és a szolgáltatás-erőforrásokhoz. A minimálisan támogatott Service Fabric futásidő 6,5 CU2. Az additoin, a build / csomag környezetben is kell az SF .Net SDK a CU2 vagy magasabb

## <a name="system-assigned-managed-identity"></a>Rendszerhez rendelt felügyelt identitás

### <a name="application-template"></a>Alkalmazássablon

A rendszer által hozzárendelt felügyelt identitással rendelkező alkalmazás engedélyezéséhez adja hozzá az **identitástulajdonságot** az alkalmazás-erőforráshoz, a **típus-rendszerhez rendelve** az alábbi példában látható módon:

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
Ez a tulajdonság deklarálja (az Azure Resource Manager, illetve a felügyelt identitás és a`system assigned`szolgáltatás háló erőforrás-szolgáltatók, illetve, hogy ez az erőforrás implicit ( ) felügyelt identitással kell rendelkeznie.

### <a name="application-and-service-package"></a>Alkalmazás- és szolgáltatáscsomag

1. Frissítse az alkalmazásjegyzéket, hogy hozzáadjon egy **ManagedIdentity** elemet a **Rendszerelemek** szakaszban, amely egyetlen bejegyzést tartalmaz az alábbi módon:

    **ApplicationManifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    Ez leképezi az alkalmazáshoz rendelt identitást erőforrásként egy rövid névhez, az alkalmazást alkotó szolgáltatások további hozzárendeléséhez. 

2. A felügyelt identitáshoz rendelt szolgáltatásnak megfelelő **ServiceManifestImport** szakaszban adjon hozzá egy **IdentityBindingPolicy** elemet az alábbiak szerint:

    **ApplicationManifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    Ez az elem az alkalmazás identitását rendeli a szolgáltatáshoz; e hozzárendelés nélkül a szolgáltatás nem lesz képes hozzáférni az alkalmazás identitásához. A fenti kódrészletben `SystemAssigned` az identitás (amely fenntartott kulcsszó) a rövid név `WebAdmin`alatt a szolgáltatás definíciójára van leképezve.

3. Frissítse a szolgáltatásjegyzéket egy **ManagedIdentity** elem hozzáadásához az **Erőforrások** `ServiceIdentityRef` szakaszban `IdentityBindingPolicy` az alkalmazásjegyzékben lévő definíció értékének megfelelő névvel:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    Ez az identitás egyenértékű leképezése egy szolgáltatáshoz a fent leírtak szerint, de a szolgáltatásdefiníció szempontjából. Az identitásra itt a rövid neve`WebAdmin`( ), az alkalmazásjegyzékben deklarált neve hivatkozik.

## <a name="next-steps"></a>Következő lépések
* [Felügyelt identitástámogatás áttekintése](./concepts-managed-identity.md) az Azure Service Fabricben
* [Új telepítés](./configure-new-azure-service-fabric-enable-managed-identity.md) Azure Service Fabric-fürt felügyelt identitástámogatással 
* [Felügyelt identitás engedélyezése](./configure-existing-cluster-enable-managed-identity-token-service.md) meglévő Azure Service Fabric-fürtben
* A Service Fabric-alkalmazások felügyelt identitásának kihasználása [a forráskódból](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure Service Fabric-alkalmazás üzembe helyezése felhasználó által hozzárendelt felügyelt identitással](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Azure Service Fabric-alkalmazás elérésének biztosítása más Azure-erőforrásokhoz](./how-to-grant-access-other-resources.md)
