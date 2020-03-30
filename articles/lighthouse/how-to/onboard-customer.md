---
title: Ügyfél előkészítése az Azure által delegált erőforrás-kezeléshez
description: Ismerje meg, hogyan lehet egy ügyfél az Azure delegált erőforrás-kezelés, amely lehetővé teszi, hogy az erőforrások elérése és kezelése a saját bérlőn keresztül.
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: 6a5f4ce03f762b5903e8b3d6f10810819e02e422
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246874"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Ügyfél előkészítése az Azure által delegált erőforrás-kezeléshez

Ez a cikk bemutatja, hogyan, mint szolgáltató, a fedélzeten egy ügyfél az Azure delegált erőforrás-kezelés, amely lehetővé teszi a delegált erőforrások (előfizetések és/vagy erőforráscsoportok) eléréséhez és kezeléséhez a saját Azure Active Directory (Azure AD) bérlő. Míg itt a szolgáltatókra és az ügyfelekre hivatkozunk, a több bérlőt kezelő vállalatok ugyanazt a [folyamatot használhatják](../concepts/enterprise.md) a felügyeleti élmény ük konszolidálására.

Ezt a folyamatot megismételheti, ha több ügyfél erőforrásait kezeli. Ezt követően, amikor egy jogosult felhasználó bejelentkezik a bérlő, hogy a felhasználó jogosult az ügyfél bérleti hatókörök felügyeleti műveletek végrehajtása anélkül, hogy jelentkezzen be minden egyes ügyfél-bérlő.

Az ügyfélkapcsolatokra gyakorolt hatás nyomon követéséhez és az elismerés fogadásához társítsa Microsoft Partner Network (MPN) azonosítóját legalább egy olyan felhasználói fiókkal, amely hozzáfér az egyes fedélzeti előfizetésekhez. Vegye figyelembe, hogy ezt a társítást a szolgáltató bérlőjében kell végrehajtania. Az egyszerűség kedvéért azt javasoljuk, hogy hozzon létre egy egyszerű szolgáltatás fiókot a bérlőben, amely az MPN-azonosító, és biztosítja, hogy reader hozzáférést minden ügyfél a fedélzeten. További információ: [Partnerazonosító csatolása az Azure-fiókjaihoz.](../../billing/billing-partner-admin-link-started.md) 

> [!NOTE]
> Az ügyfelek akkor is beszállhatnak, ha az Azure Piactéren közzétett (nyilvános vagy privát) felügyelt szolgáltatási ajánlatot vásárolnak. További információ: [Felügyelt szolgáltatások ajánlatai közzététele az Azure Piactéren.](publish-managed-services-offers.md) Használhatja az itt leírt bevezetési folyamatot az Azure Marketplace-en közzétett ajánlattal együtt.

A bevezetési folyamat megköveteli, hogy a műveleteket a szolgáltató bérlőjéből és az ügyfél bérlőjéből is el kell végezni. Ezeket a lépéseket ebben a cikkben ismertetjük.

## <a name="gather-tenant-and-subscription-details"></a>Bérlői és előfizetési adatok összegyűjtése

Az ügyfél bérlőjének fedélzetére való ellátásához aktív Azure-előfizetéssel kell rendelkeznie. A következőket kell tudnia:

- A szolgáltató bérlőjének bérlői azonosítója (ahol az ügyfél erőforrásait kezeli)
- Az ügyfél bérlőjének bérlői azonosítója (amely a szolgáltató által kezelt erőforrásokkal fog rendelkezni)
- Az ügyfél bérlőjének minden egyes előfizetéséhez tartozó előfizetési azonosítók, amelyeket a szolgáltató kezel (vagy amely a szolgáltató által kezelt erőforráscsoportot(oka)t tartalmazza).

> [!NOTE]
> Még akkor is, ha csak egy előfizetésen belül csak egy vagy több erőforráscsoportot szeretne bevonni, a központi telepítést az előfizetés szintjén kell elvégezni, így szüksége lesz az előfizetés-azonosítóra.

Ha még nem rendelkezik ezekkel az azonosítóértékekkel, az alábbi módokon kérheti be őket. Győződjön meg róla, és használja ezeket a pontos értékeket a központi telepítésben.

### <a name="azure-portal"></a>Azure portál

A bérlői azonosító látható, ha az Azure Portal jobb felső részén a fiók neve fölé viszi az egérmutatót, vagy a **Címtár váltása**lehetőséget választja. A bérlői azonosító kiválasztásához és másolásához keresse meg az "Azure Active Directory" kifejezést a portálon belül, majd válassza a **Tulajdonságok** lehetőséget, és másolja a **Címtárazonosító** mezőben látható értéket. Ha meg szeretné találni az előfizetés azonosítóját az ügyfél bérlőjében, keresse meg az "Előfizetések" kifejezést, majd válassza ki a megfelelő előfizetés-azonosítót.

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
> Amikor egy előfizetést (vagy egy előfizetésen belül egy vagy több erőforráscsoportot) az itt leírt folyamat használatával alkalmaz, a **Microsoft.ManagedServices** erőforrás-szolgáltató regisztrálva lesz az adott előfizetéshez.

## <a name="define-roles-and-permissions"></a>Szerepkörök és engedélyek definiálása

Szolgáltatóként előfordulhat, hogy több feladatot szeretne végrehajtani egyetlen ügyfél számára, amelyek különböző hatókörökhöz különböző hozzáférést igényelnek. Annyi engedélyezést definiálhat, amennyi szükséges [a szerepköralapú hozzáférés-vezérlési (RBAC) beépített szerepkörök](../../role-based-access-control/built-in-roles.md) hozzárendeléséhez a bérlőfelhasználóihoz.

A felügyelet megkönnyítése érdekében azt javasoljuk, hogy az Azure AD felhasználói csoportok minden szerepkör, amely lehetővé teszi, hogy adjunk hozzá, vagy távolítsa el az egyes felhasználók a csoporthoz ahelyett, hogy engedélyeket közvetlenül az adott felhasználóhoz. Előfordulhat, hogy szerepköröket is hozzá szeretne rendelni egy egyszerű szolgáltatáshoz. Ügyeljen arra, hogy kövesse a minimális jogosultság elvét, hogy a felhasználók csak a feladat uk elvégzéséhez szükséges engedélyekkel rendelkezhessenek. A támogatott szerepkörökkel kapcsolatos javaslatokért és információkért [lásd: Bérlők, felhasználók és szerepkörök az Azure Világítótorony-forgatókönyvekben.](../concepts/tenants-users-roles.md)

> [!IMPORTANT]
> Az Azure AD-csoport engedélyeinek hozzáadásához a **csoporttípusnak** **biztonságnak,** nem pedig **Office 365-nek**kell lennie. Ez a beállítás a csoport létrehozásakor van bejelölve. További információ: [Hozzon létre egy alapszintű csoportot, és adjon hozzá tagokat az Azure Active Directory használatával.](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

Az engedélyek meghatározásához ismernie kell az egyes felhasználók, felhasználói csoportok vagy egyszerű szolgáltatás azonosítóértékeit abban a szolgáltatóbérlőben, amelyhez hozzáférést kíván adni. A szerepkör-definícióazonosítóra is szüksége lesz minden hozzárendelni kívánt beépített szerepkörhöz. Ha még nem rendelkezik velük, lekérheti őket az alábbi parancsok futtatásával a szolgáltató bérlőjéből.

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
az ad user show --id "<yourUPN>" --query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```
> [!TIP]
> Javasoljuk, hogy a [felügyelt szolgáltatások regisztrációs hozzárendelésének törlése szerepkört](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) az ügyfél bevezetéskor rendelje hozzá, hogy a bérlő felhasználói szükség esetén [később eltávolíthassák a delegáláshoz való hozzáférést.](#remove-access-to-a-delegation) Ha ez a szerepkör nincs hozzárendelve, a delegált erőforrásokat csak az ügyfél bérlőjében lévő felhasználó távolíthatja el.

## <a name="create-an-azure-resource-manager-template"></a>Azure Resource Manager-sablon létrehozása

Az ügyfél fedélzetére, létre kell hoznia egy [Azure Resource Manager](../../azure-resource-manager/index.yml) sablont az ajánlathoz a következő információkkal. Az **mspOfferName** és **az mspOfferDescription** értékek láthatók lesznek az ügyfél számára, amikor az Azure Portal [Szolgáltatói lapján](view-manage-service-providers.md) tekintik meg az ajánlat részleteit.

|Mező  |Meghatározás  |
|---------|---------|
|**mspOfferName**     |A definíciót leíró név. Ez az érték jelenik meg az ügyfél számára az ajánlat címeként.         |
|**mspOfferDescription**     |Az ajánlat rövid leírása (például "Contoso VM felügyeleti ajánlat").      |
|**managedByTenantId**     |A bérlőazonosítója.          |
|**Engedélyek**     |A **bérlők** től származó felhasználók/csoportok/SPN-ek rendszerazonosítóértékei, amelyek mindegyike **rendelkezik egy-egy lett- és egy-egy lett,** amely segít az ügyfélnek megérteni az engedélyezés célját, és egy beépített **roleDefinitionId** értékhez van rendelve a hozzáférés szintjének megadásához.      |

A bevezetési folyamathoz egy Azure Resource Manager-sablonra [(a mintatárban](https://github.com/Azure/Azure-Lighthouse-samples/)biztosított) és egy megfelelő paraméterfájlra van szükség, amelyet a konfigurációnak megfelelően módosít, és meghatározza az engedélyeket.

A választott sablon attól függ, hogy egy teljes előfizetést, egy erőforráscsoportot vagy több erőforráscsoportot vezet-e be egy előfizetésen belül. Egy sablont is biztosítunk, amely használható azoknak az ügyfeleknek, akik az Azure Marketplace-en közzétett felügyelt szolgáltatási ajánlatot vásárolták, ha így szeretné az előfizetéseiket.

|A fedélzetre ezt a  |Az Azure Resource Manager sablon használata  |És módosítsa ezt a paraméterfájlt |
|---------|---------|---------|
|Előfizetés   |[delegáltResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[dedemresourcemanagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Erőforráscsoport   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Több erőforráscsoport egy előfizetésen belül   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Előfizetés (az Azure Marketplace-en közzétett ajánlat használata esetén)   |[piactérDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Az itt leírt folyamat külön előfizetési szintű üzembe helyezést igényel minden egyes üzembe helyezett előfizetéshez, még akkor is, ha ugyanazon ügyfél-bérlőben van előfizetések bevezetése. Külön központi telepítések is szükségesek, ha több erőforráscsoportok at különböző előfizetések ugyanazon ügyfél-bérlőn belül. Azonban egy előfizetésen belül több erőforráscsoport bevezetése egyetlen előfizetésen belül is elvégezhető egy előfizetés-szintű telepítésben.
>
> Külön központi telepítések is szükség van több ajánlatok ugyanazon előfizetésre (vagy erőforráscsoportok egy előfizetésen belül) alkalmazva. Minden alkalmazott ajánlatnak más **mspOfferName-t kell használnia.**

A következő példa egy módosított **delegáltErőforrás-kezelő.parameters.json** fájlt mutat be, amely az előfizetés ekként használható. Az erőforráscsoport paraméterfájljai (amelyek az [rg-delegált erőforrás-felügyeleti](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) mappában találhatók) hasonlóak, de tartalmaznak egy **rgName** paramétert is a fedélzetre helyezendő erőforráscsoport(ok) azonosításához.

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

A fenti példában szereplő utolsó engedélyezés egy user access administrator szerepkörrel rendelkező **principalId azonosítót** ad hozzá (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). A szerepkör hozzárendelésekor meg kell egyenlennie a **delegáltRoleDefinitionIds** tulajdonsággal és egy vagy több beépített szerepkörrel. Az ebben az engedélyezésben létrehozott felhasználó hozzá rendelheti ezeket a beépített szerepköröket az ügyfél-bérlő [felügyelt identitásaihoz,](../../active-directory/managed-identities-azure-resources/overview.md) ami szükséges a [kiigazítható házirendek üzembe helyezéséhez.](deploy-policy-remediation.md) Erre a felhasználóra a Felhasználói hozzáférés rendszergazdaszerepköréhez általában társított egyéb engedélyek nem vonatkoznak.

## <a name="deploy-the-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok üzembe helyezése

Miután frissítette a paraméterfájlt, az ügyfél bérlőjének egyik felhasználójának üzembe kell helyeznie az Azure Resource Manager-sablont a bérlőn belül előfizetési szintű telepítésként. Külön üzembe helyezésszükséges minden olyan előfizetéshez, amelyet az Azure delegált erőforrás-kezelésére szeretne bevonni (vagy minden olyan előfizetéshez, amely a fedélzeten lenni kívánt erőforráscsoportokat tartalmaz).

Mivel ez egy előfizetési szintű központi telepítés, nem indítható az Azure Portalon. A központi telepítés történhet A PowerShell vagy az Azure CLI használatával, az alábbiak szerint.

> [!IMPORTANT]
> Ezt az előfizetési szintű telepítést az ügyfél bérlőjében lévő nem vendégfióknak kell elvégeznie, aki rendelkezik a [tulajdonos beépített szerepkörrel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) a beépített előfizetéshez (vagy amely a beépítés alatt álló erőforráscsoportokat tartalmazza). Az előfizetésdelegálható összes felhasználó megtekintéséhez az ügyfél bérlőjének egyik felhasználója kiválaszthatja az előfizetést az Azure Portalon, megnyithatja a **hozzáférés-vezérlést (IAM)** és [megtekintheti a Tulajdonos szerepkörrel rendelkező összes felhasználót.](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription)

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

## <a name="confirm-successful-onboarding"></a>Sikeres bevezetés megerősítése

Ha egy ügyfél-előfizetés sikeresen bevan szervezve az Azure delegált erőforrás-kezelésére, a szolgáltató bérlőjének felhasználói láthatják az előfizetést és annak erőforrásait (ha a fenti folyamat során hozzáférést kaptak hozzá, vagy egyénileg, vagy egy Azure AD-csoport tagjaként a megfelelő engedélyekkel). Ennek megerősítéséhez ellenőrizze, hogy az előfizetés az alábbi módokon jelenik-e meg.  

### <a name="azure-portal"></a>Azure portál

A szolgáltató bérlőjében:

1. Nyissa meg a [Saját ügyfelek lapot](view-manage-customers.md).
2. Válassza a **Vevők lehetőséget.**
3. Ellenőrizze, hogy az Erőforrás-kezelő sablonban megadott ajánlatnévvel rendelkező előfizetés(ek) láthatók-e.

> [!IMPORTANT]
> Annak érdekében, hogy a delegált előfizetés az [ügyfeleim,](view-manage-customers.md)a felhasználók a szolgáltató bérlője kell adni a [Reader](../../role-based-access-control/built-in-roles.md#reader) szerepkör (vagy egy másik beépített szerepkör, amely magában foglalja a Reader-hozzáférés), amikor az előfizetés volt beszervezve az Azure delegált erőforrás-kezelés.

Az ügyfél bérlőjében:

1. Keresse meg a [Szolgáltatók lapot](view-manage-service-providers.md).
2. Válassza **a Szolgáltatóajánlatok lehetőséget.**
3. Ellenőrizze, hogy az Erőforrás-kezelő sablonban megadott ajánlatnévvel rendelkező előfizetés(ek) láthatók-e.

> [!NOTE]
> A központi telepítés befejezése után is eltarthat néhány percig, mielőtt a frissítések megjelennek az Azure Portalon.

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

## <a name="remove-access-to-a-delegation"></a>Delegáláshoz való hozzáférés megszüntetése

Alapértelmezés szerint az ügyfél bérlője, akik rendelkeznek a megfelelő engedélyekkel eltávolíthatja a szolgáltató hozzáférését a delegált erőforrások az Azure Portal [Szolgáltató lapján.](view-manage-service-providers.md#add-or-remove-service-provider-offers) Ha így tesznek, a szolgáltató bérlőjének egyetlen felhasználója sem férhet hozzá a korábban delegált erőforrásokhoz.

Ha a [felügyelt szolgáltatások regisztrációs hozzárendelésének törlése szerepkörrel](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) rendelkező felhasználókat az Azure delegált erőforrás-kezelésére való bevezetéskor beszállt a felhasználókba, ezek a felhasználók is eltávolíthatják a delegálást.

Az alábbi példa egy olyan hozzárendelést mutat be, amely megadja a **kezelt szolgáltatások regisztrációs hozzárendelésének törlési szerepkörét,** amely szerepelhet egy paraméterfájlban:

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

Az ilyen engedéllyel rendelkező felhasználók az alábbi módokon távolíthatják el a delegálást.

### <a name="azure-portal"></a>Azure portál

1. Nyissa meg a [Saját ügyfelek lapot](view-manage-customers.md).
2. Válassza a **Delegálások lehetőséget**.
3. Keresse meg az eltávolítani kívánt delegálást, majd jelölje ki a kuka ikont, amely a sorában jelenik meg.

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

## <a name="next-steps"></a>További lépések

- További információ a [bérlők közötti felügyeleti élményekről.](../concepts/cross-tenant-management-experience.md)
- Az Azure Portalon az **Ügyfeleimmel** tekintheti meg és kezelheti az [ügyfeleket.](view-manage-customers.md)
