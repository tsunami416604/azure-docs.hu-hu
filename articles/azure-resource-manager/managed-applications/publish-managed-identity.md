---
title: Felügyelt alkalmazás felügyelt identitással
description: Konfigurálja a felügyelt alkalmazást felügyelt identitással a meglévő erőforrásokhoz való csatoláshoz, az Azure-erőforrások kezeléséhez és a tevékenységnapló működési identitásának biztosításához.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: dbf75262440474c5cb50a6d733ac7cba212b5f3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651656"
---
# <a name="azure-managed-application-with-managed-identity"></a>Azure felügyelt alkalmazás felügyelt identitással

> [!NOTE]
> A felügyelt alkalmazások felügyelt identitástámogatása jelenleg előzetes verzióban érhető el. Kérjük, használja a 2018-09-01-preview api verzió t managed identity.

Megtudhatja, hogy miként konfigurálhat felügyelt alkalmazásokat felügyelt identitás tárolására. A felügyelt identitás segítségével lehetővé teheti az ügyfél számára, hogy a felügyelt alkalmazás számára hozzáférést biztosítson további meglévő erőforrásokhoz. Az identitás t an azure platform kezeli, és nem követeli meg, hogy kiépítse vagy forgassa a titkos kulcsokat. Az Azure Active Directoryban (AAD) felügyelt identitásokról az [Azure-erőforrások felügyelt identitásai](../../active-directory/managed-identities-azure-resources/overview.md)című témakörben van.

A kérelem kétféle identitást kaphat:

- A **rendszer által hozzárendelt identitás** az alkalmazáshoz van kötve, és törlődik, ha az alkalmazás törlődik. Egy alkalmazás csak egy rendszer-hozzárendelt identitással rendelkezhet.
- A **felhasználó által hozzárendelt identitás** egy önálló Azure-erőforrás, amely hozzárendelhető az alkalmazáshoz. Egy alkalmazás több felhasználó által hozzárendelt identitással is rendelkezhet.

## <a name="how-to-use-managed-identity"></a>A felügyelt identitás használata

A felügyelt identitás számos forgatókönyvet tesz lehetővé a felügyelt alkalmazások számára. Néhány megoldható gyakori forgatókönyv a következő:

- Meglévő Azure-erőforrásokhoz kapcsolódó felügyelt alkalmazás üzembe helyezése. Egy példa egy Azure virtuális gép (VM) telepítése a felügyelt alkalmazáson belül, amely egy [meglévő hálózati adapterhez](../../virtual-network/virtual-network-network-interface-vm.md)van csatolva.
- A felügyelt alkalmazás és a közzétevő hozzáférésének biztosítása az Azure-erőforrásokhoz a **felügyelt erőforráscsoporton**kívül.
- A felügyelt alkalmazások tevékenységnaplóhoz és az Azure-on belüli egyéb szolgáltatások működési identitásának biztosítása.

## <a name="adding-managed-identity"></a>Felügyelt identitás hozzáadása

Felügyelt alkalmazás létrehozása felügyelt identitással igényel egy további tulajdonságot kell beállítani az Azure-erőforrás. A következő példa egy **mintaidentitás-tulajdonságot** mutat be:

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

Kétféleképpen hozhat létre **identitással**rendelkező felügyelt alkalmazást: [CreateUIDefinition.json](./create-uidefinition-overview.md) és [Azure Resource Manager-sablonok](../templates/template-syntax.md). Egyszerű egyetlen létrehozási forgatókönyvek, CreateUIDefinition kell használni a felügyelt identitás engedélyezéséhez, mert ez egy gazdagabb élményt nyújt. Azonban, ha olyan fejlett vagy összetett rendszerekkel foglalkozik, amelyek automatizált vagy több felügyelt alkalmazás-telepítést igényelnek, sablonok használhatók.

### <a name="using-createuidefinition"></a>CreateUIDefinition használatával

A felügyelt alkalmazások a [CreateUIDefinition.json](./create-uidefinition-overview.md)segítségével konfigurálhatók felügyelt identitással. A [kimenetek szakaszban](./create-uidefinition-overview.md#outputs) `managedIdentity` a kulcs a felügyelt alkalmazássablon identitástulajdonságának felülbírálására használható. A minta ordít lehetővé teszi a **rendszer által kijelölt** identitás a felügyelt alkalmazás. Összetettebb identitásobjektumok a CreateUIDefinition elemek használatával hozhathatók létre, hogy a fogyasztótól bemeneteket kérjenek. Ezek a bemenetek a felhasználó **által hozzárendelt identitással**rendelkező felügyelt alkalmazások létrehozásához használhatók.

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>Mikor kell használni a CreateUIDefinition definíciót a felügyelt identitáshoz?

Az alábbiakban néhány javaslatot, hogy mikor kell használni CreateUIDefinition a felügyelt identitás felügyelt alkalmazások on managed identity engedélyezéséhez.

- A felügyelt alkalmazás létrehozása az Azure Portalon vagy a piactéren keresztül történik.
- A felügyelt identitás összetett fogyasztói bevitelt igényel.
- A felügyelt identitás ra van szükség a felügyelt alkalmazás létrehozása.

#### <a name="systemassigned-createuidefinition"></a>SystemAssigned CreateUIDefinition

Egy alapvető CreateUIDefinition, amely lehetővé teszi a SystemAssigned identitása a felügyelt alkalmazás.

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

Egy alapvető CreateUIDefinition, amely egy **felhasználó által hozzárendelt** identitás-erőforrást vesz igénybe bemenetként, és engedélyezi a UserAssigned identitást a felügyelt alkalmazáshoz.

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

A createUIDefinition.json fenti létrehoz egy felhasználói élményt, amely egy szövegdobozt a fogyasztó számára, hogy adja meg a **felhasználó által hozzárendelt identitás** Azure-erőforrás-azonosító. A létrehozott élmény így nézne ki:

![Minta felhasználó által hozzárendelt identitás CreateUIDefinition](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok használata

> [!NOTE]
> Marketplace-alapú felügyelt alkalmazássablonok automatikusan létrejön az ügyfelek megy keresztül az Azure Portal létrehozása élményt.
> Ezeknél az esetekben a `managedIdentity` CreateUIDefinition kimeneti kulcsát kell használni az identitás engedélyezéséhez.

A felügyelt identitás azure Resource Manager-sablonokon keresztül is engedélyezhető. A minta ordít lehetővé teszi a **rendszer által kijelölt** identitás a felügyelt alkalmazás. Összetettebb identitásobjektumok hozhatnak létre az Azure Resource Manager sablon paramétereivel a bemenetek biztosításához. Ezek a bemenetek a felhasználó **által hozzárendelt identitással**rendelkező felügyelt alkalmazások létrehozásához használhatók.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>Mikor kell használni az Azure Resource Manager-sablonokat a felügyelt identitáshoz?

Az alábbiakban néhány javaslatot, hogy mikor kell használni az Azure Resource Manager-sablonok a felügyelt identitás felügyelt alkalmazásokon való engedélyezéséhez.

- A felügyelt alkalmazások programozott módon telepíthetők egy sablon alapján.
- A felügyelt identitás egyéni szerepkör-hozzárendelések szükségesek a felügyelt alkalmazás kiépítéséhez.
- A felügyelt alkalmazásnak nincs szüksége az Azure Portalra és a piactér létrehozási folyamatára.

#### <a name="systemassigned-template"></a>SystemAssigned sablon

Egy alapvető Azure Resource Manager-sablon, amely egy felügyelt alkalmazást telepít **rendszeráltal hozzárendelt** identitással.

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

### <a name="userassigned-template"></a>Felhasználóhozzárendelt sablon

Egy alapvető Azure Resource Manager-sablon, amely egy felügyelt alkalmazást telepít **felhasználó által hozzárendelt identitással.**

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

## <a name="granting-access-to-azure-resources"></a>Hozzáférés biztosítása az Azure-erőforrásokhoz

Miután egy felügyelt alkalmazás megkapta az identitást, hozzáférést kaphat a meglévő azure-erőforrásokhoz. Ez a folyamat az Azure Portalon található Hozzáférés-vezérlési (IAM) felületen keresztül hajtható el. A felügyelt alkalmazás vagy a felhasználó által hozzárendelt identitás neve megkereshető egy **szerepkör-hozzárendelés** hozzáadásához.

![Szerepkör-hozzárendelés hozzáadása a felügyelt alkalmazáshoz](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Meglévő Azure-erőforrások összekapcsolása

> [!NOTE]
> A felügyelt alkalmazás telepítése előtt [konfigurálni](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) kell egy **felhasználó által hozzárendelt identitást.** Emellett a felügyelt alkalmazások kapcsolódó erőforrás-telepítése csak a **piactér-típusú** támogatott.

Felügyelt identitás is használható egy felügyelt alkalmazás, amely hozzáférést igényel a meglévő erőforrások üzembe helyezése során. Ha a felügyelt alkalmazás kiépítése az ügyfél, **a felhasználó által hozzárendelt identitások** adhatók hozzá, hogy további engedélyeket a **mainTemplate** központi telepítés.

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>A CreateUIDefinition létrehozása csatolt erőforrással

A felügyelt alkalmazás meglévő erőforrásokkal való összekapcsolásakor mind a meglévő Azure-erőforrást, mind pedig a **felhasználó által hozzárendelt identitást** az adott erőforrásra vonatkozó szerepkör-hozzárendeléssel kell biztosítani.

 Egy createUIDefinition minta, amely két bemenetet igényel: egy hálózati adapter erőforrás-azonosítóját és egy felhasználó által hozzárendelt identitás-erőforrás azonosítót.

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

Ez a CreateUIDefinition.json két mezőt tartalmazó létrehozási felhasználói élményt hoz létre. Az első mező lehetővé teszi, hogy a felhasználó adja meg az Azure-erőforrás-azonosító a felügyelt alkalmazás központi telepítéséhez kapcsolódó erőforrás. A második az, hogy a fogyasztó adja meg a **felhasználó által hozzárendelt identitás** Azure-erőforrás-azonosító, amely hozzáfér a csatolt Azure-erőforrás. A létrehozott élmény így nézne ki:

![Példa CreateUIDefinition két bemenettel: egy hálózati adapter erőforrásazonosítója és egy felhasználó által hozzárendelt identitás-erőforrás azonosítója](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>A fősablon szerkesztése csatolt erőforrással

A CreateUIDefinition frissítése mellett a fő sablont is frissíteni kell, hogy elfogadja az átadott erőforrás-azonosítót. A fő sablon frissíthető, hogy elfogadja az új kimenetet egy új paraméter hozzáadásával. Mivel `managedIdentity` a kimenet felülbírálja a létrehozott felügyelt alkalmazássablon értékét, nem kerül át a fő sablonba, és nem szerepelhet a paraméterek szakaszban.

Egy főmintasablon, amely a hálózati profilt a CreateUIDefinition által biztosított meglévő hálózati adapterre állítja be.

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

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>A felügyelt alkalmazás felhasználása csatolt erőforrással

A felügyelt alkalmazáscsomag létrehozása után a felügyelt alkalmazás az Azure Portalon keresztül használható fel. Felhasználás előtt számos előfeltételi lépés létezik.

- Létre kell hozni a szükséges csatolt Azure-erőforrás egy példányát.
- A **felhasználó által hozzárendelt identitást** létre kell [hozni, és szerepkör-hozzárendeléseket](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) kell adni a csatolt erőforráshoz.
- A createUIDefinition a meglévő csatolt erőforrás-azonosítót és a **felhasználó által hozzárendelt identitásazonosítót** biztosítja.

## <a name="accessing-the-managed-identity-token"></a>A felügyelt identitásjogkivonat elérése

A felügyelt alkalmazás jogkivonata most már `listTokens` elérhető az api-n keresztül a kiadó i. Egy példakérés így nézhet ki:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1

{
    "authorizationAudience": "https://management.azure.com/",
    "userAssignedIdentities": [
        "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userAssignedIdentityName}"
    ]
}
```

Kérelem törzsének paraméterei:

Paraméter | Kötelező | Leírás
---|---|---
authorizationAudience (engedélyezés– Közönség | *nem* | A célerőforrás alkalmazásazonosító-URI-ja. Ez is `aud` a (közönség) jogcím a kiadott jogkivonat. Az alapértelmezett értékhttps://management.azure.com/" " "
userAssignedIdentities | *nem* | A felhasználó által hozzárendelt felügyelt identitások listája, amelyhez egy jogkivonatot le szeretne kérni. Ha nincs megadva, `listTokens` visszaadja a rendszer által hozzárendelt felügyelt identitás jogkivonatát.


A mintaválasz így nézhet ki:

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

A válasz a `value` tulajdonság alatt egy jogkivonattömböt fog tartalmazni:

Paraméter | Leírás
---|---
access_token | A kért hozzáférési jogkivonat.
expires_in | A hozzáférési jogkivonat érvényességi ideje.
expires_on | A hozzáférési jogkivonat lejáratának időtartománya. Ez a korszaktól másodpercnyi másodpercben jelenik meg.
not_before | A hozzáférési jogkivonat érvénybe lépésének időtartománya. Ez a korszaktól másodpercnyi másodpercben jelenik meg.
authorizationAudience (engedélyezés– Közönség | A `aud` (közönség) a hozzáférési jogkivonat volt a kérelem. Ez ugyanaz, mint amit a `listTokens` kérelemben nyújtottak.
resourceId | A kiadott jogkivonat Azure-erőforrásazonosítója. Ez vagy a felügyelt alkalmazásazonosító, vagy a felhasználó által hozzárendelt identitásazonosító.
token_type | A token típusa.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Felügyelt alkalmazás konfigurálása egyéni szolgáltatóval](../custom-providers/overview.md)
