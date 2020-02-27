---
title: Ügyfél előkészítése az Azure által delegált erőforrás-kezeléshez
description: Ismerje meg, hogyan végezheti el az ügyfelek bevezetését az Azure-beli delegált erőforrás-kezelési szolgáltatásba, így az erőforrásaik a saját bérlőn keresztül érhetők el és kezelhetők.
ms.date: 01/20/2020
ms.topic: conceptual
ms.openlocfilehash: 33cf880098e174c2c230a3d78e125ad8df7d894a
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649789"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Ügyfél előkészítése az Azure által delegált erőforrás-kezeléshez

Ez a cikk azt ismerteti, hogy Ön, mint szolgáltató, hogyan helyezhet üzembe egy ügyfelet az Azure által delegált erőforrás-kezelésben, lehetővé téve a delegált erőforrások (előfizetések és/vagy erőforráscsoportok) elérését és kezelését a saját Azure Active Directory (Azure AD) bérlőn keresztül. Noha a szolgáltatók és az ügyfelekre is hivatkozunk, a [több bérlőt kezelő vállalatok](../concepts/enterprise.md) ugyanazt a folyamatot használhatják a kezelési élményük megszilárdítására.

Ezt a folyamatot megismételheti, ha több ügyfél erőforrásait kezeli. Ezután, amikor egy jogosult felhasználó bejelentkezik a bérlőbe, a felhasználó jogosult lehet az ügyfél-kihelyezés hatókörében a felügyeleti műveletek végrehajtására anélkül, hogy be kellene jelentkeznie minden egyes ügyfél-bérlőre.

Ha nyomon szeretné követni az ügyfelek bevonásait és az elismerést, társítsa a Microsoft Partner Network (MPN) AZONOSÍTÓját legalább egy olyan felhasználói fiókkal, amely hozzáfér a beérkező előfizetésekhez. Vegye figyelembe, hogy ezt a társítást a szolgáltatói bérlőben kell végrehajtania. Az egyszerűség kedvéért javasoljuk, hogy hozzon létre egy egyszerű szolgáltatásnevet a bérlőben, amely az MPN-AZONOSÍTÓhoz van társítva, és hogy az olvasó hozzáférést biztosítson az összes felhasználóhoz. További információ: [partner-azonosító csatolása az Azure-fiókokhoz](../../billing/billing-partner-admin-link-started.md). 

> [!NOTE]
> Az ügyfelek akkor is bevonhatók, ha az Azure Marketplace-en közzétett, felügyelt szolgáltatások (nyilvános vagy privát) vásárlási ajánlatot vásárolnak. További információ: [felügyelt szolgáltatások ajánlatának közzététele az Azure Marketplace-](publish-managed-services-offers.md)en. Az itt ismertetett bevezetési folyamatot az Azure Marketplace-en közzétett ajánlattal együtt is használhatja.

A bevezetési folyamathoz a szolgáltató bérlője és az ügyfél bérlője között végrehajtandó műveletek szükségesek. A fenti lépéseket a cikk ismerteti.

> [!IMPORTANT]
> Az Azure-beli delegált erőforrás-kezeléshez jelenleg nem lehet előfizetést (vagy erőforráscsoportot) előkészíteni, ha az előfizetés Azure Databricks használ. Hasonlóképpen, ha regisztrálva van egy előfizetés a **Microsoft. ManagedServices** erőforrás-szolgáltatóval való bevezetéshez, jelenleg nem fog tudni Databricks-munkaterületet létrehozni az adott előfizetéshez.

## <a name="gather-tenant-and-subscription-details"></a>Bérlői és előfizetési adatok összegyűjtése

Az ügyfél bérlője számára aktív Azure-előfizetéssel kell rendelkeznie. Ismernie kell a következőket:

- A szolgáltató bérlője bérlői azonosítója (ahol az ügyfél erőforrásait fogja kezelni)
- Az ügyfél bérlője bérlői azonosítója (amelynek erőforrásai a szolgáltató által felügyelt erőforrások lesznek)
- Az ügyfél bérlője által felügyelt minden egyes előfizetéshez tartozó előfizetési azonosítók (vagy a szolgáltató által felügyelt erőforráscsoport (oka) t tartalmazza).

> [!NOTE]
> Még ha csak egy vagy több erőforráscsoportot szeretne előkészíteni egy előfizetésen belül, a központi telepítést az előfizetés szintjén kell elvégezni, így szüksége lesz az előfizetés-AZONOSÍTÓra.

Ha még nem rendelkezik ezekkel az azonosító értékekkel, a következő módokon kérheti le őket. Ügyeljen rá, hogy ezeket a pontos értékeket használja az üzemelő példányban.

### <a name="azure-portal"></a>Azure Portal

A bérlő AZONOSÍTÓját a Azure Portal jobb felső sarkában lévő fiók neve fölé helyezve, vagy a **könyvtár váltása**lehetőség kiválasztásával lehet látni. A bérlői azonosító kiválasztásához és másolásához keressen a "Azure Active Directory" kifejezésre a portálon, majd válassza a **Tulajdonságok** lehetőséget, és másolja ki a **címtár-azonosító** mezőben megjelenő értéket. Az előfizetés ügyfél-bérlőben történő megkereséséhez keressen rá az "előfizetések" kifejezésre, majd válassza ki a megfelelő előfizetés-azonosítót.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> Ha egy előfizetést (vagy egy vagy több, az előfizetésen belüli erőforráscsoportot) készít az itt leírt eljárással, a **Microsoft. ManagedServices** erőforrás-szolgáltató regisztrálva lesz az adott előfizetéshez.

## <a name="define-roles-and-permissions"></a>Szerepkörök és engedélyek definiálása

Szolgáltatóként több feladatot is el lehet végezni egyetlen ügyfél számára, eltérő hozzáférésre van szükség a különböző hatókörökhöz. Annyi jogosultságot is megadhat, amennyi szükséges ahhoz, hogy [szerepköralapú hozzáférés-vezérlési (RBAC) szerepköröket](../../role-based-access-control/built-in-roles.md) rendeljen a bérlő felhasználói számára.

A felügyelet egyszerűbbé tételéhez ajánlott az Azure AD felhasználói csoportok használata az egyes szerepkörökhöz, ami lehetővé teszi az egyes felhasználók hozzáadását vagy eltávolítását, nem pedig közvetlenül az adott felhasználóhoz rendel hozzá engedélyeket. Az is előfordulhat, hogy szerepköröket szeretne hozzárendelni egy egyszerű szolgáltatáshoz. Ügyeljen arra, hogy kövesse a legalacsonyabb jogosultsági szint elvét, hogy a felhasználók csak a feladataik elvégzéséhez szükséges engedélyekkel rendelkezzenek. A támogatott szerepkörökkel kapcsolatos javaslatokért és információkért lásd: [bérlők, felhasználók és szerepkörök az Azure Lighthouse-forgatókönyvekben](../concepts/tenants-users-roles.md).

Az engedélyek definiálásához ismernie kell az egyes felhasználók, felhasználói csoportok vagy egyszerű szolgáltatásnév azonosító értékeit abban a szolgáltatói bérlőn, amelyhez hozzáférést szeretne biztosítani. A hozzárendelni kívánt beépített szerepkörökhöz is szüksége lesz a szerepkör-definíciós AZONOSÍTÓra. Ha még nem rendelkezik velük, lekérheti őket az alábbi parancsok futtatásával a szolgáltatói bérlőn belül.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>').objectId

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --upn-or-object-id "<yourUPN>" –-query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```
> [!TIP]
> Javasoljuk, hogy a [felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepkörét](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) a felhasználó bevezetéséhez rendelje hozzá, hogy a bérlő felhasználói szükség esetén később is [el tudják távolítani a delegáláshoz való hozzáférést](#remove-access-to-a-delegation) . Ha ez a szerepkör nincs hozzárendelve, a delegált erőforrásokat csak egy felhasználó távolíthatja el az ügyfél bérlője számára.

## <a name="create-an-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon létrehozása

Az ügyfél beléptetéséhez létre kell hoznia egy [Azure Resource Manager](../../azure-resource-manager/index.yml) sablont az ajánlatához a következő információkkal. A **mspOfferName** és a **mspOfferDescription** értékek láthatók lesznek az ügyfél számára, amikor a Azure Portal [szolgáltató lapján](view-manage-service-providers.md) megtekinti az ajánlat részleteit.

|Mező  |Meghatározás  |
|---------|---------|
|**mspOfferName**     |A definíciót leíró név. Ez az érték jelenik meg az ügyfél számára az ajánlat címeként.         |
|**mspOfferDescription**     |Az ajánlat rövid leírása (például "contoso VM Management ajánlat").      |
|**managedByTenantId**     |A bérlő azonosítója.          |
|**engedélyek**     |A bérlőből származó felhasználók/csoportok/SPN- **principalId** értékei **, amelyek segítségével** az ügyfelek megismerhetik az engedélyezés célját, és egy beépített **roleDefinitionId** értékre vannak leképezve, hogy megadják a hozzáférési szintet.      |

A bevezetési folyamathoz szükség van egy Azure Resource Manager sablonra (a [mintákat](https://github.com/Azure/Azure-Lighthouse-samples/)tartalmazó tárházban), valamint egy megfelelő, a konfigurációnak megfelelően módosított paramétereket tartalmazó fájlra és az engedélyek megadására.

A választott sablon attól függ, hogy teljes előfizetést, erőforráscsoportot vagy több erőforráscsoportot készít elő egy előfizetésen belül. Egy olyan sablont is biztosítunk, amely az Azure Marketplace-en közzétett, felügyelt szolgáltatási ajánlatot megvásárló ügyfelek számára is felhasználható, ha így szeretne előfizetni.

|A beléptetéshez  |Azure Resource Manager sablon használata  |A paraméter fájljának módosítása |
|---------|---------|---------|
|Előfizetést   |[delegatedResourceManagement. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement. Parameters. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Erőforráscsoport   |[rgDelegatedResourceManagement. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement. Parameters. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Több erőforráscsoport egy előfizetésen belül   |[multipleRgDelegatedResourceManagement. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement. Parameters. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Előfizetés (az Azure Marketplace-en közzétett ajánlat használata esetén)   |[marketplaceDelegatedResourceManagement. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement. Parameters. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Az itt ismertetett folyamat külön előfizetési szintű üzembe helyezést igényel minden előfizetéshez, még akkor is, ha az előfizetések ugyanabban az ügyfél-bérlőben vannak bevezetésben. A különálló központi telepítések akkor is szükségesek, ha több erőforráscsoportot is előkészít ugyanazon ügyfél bérlője különböző előfizetéseken belül. Egy előfizetésen belül több erőforráscsoport bevezetését azonban egyetlen előfizetési szintű telepítésben is elvégezheti.
>
> Külön központi telepítések is szükségesek ahhoz, hogy több ajánlat is alkalmazható legyen ugyanarra az előfizetésre (vagy az előfizetésen belüli erőforráscsoportok). Minden egyes alkalmazásnak eltérő **mspOfferName**kell használnia.

Az alábbi példa egy módosított **delegatedResourceManagement. Parameters. JSON** fájlt mutat be, amely egy előfizetés bevezetésére használható. Az erőforráscsoport-paraméter fájljai (az [RG-delegált erőforrás-kezelő](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) mappában találhatók) hasonlóak, de tartalmaznak egy **rgName** paramétert is a bevezetéshez megadott erőforráscsoport (ok) azonosításához.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```

A fenti példában szereplő utolsó engedély egy **principalId** hoz létre a felhasználói hozzáférés rendszergazdai szerepkörrel (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). A szerepkör kiosztásakor meg kell adnia a **delegatedRoleDefinitionIds** tulajdonságot, és egy vagy több beépített szerepkört. Az ebben az engedélyben létrehozott felhasználó ezeket a beépített szerepköröket hozzárendelheti az ügyfél bérlője [felügyelt identitásához](../../active-directory/managed-identities-azure-resources/overview.md) , ami szükséges a [szervizelhető házirendek telepítéséhez](deploy-policy-remediation.md). Erre a felhasználóra nem vonatkozik a felhasználói hozzáférés rendszergazdai szerepkörhöz tartozó egyéb engedélyek.

## <a name="deploy-the-azure-resource-manager-templates"></a>A Azure Resource Manager-sablonok üzembe helyezése

A paramétert tartalmazó fájl frissítése után az ügyfél bérlője a Azure Resource Manager sablont a bérlőn belül, előfizetési szintű telepítésként kell telepítenie. Külön üzembe helyezésre van szükség minden olyan előfizetés esetében, amelyet be szeretne készíteni az Azure-beli delegált erőforrás-kezelésbe (vagy minden olyan előfizetéshez, amely a bevezetéshez használni kívánt erőforráscsoportokat tartalmaz).

Mivel ez egy előfizetési szintű telepítés, nem indítható el a Azure Portalban. A központi telepítés a PowerShell vagy az Azure CLI használatával végezhető el, az alább látható módon.

> [!IMPORTANT]
> Ezt az előfizetési szintű üzembe helyezést egy nem vendég fióknak kell végrehajtania az ügyfél bérlője számára, aki az előfizetéshez tartozó [tulajdonos beépített szerepkörrel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) rendelkezik (vagy amely tartalmazza az előkészítés alatt álló erőforráscsoportokat). Ha szeretné megtekinteni az összes olyan felhasználót, aki delegálhatja az előfizetést, az ügyfél bérlője kiválaszthatja az előfizetést a Azure Portalban, megnyithatja a **hozzáférés-vezérlés (iam)** elemet, és [megtekintheti a tulajdonosi szerepkörrel rendelkező felhasználókat](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>Sikeres előkészítés megerősítése

Ha az ügyfél-előfizetés sikeresen bekerült az Azure-beli delegált erőforrás-kezelésbe, a szolgáltató bérlője felhasználói láthatják az előfizetést és annak erőforrásait (ha a fenti folyamaton keresztül kaptak hozzáférést hozzájuk. egyénileg vagy egy Azure AD-csoport tagjaként a megfelelő engedélyekkel. Ennek megerősítéséhez győződjön meg arról, hogy az előfizetés az alábbi módszerek egyikével jelenik meg.  

### <a name="azure-portal"></a>Azure Portal

A szolgáltató bérlője:

1. Navigáljon a [saját ügyfelek oldalra](view-manage-customers.md).
2. Válassza az **ügyfelek**lehetőséget.
3. Győződjön meg arról, hogy az előfizetés (ok) a Resource Manager-sablonban megadott ajánlat nevével jelenik meg.

> [!IMPORTANT]
> Ha szeretné megtekinteni a delegált előfizetést az [ügyfeleken](view-manage-customers.md), a szolgáltató bérlője felhasználóinak meg kell adni az [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkört (vagy egy másik beépített szerepkört, amely olvasói hozzáférést is tartalmaz), ha az előfizetés az Azure-beli delegált erőforrás-kezeléshez lett előkészítve.

Az ügyfél bérlője:

1. Navigáljon a szolgáltatók [lapra](view-manage-service-providers.md).
2. Válassza ki a **szolgáltatói ajánlatokat**.
3. Győződjön meg arról, hogy az előfizetés (ok) a Resource Manager-sablonban megadott ajánlat nevével jelenik meg.

> [!NOTE]
> A telepítés befejezése után néhány percet is igénybe vehet, mielőtt a frissítések megjelennek a Azure Portalban.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

## <a name="remove-access-to-a-delegation"></a>Delegáláshoz való hozzáférés eltávolítása

Alapértelmezés szerint az ügyfél bérlője, aki rendelkezik a megfelelő engedélyekkel, eltávolíthatja a szolgáltató hozzáférését a delegált erőforrásokhoz a Azure Portal [szolgáltató lapján](view-manage-service-providers.md#add-or-remove-service-provider-offers) . Ha igen, a szolgáltató bérlője egyik felhasználója sem férhet hozzá a korábban delegált erőforrásokhoz.

Ha a [felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepkörével](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) rendelkező felhasználók bevezetését végzi az Azure-beli delegált erőforrás-kezeléshez, akkor ezek a felhasználók is el tudják távolítani a delegálást.

Az alábbi példa egy olyan hozzárendelést mutat be, amely megadja a **felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepkörét** , amely szerepelhet egy paraméter-fájlban:

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

Az ezzel az engedéllyel rendelkező felhasználók a következő módszerek egyikével törölhetik a delegálást.

### <a name="azure-portal"></a>Azure Portal

1. Navigáljon a [saját ügyfelek oldalra](view-manage-customers.md).
2. Válassza a **delegálások**lehetőséget.
3. Keresse meg az eltávolítani kívánt delegálást, majd válassza ki a sorban megjelenő Kuka ikont.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>Következő lépések

- További információ a [bérlők közötti felügyeleti élményekről](../concepts/cross-tenant-management-experience.md).
- [Megtekintheti és kezelheti az ügyfeleket](view-manage-customers.md) a Azure Portalban lévő **ügyfelekkel** .
