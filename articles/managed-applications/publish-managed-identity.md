---
title: Az Azure felügyelt alkalmazások felügyelt identitással
description: Ismerje meg, hogy egy felügyelt identitás konfigurálása egy felügyelt alkalmazást. A meglévő erőforrásokhoz kapcsolódó felügyelt alkalmazások üzembe helyezése felügyelt identitás használható biztosítanak az alkalmazások felügyelt kívül a kezelt erőforráscsoport Azure-erőforrások kezeléséhez, és adja meg a felügyelt alkalmazások működési identitást a tevékenységnaplóban és egyéb szolgáltatások Azure-ban.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: 5ef653e825a5f1eb0f5df52f9c2544a5224b34cf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66003446"
---
# <a name="azure-managed-application-with-managed-identity"></a>Az Azure felügyelt alkalmazások felügyelt identitással

> [!NOTE]
> A felügyelt alkalmazások felügyelt identitás támogatása jelenleg előzetes verzióban érhető el. A 2018-09-01-preview api-verzió használatával felügyelt identitást használja.

Ismerje meg, hogy egy felügyelt alkalmazás tartalmaz egy felügyelt identitás konfigurálása. Felügyelt identitás használható, hogy az ügyfél által felügyelt alkalmazás további meglévő erőforrásokhoz való hozzáférést. Az identitás az Azure platform kezeli, és nem igényli, üzembe helyezése és titkos kulcsok elforgatása. Felügyelt identitások az Azure Active Directory (AAD) kapcsolatos további információkért lásd: [felügyelt identitások az Azure-erőforrások](../active-directory/managed-identities-azure-resources/overview.md).

Alkalmazását kétféle típusú identitások adható meg:

- A **rendszer által hozzárendelt identitás** vannak kötve, az alkalmazás és az alkalmazás törlésekor törlődik. Az alkalmazás legfeljebb egy rendszer által hozzárendelt identitás.
- A **felhasználó által hozzárendelt identitás** egy önálló Azure-erőforrás, hozzárendelheti az alkalmazást. Egy alkalmazás több felhasználó által hozzárendelt identitások is rendelkezhet.

## <a name="how-to-use-managed-identity"></a>Felügyelt identitás használata

Felügyelt identitás a felügyelt alkalmazások lehetővé teszi számos forgatókönyv. Néhány olyan gyakori helyzetet megoldhatók a következők:

- Egy felügyelt alkalmazás üzembe helyezése társított, meglévő Azure-erőforrásokhoz. Példa egy Azure virtuális gép (VM) üzembe helyezése belül a felügyelt alkalmazás, amely csatlakozik egy [meglévő hálózati adaptert](../virtual-network/virtual-network-network-interface-vm.md).
- A felügyelt alkalmazások és a közzétevő kívül Azure-erőforrásokhoz való hozzáférés biztosítása a **felügyelt erőforráscsoport**.
- Felügyelt alkalmazások működési identitás biztosítása a tevékenységnapló és egyéb szolgáltatások, Azure-ban.

## <a name="adding-managed-identity"></a>Adding Managed Identity

Az Azure-erőforráson kell beállítani egy új tulajdonság egy felügyelt alkalmazás létrehozása egy felügyelt identitással van szükség. Az alábbi példa megmutatja, **identitás** tulajdonság:

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

A felügyelt alkalmazás létrehozása két gyakori módszer van **identitás**: [CreateUIDefinition.json](./create-uidefinition-overview.md) és [Azure Resource Manager-sablonok](../azure-resource-manager/resource-group-authoring-templates.md). Forgatókönyvek létrehozása egyszerű egyetlen, CreateUIDefinition kell használni ahhoz, hogy a felügyelt identitást, mivel egy gazdagabb felhasználói élményt biztosít. Esetén a speciális vagy összetett igénylő rendszerek automatizált, vagy több felügyelt alkalmazások központi telepítése esetén sablonok segítségével.

### <a name="using-createuidefinition"></a>CreateUIDefinition használatával

Egy felügyelt alkalmazást is konfigurálhatók a felügyelt identitást keresztül a [CreateUIDefinition.json](./create-uidefinition-overview.md). Az a [szakasz kimenete](./create-uidefinition-overview.md#outputs), a kulcs `managedIdentity` bírálja felül az azonosító tulajdonság a felügyelt alkalmazást sablon segítségével. A minta Csengő megjelenését a techeden **alapértelmezett** a felügyelt alkalmazás identitását. A fogyasztó bemenetei között meg CreateUIDefinition elemek használatával összetettebb identitásobjektumok alakítható ki. Ezeket a bemeneteket segítségével hozza létre a felügyelt alkalmazások **felhasználó által hozzárendelt identitás**.

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>Mikor érdemes használni a CreateUIDefinition felügyelt identitás

Az alábbiakban néhány javaslat olvasható a CreateUIDefinition használata a felügyelt alkalmazások a felügyelt identitás engedélyezése.

- A felügyelt alkalmazás létrehozása az Azure portal vagy a Marketplace-en keresztül halad.
- A felügyelt identitás összetett felhasználói adatbevitelt igényel.
- A felügyelt alkalmazás létrehozása a felügyelt identitás van szükség.

#### <a name="systemassigned-createuidefinition"></a>SystemAssigned CreateUIDefinition

Alapszintű CreateUIDefinition, amely lehetővé teszi a SystemAssigned identitását a felügyelt alkalmazáshoz.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
        ],
        "outputs": {
            "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
        }
    }
}
```

#### <a name="userassigned-createuidefinition"></a>UserAssigned CreateUIDefinition

Egy alapszintű CreateUIDefinition, amely egy **felhasználó által hozzárendelt identitás** erőforrás bemeneti és a felügyelt alkalmazás lehetővé teszi, hogy a UserAssigned identitását.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "manageIdentity",
                "label": "Identity",
                "subLabel": {
                    "preValidation": "Manage Identities",
                    "postValidation": "Done"
                },
                "bladeTitle": "Identity",
                "elements": [
                    {
                        "name": "userAssignedText",
                        "type": "Microsoft.Common.TextBox",
                        "label": "User assigned managed identity",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('manageIdentity').userAssignedText),':{}}}'))]"
        }
    }
}
```

A fenti CreateUIDefinition.json állít elő, a létrehozás, amely rendelkezik egy szövegbeviteli mezője a fogyasztó adja meg a felhasználói élmény a **felhasználó által hozzárendelt identitás** Azure erőforrás-azonosítója. A létrehozott felület hasonlóan néz ki:

![Felhasználó által hozzárendelt identitás CreateUIDefinition minta](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager-sablonok használatával

> [!NOTE]
> Marketplace-en felügyelt alkalmazássablonok automatikusan jönnek létre az Azure Portalon keresztül ügyféllel, hozzon létre felhasználói élményt.
> Ebben az esetben a `managedIdentity` engedélyezve van az identitás a CreateUIDefinition kimeneti kulcsot kell használni.

A felügyelt identitás Azure Resource Manager-sablonokkal is engedélyezhető. A minta Csengő megjelenését a techeden **alapértelmezett** a felügyelt alkalmazás identitását. Hogy az Azure Resource Manager-Sablonparaméterek bemenetek révén összetettebb identitásobjektumok alakítható ki. Ezeket a bemeneteket segítségével hozza létre a felügyelt alkalmazások **felhasználó által hozzárendelt identitás**.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>Mikor érdemes használni az Azure Resource Manager-sablonok felügyelt identitás

Az alábbiakban néhány javaslat olvasható a felügyelt identitás a felügyelt alkalmazások engedélyezése az Azure Resource Manager-sablonok használata.

- Felügyelt alkalmazások programozott módon telepíthető egy sablon alapján.
- Egyéni szerepkör-hozzárendeléseit a felügyelt identitás van szükség a felügyelt alkalmazás üzembe helyezése.
- A felügyelt alkalmazásnak nem kell az Azure portál és marketplace létrehozási folyamat.

#### <a name="systemassigned-template"></a>SystemAssigned sablon

Egy Azure Resource Manager-sablont, amely üzembe helyezi egy felügyelt alkalmazás **alapértelmezett** identitás.

```json
"resources": [
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

### <a name="userassigned-template"></a>UserAssigned sablon

Egy Azure Resource Manager-sablont, amely üzembe helyezi egy felügyelt alkalmazás egy **felhasználó által hozzárendelt identitás**.

```json
"resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('managedIdentityName')]",
      "apiVersion": "2018-11-30",
      "location": "[parameters('location')]"
    },
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('managedIdentityName'))]": {}
            }
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

## <a name="granting-access-to-azure-resources"></a>Azure-erőforrásokhoz való hozzáférés biztosítása

Ha egy felügyelt alkalmazás megkapja az identitás, meglévő azure-erőforrásokhoz való hozzáférés meg is megadható. Ez a folyamat a hozzáférés-vezérlés (IAM) felületen az Azure Portalon teheti meg. A felügyelt alkalmazás nevét vagy **felhasználó által hozzárendelt identitás** szerepkör-hozzárendelés hozzáadása lehet keresni.

![Felügyelt alkalmazás szerepkör-hozzárendelés hozzáadása](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Meglévő Azure-erőforrások csatolása

> [!NOTE]
> A **felhasználó által hozzárendelt identitás** kell [konfigurált](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) a felügyelt alkalmazás üzembe helyezése előtt. Emellett társított erőforrások üzembe helyezésének felügyelt alkalmazások csak a támogatott a **marketplace** típusa.

Felügyelt identitás is használható, az üzembe helyezés során a meglévő erőforrásokhoz hozzáférést igénylő felügyelt alkalmazás üzembe helyezése. Ha a felügyelt alkalmazás ki van építve a vásárlók **felhasználó által hozzárendelt identitások** adhatók hozzá további engedélyeket biztosít az **mainTemplate** üzembe helyezési.

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>A társított erőforrás CreateUIDefinition készítése

Amikor olyan meglévő erőforrások, mind a meglévő Azure-erőforrás a felügyelt alkalmazás központi telepítésének és a egy **felhasználó által hozzárendelt identitás** a megfelelő szerepkör-hozzárendelést az adott erőforráshoz meg kell adni.

 A két bemenet igénylő CreateUIDefinition minta: egy hálózati adapter erőforrás-azonosító és a egy felhasználóhoz hozzárendelt identitás erőforrás-azonosító.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "managedApplicationSetting",
                "label": "Managed Application Settings",
                "subLabel": {
                    "preValidation": "Managed Application Settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Managed Application Settings",
                "elements": [
                    {
                        "name": "networkInterfaceId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "network interface resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Network/networkInterfaces/existingnetworkinterface",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.Network/networkInterfaces/networkinterface1",
                        "visible": true
                    },
                    {
                        "name": "userAssignedId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "user assigned identity resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity1",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "existingNetworkInterfaceId": "[steps('managedApplicationSetting').networkInterfaceId]",
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('managedApplicationSetting').userAssignedId),':{}}}'))]"
        }
    }
}
```

Ez CreateUIDefinition.json állít elő, a Létrehozás felhasználói környezet, amely két mezőt tartalmaz. Az első mező lehetővé teszi, hogy a felhasználónak meg kell adnia az Azure erőforrás-azonosító az erőforrás a felügyelt alkalmazás központi telepítése folyamatban van csatolva. A második pedig a fogyasztó a adja meg a **felhasználó által hozzárendelt identitás** Azure-erőforrás azonosítója, amely hozzáfér a társított Azure-erőforráshoz. A létrehozott felület hasonlóan néz ki:

![A két bemenet CreateUIDefinition példa: egy hálózati adapter az erőforrás-azonosító és a egy felhasználóhoz hozzárendelt identitás erőforrás-azonosító](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>A társított erőforrás mainTemplate készítése

Mellett a CreateUIDefinition frissítése, a fő sablont is frissíteni kell a társított erőforrás-azonosítója. az átadott fogadására A fő sablont, fogadja el az új kimeneti paraméter hozzáadásával lehet frissíteni. Mivel a `managedIdentity` kimeneti felülírja az értéket, a létrehozott felügyelt alkalmazás sablonhoz, nem ad át a fő sablont, és nem kell foglalni a Paraméterek szakaszban.

Egy minta fő sablont, amely a hálózati profil beállítja egy meglévő hálózati adaptert a CreateUIDefinition által biztosított.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "existingNetworkInterfaceId": { "type": "string" }
    },
    "variables": {
    },
    "resources": [
        {
            "apiVersion": "2016-04-30-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "myLinkedResourceVM",
            "location": "[resourceGroup().location]",
            "properties": {
                …,
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[parameters('existingNetworkInterfaceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>A felügyelt alkalmazás a hivatkozott erőforrás-felhasználása

A felügyelt alkalmazás-csomag létrehozása után a felügyelt alkalmazás képes használni az Azure Portalon keresztül. Képes használni, mielőtt nincsenek néhány előfeltételként felsorolt lépéseket.

- A szükséges társított Azure-erőforrás egy példányát kell létrehozni.
- A **felhasználó által hozzárendelt identitás** kell [létrehozott, és a megadott szerepkör-hozzárendelések](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) a társított erőforrás.
- A meglévő társított erőforrás-azonosító és a **felhasználó által hozzárendelt identitás** azonosítója a CreateUIDefinition vannak megadva.

## <a name="accessing-the-managed-identity-token"></a>A felügyelt identitás token elérése

A jogkivonat a felügyelt alkalmazás most már keresztül érhetők el a `listTokens` api közzétevő bérlőtől. Egy kérelem (példa) hasonló lehet:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}?api-version=2018-09-01-preview HTTP/1.1
```

Egy mintaválasz hasonló lehet:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json

{
    "value": [
        {
            "access_token": "eyJ0eXAi…",
            "expires_in": "2…",
            "expires_on": "1557…",
            "not_before": "1557…",
            "authorizationAudience": "https://management.azure.com/",
            "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}",
            "token_type": "Bearer"
        }
    ]
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni szolgáltatóval kezelt alkalmazás konfigurálása](./custom-providers-overview.md)