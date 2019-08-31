---
title: Ügyfél beléptetése az Azure-beli delegált erőforrás-kezelésbe – Azure Lighthouse
description: Ismerje meg, hogyan végezheti el az ügyfelek bevezetését az Azure-beli delegált erőforrás-kezelési szolgáltatásba, így az erőforrásaik a saját bérlőn keresztül érhetők el és kezelhetők.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 08/29/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: dabee74dc757a8ccdc4384662f5c9bc09a1e5fbe
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165034"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Ügyfél előkészítése az Azure által delegált erőforrás-kezeléshez

Ez a cikk azt ismerteti, hogy Ön, mint szolgáltató, hogyan helyezhet üzembe egy ügyfelet az Azure által delegált erőforrás-kezelésben, lehetővé téve a delegált erőforrások (előfizetések és/vagy erőforráscsoportok) elérését és kezelését a saját Azure Active Directory ( Azure AD-bérlő. Noha a szolgáltatók és az ügyfelekre is hivatkozunk, a több bérlőt kezelő vállalatok ugyanazt a folyamatot használhatják a kezelési élményük megszilárdítására.

Ezt a folyamatot megismételheti, ha több ügyfél erőforrásait kezeli. Ezután, amikor egy jogosult felhasználó bejelentkezik a bérlőbe, a felhasználó jogosult lehet az ügyfél-kihelyezés hatókörében a felügyeleti műveletek végrehajtására anélkül, hogy be kellene jelentkeznie minden egyes ügyfél-bérlőre.

A Microsoft Partner Network-(MPN-) azonosítót társíthatja a beérkező előfizetésekkel, hogy nyomon követhesse az ügyfelek és a fogadások elismerését. További információ: [partner-azonosító csatolása az Azure-fiókokhoz](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started). Vegye figyelembe, hogy ezt a társítást külön kell elvégeznie minden olyan ügyfél-bérlőnél, amelyben erőforrásokat kezel. 

> [!NOTE]
> Az ügyfelek automatikusan beállíthatók az Azure Marketplace-en közzétett felügyelt szolgáltatások (nyilvános vagy privát) vásárlása esetén. További információ: felügyelt [szolgáltatások ajánlatának közzététele az Azure Marketplace-](publish-managed-services-offers.md)en. Az itt ismertetett bevezetési folyamatot az Azure Marketplace-en közzétett ajánlattal is elvégezheti.

A bevezetési folyamathoz a szolgáltató bérlője és az ügyfél bérlője között végrehajtandó műveletek szükségesek. A fenti lépéseket a cikk ismerteti.

> [!IMPORTANT]
> Az Azure-beli delegált erőforrás-kezeléshez jelenleg nem lehet előfizetést (vagy erőforráscsoportot) előkészíteni, ha az előfizetés Azure Databricks használ. Hasonlóképpen, ha regisztrálva van egy előfizetés a **Microsoft. ManagedServices** erőforrás-szolgáltatóval való bevezetéshez, jelenleg nem fog tudni Databricks-munkaterületet létrehozni az adott előfizetéshez.

## <a name="gather-tenant-and-subscription-details"></a>Bérlői és előfizetési adatok összegyűjtése

Az ügyfél bérlője számára aktív Azure-előfizetéssel kell rendelkeznie. Ismernie kell a következőket:

- A szolgáltató bérlője bérlői azonosítója (ahol az ügyfél erőforrásait fogja kezelni)
- Az ügyfél bérlője bérlői azonosítója (amelynek erőforrásai a szolgáltató által felügyelt erőforrások lesznek)
- Az ügyfél bérlője által felügyelt minden egyes előfizetéshez tartozó előfizetési azonosítók (vagy a szolgáltató által felügyelt erőforráscsoport (oka) t tartalmazza)

Ha még nem rendelkezik ezzel az információval, a következő módokon kérheti le.

### <a name="azure-portal"></a>Azure Portal

A bérlő AZONOSÍTÓját a Azure Portal jobb felső sarkában lévő fiók neve fölé helyezve, vagy a **könyvtár váltása**lehetőség kiválasztásával lehet látni. A bérlői azonosító kiválasztásához és másolásához keressen a "Azure Active Directory" kifejezésre a portálon, majd válassza a **Tulajdonságok** lehetőséget, és másolja ki a **címtár-azonosító** mezőben megjelenő értéket. Az előfizetés AZONOSÍTÓjának megkereséséhez keressen rá az "előfizetések" kifejezésre, majd válassza ki a megfelelő előfizetés-azonosítót.

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

Szolgáltatóként több ajánlatot is használhat egyetlen ügyféllel, és eltérő hozzáférésre van szüksége a különböző hatókörökhöz.

A felügyelet egyszerűbbé tételéhez ajánlott az Azure AD felhasználói csoportok használata az egyes szerepkörökhöz, ami lehetővé teszi az egyes felhasználók hozzáadását vagy eltávolítását, nem pedig közvetlenül az adott felhasználóhoz rendel hozzá engedélyeket. Az is előfordulhat, hogy szerepköröket szeretne hozzárendelni egy egyszerű szolgáltatáshoz. Ügyeljen arra, hogy kövesse a legalacsonyabb jogosultsági szint elvét, hogy a felhasználók csak a feladataik elvégzéséhez szükséges engedélyekkel rendelkezzenek, ami segít csökkenteni a véletlen hibák esélyét. További információ: [ajánlott biztonsági eljárások](../concepts/recommended-security-practices.md).

> [!NOTE]
> A szerepkör-hozzárendeléseknek a szerepköralapú hozzáférés-vezérlés (RBAC) [beépített szerepköreit](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)kell használniuk. Az Azure-beli delegált erőforrás-kezelés jelenleg minden beépített szerepkört támogat, kivéve a tulajdonost és a [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) engedéllyel rendelkező beépített szerepköröket. A felhasználói hozzáférés rendszergazdai beépített szerepköre korlátozott használat esetén támogatott az alább leírtak szerint. Az egyéni szerepkörök és a [klasszikus előfizetés-rendszergazdai szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) szintén nem támogatottak.

Az engedélyek definiálásához ismernie kell az egyes felhasználók, felhasználói csoportok vagy egyszerű szolgáltatásnév azonosító értékeit, amelyekhez hozzáférést szeretne biztosítani. A hozzárendelni kívánt beépített szerepkörökhöz is szüksége lesz a szerepkör-definíciós AZONOSÍTÓra. Ha még nem rendelkezik velük, a következő módszerek egyikével kérheti le őket.

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

## <a name="create-an-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon létrehozása

Az ügyfél beléptetéséhez létre kell hoznia egy [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) sablont, amely a következőket tartalmazza:

|Mező  |Meghatározás  |
|---------|---------|
|**mspName**     |Szolgáltató neve         |
|**mspOfferDescription**     |Az ajánlat rövid leírása (például "contoso VM Management ajánlat")      |
|**managedByTenantId**     |A bérlő azonosítója         |
|**engedélyek**     |A bérlőből származó felhasználók/csoportok/SPN- **principalId** értékei, amelyek mindegyike **principalIdDisplayName** segíti az ügyfél számára az engedélyezés céljának megértését, és a beépített **roleDefinitionId** értékre van leképezve, hogy megadja a hozzáférési szint         |

Az ügyfél előfizetésének bevezetéséhez használja a [minták](https://github.com/Azure/Azure-Lighthouse-samples/)tárházában megadott megfelelő Azure Resource Manager sablont, valamint egy megfelelő paramétereket tartalmazó fájlt, amelyet a konfigurációnak megfelelően módosítania kell, és meg kell határoznia az engedélyeket. A különálló sablonokat attól függően kell megadnia, hogy egy teljes előfizetést, egy erőforráscsoportot vagy több erőforráscsoportot kíván-e előkészíteni egy előfizetésen belül. Egy olyan sablont is biztosítunk, amely az Azure Marketplace-en közzétett, felügyelt szolgáltatási ajánlatot megvásárló ügyfelek számára is felhasználható, ha így szeretne előfizetni.

|**A beléptetéshez**  |**Azure Resource Manager sablon használata**  |**A paraméter fájljának módosítása** |
|---------|---------|---------|
|Subscription   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resource group   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Több erőforráscsoport egy előfizetésen belül   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Előfizetés (az Azure Marketplace-en közzétett ajánlat használata esetén)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Az itt leírt folyamatnak külön üzembe kell helyeznie a bevezetéshez szükséges összes előfizetést.
> 
> Külön központi telepítések is szükségesek, ha több különböző előfizetésben lévő erőforráscsoportot készít elő. Egy adott előfizetésen belül több erőforráscsoport bevezetését azonban egyetlen központi telepítésben is elvégezheti.

Az alábbi példa egy módosított **resourceProjection. Parameters. JSON** fájlt mutat be, amely egy előfizetés bevezetéséhez használható. Az erőforráscsoport-paraméter fájljai (az [RG-delegált erőforrás-kezelő](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) mappában találhatók) hasonlóak, de tartalmaznak egy **rgName** paramétert is a bevezetéshez megadott erőforráscsoport (ok) azonosításához.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspName": {
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
A fenti példában szereplő utolsó engedély egy **principalId** hoz létre a felhasználói hozzáférés rendszergazdai szerepkörrel (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). A szerepkör kiosztásakor meg kell adnia a **delegatedRoleDefinitionIds** tulajdonságot, és egy vagy több beépített szerepkört. Az ezen engedélyben létrehozott felhasználó ezeket a beépített szerepköröket hozzárendelheti a felügyelt identitásokhoz. Vegye figyelembe, hogy a felhasználói hozzáférés-rendszergazdai szerepkörhöz tartozó egyéb engedélyek nem lesznek érvényesek erre a felhasználóra.

## <a name="deploy-the-azure-resource-manager-templates"></a>A Azure Resource Manager-sablonok üzembe helyezése

A paraméter fájljának frissítése után az ügyfélnek az erőforrás-kezelési sablont kell központilag telepítenie az ügyfél bérlője számára előfizetési szintű telepítésként. Külön üzembe helyezésre van szükség minden olyan előfizetés esetében, amelyet be szeretne készíteni az Azure-beli delegált erőforrás-kezelésbe (vagy minden olyan előfizetéshez, amely a bevezetéshez használni kívánt erőforráscsoportokat tartalmaz).

> [!IMPORTANT]
> Az üzembe helyezést egy nem vendég fióknak kell végrehajtania az ügyfél bérlője számára, amely az előfizetéshez tartozó [tulajdonos beépített szerepkörrel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) rendelkezik (vagy amely tartalmazza az előkészítés alatt álló erőforráscsoportokat).

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
az deployment create –-name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create –-name <deploymentName \
                     –-location <AzureRegion> \
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

## <a name="next-steps"></a>További lépések

- További információ a [bérlők közötti felügyeleti élményekről](../concepts/cross-tenant-management-experience.md).
- [Megtekintheti és kezelheti](view-manage-customers.md) az ügyfeleket a Azure Portalban lévő **ügyfelekkel** .
