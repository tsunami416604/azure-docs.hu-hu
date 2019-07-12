---
title: Készítse elő az Azure-bA egy ügyfél meghatalmazott resource management - Azure világítótoronyig
description: Ismerje meg, hogyan előkészítése az Azure-bA egy ügyfél delegált erőforrás-kezelés lehetővé teszi az elérése és kezelhető a saját bérlő erőforrásaikat.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 1885a6220f14de234710b6980b5d3b6a6172bb7e
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809861"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Készítse elő az ügyfél az Azure-bA meghatalmazott erőforrás-kezelés

Ez a cikk azt ismerteti, hogyan, egy szolgáltató segítségével készítheti elő lehetővé teszi a delegált erőforrásaik (az előfizetések és/vagy erőforráscsoportok) elérése és kezelhető a saját Azure Active Directory (Azure delegált erőforrás-kezelés, az ügyfél Az Azure AD-) bérlőhöz. Azt a szolgáltatók és az ügyfelek itt fog hivatkozni, miközben kezelése több bérlőnél vállalatok is ugyanazzal az eljárással egyesíthetők a saját felügyeleti kezelőfelület.

Ha erőforrások kezeli a több ügyfél számára, megismételheti ezt a folyamatot. Majd amikor egy jogosult felhasználó jelentkezik be a bérlő, hogy a felhasználó jogosult-ügyfél bérlős hatókörökön belül anélkül, hogy jelentkezzen be minden egyes ügyfél bérlői kellene műveletek végrehajtása.

Az előkészített előfizetések nyomon követésére a hatás az ügyfélesetekből társíthatja a Microsoft Partner Network (MPN) Azonosítóját. További információ: [Partnerazonosító csatolása az Azure-fiókokhoz](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started).

> [!NOTE]
> Ügyfelek, amikor azok vásárol egy felügyelt szolgáltatások ajánlat (nyilvános vagy privát) az Azure Marketplace-en közzétett automatikusan lehet előkészíteni. További információ: [közzététele felügyelt szolgáltatásokat kínál az Azure Marketplace-en](publish-managed-services-offers.md). A bevezetési folyamatban, az itt leírtak szerint az Azure Marketplace-en közzétett ajánlatot is használhatja.

A bevezetési folyamat szükséges műveletek végrehajtását mind a szolgáltató bérlőn belül, és az ügyfél-bérlőből. Ebben a cikkben ismertetett lépésekkel.

> [!IMPORTANT]
> Jelenleg, akkor nem lehet előkészíteni egy előfizetést (vagy erőforráscsoportot egy előfizetésen belül) az Azure delegált erőforrás-kezelés, ha az előfizetés az Azure Databricks használ. Hasonlóképpen ha az előfizetés regisztrálva van a bevezetése az **Microsoft.ManagedServices** erőforrás-szolgáltató, akkor létre tudja hozni egy Databricks-munkaterület előfizetés esetében jelenleg.

## <a name="gather-tenant-and-subscription-details"></a>Gyűjtse össze a bérlői és az előfizetés részletei

Készítse elő a felhasználó bérlőjéhez, azt kell rendelkeznie egy aktív Azure-előfizetéssel. Meg kell ismernie a következő:

- A bérlő Azonosítóját, a szolgáltató bérlő (ahol felügyel az ügyfél-erőforrások)
- A bérlő Azonosítóját, a vevő bérlő (amely a szolgáltató által felügyelt erőforrások kell)
- Az előfizetés azonosítókat egyes adott előfizetés az ügyfél-bérlőben, hogy a szolgáltató által kezelendő (vagy, amely tartalmazza az erőforrás (ok) ban a szolgáltató által felügyelt)

Ezek az adatok már nem rendelkezik, az alábbi módon kérheti.

### <a name="azure-portal"></a>Azure Portal

A bérlő Azonosítóját láthatja az egérmutatót a fiók neve az Azure Portal jobb felső oldalán, vagy a kiválasztásával **címtár váltása**. Jelölje ki, és másolja a bérlő Azonosítóját, keressen a "Azure Active Directory", a portálon, majd jelölje ki **tulajdonságok** , és másolja az értéket, látható az **címtár-azonosító** mező. Az előfizetés Azonosítójának megkereséséhez keressen az "Előfizetések", és válassza ki a megfelelő előfizetés-azonosítójára.

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


## <a name="ensure-the-customers-subscription-is-registered-for-onboarding"></a>Győződjön meg arról, az ügyfél-előfizetés regisztrálva van a bevezetéséhez

Az egyes előfizetésekhez bevezetése regisztrálja manuálisan kell engedélyezni a **Microsoft.ManagedServices** erőforrás-szolgáltató. Az ügyfél-előfizetés regisztrálhatja ismertetett lépéseket követve [Azure-erőforrás-szolgáltatókat és típusaikat](../../azure-resource-manager/resource-manager-supported-services.md).

Az ügyfél ellenőrizheti, hogy az előfizetés készen áll a bevezetésre a következő módszerek valamelyikével.

### <a name="azure-portal"></a>Azure Portal

1. Az Azure Portalon válassza ki az előfizetést.
1. Válassza ki **erőforrás-szolgáltatók**.
1. Ellenőrizze, hogy **Microsoft.ManagedServices** állapota **regisztrált**.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Set-AzContext -Subscription <subscriptionId>
Get-AzResourceProvider -ProviderNameSpace 'Microsoft.ManagedServices'
```

A kapott eredmények a következőhöz hasonló:

```output
ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationDefinitions}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationAssignments}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {operations}
Locations         : {}
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set –subscription <subscriptionId>
az provider show –namespace "Microsoft.ManagedServices" –-output table
```

A kapott eredmények a következőhöz hasonló:

```output
Namespace                  RegistrationState
-------------------------  -------------------
Microsoft.ManagedServices  Registered
```

## <a name="define-roles-and-permissions"></a>Szerepkörök és engedélyek megadása

Szolgáltatóként érdemes több ajánlattal használata egyetlen ügyfél számára különböző hatóköröket eltérő hozzáférést igényelnek.

A felügyelet megkönnyítése érdekében használatát javasoljuk az Azure AD felhasználói csoportokat az egyes szerepkörökhöz engedélyek hozzárendelése a közvetlenül az adott felhasználó helyett lehetővé teszi, hogy a csoport egyes felhasználók hozzáadásához és eltávolításához. Szerepkörök hozzárendelése egy egyszerű szolgáltatás is érdemes. Mindenképpen hajtsa végre a legalacsonyabb jogosultsági szint elve az, hogy a felhasználók csak a feladat végrehajtásához szükséges engedélyekkel való nem szándékos hibák esélyének csökkentése érdekében. További információ: [ajánlott biztonsági eljárások](../concepts/recommended-security-practices.md).

> [!NOTE]
> Szerepkör-hozzárendelések kell használnia a szerepköralapú hozzáférés-vezérlés (RBAC) [beépített szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). Összes beépített szerepkört az Azure delegált resource Managerrel, kivéve a tulajdonos és a beépített szerepköröket a jelenleg támogatott [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) engedéllyel. A felhasználói hozzáférés rendszergazdai szerepkört az alább ismertetett való korlátozott használata támogatott. Egyéni szerepkörök és [hagyományos előfizetés-Rendszergazda szerepkörhöz](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) még nem támogatott.

Engedélyek definiálásához, kell tudni, hogy minden felhasználó, felhasználói csoportot, vagy szolgáltatás, amelyhez hozzáférést szeretne azonosító értékeit. Minden hozzárendelni kívánt beépített szerepkör is szüksége a szerepkördefiníció-Azonosítójára. Ha azok még nem rendelkezik, a következő módszerek valamelyikével letöltheti őket.



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
az ad group list –-query "[?displayName == '<yourGroupName>'].objectId" –-output tsv

# To retrieve the objectId for an Azure AD user
az ad user show –-upn-or-object-id "<yourUPN>" –-query "objectId" –-output tsv

# To retrieve the objectId for an SPN
az ad sp list –-query "[?displayName == '<spDisplayName>'].objectId" –-output tsv

# To retrieve role definition IDs
az role definition list –-name "<roleName>" | grep name
```

## <a name="create-an-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon létrehozása

Készítse elő az ügyfelek lesz szüksége, hozzon létre egy [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) sablont, amely a következőket tartalmazza:

|Mező  |Meghatározás  |
|---------|---------|
|**mspName**     |Szolgáltató neve         |
|**mspOfferDescription**     |Az ajánlat (például "Contoso virtuális gép felügyeleti ajánlat") rövid leírása      |
|**managedByTenantId**     |A bérlő azonosítója         |
|**Engedélyek**     |A **principalId** értékei a felhasználók/csoportok/SPN-ek a bérlőről, amelyek mindegyike egy **principalIdDisplayName** segítségével az ügyfelek az engedélyezési rendeltetésének megismerése és a egy beépített csatlakoztatott**roleDefinitionId** értéket adja meg a hozzáférési szintet         |

A bevezetni egy ügyfél előfizetését, használja a megfelelő Azure Resource Manager-sablon az általunk biztosított az [tárház minták](https://github.com/Azure/Azure-Lighthouse-samples/), együtt a megfelelő paramétereket tartalmazó fájlt, amely meghatározásához és a konfigurációnak megfelelően módosítja a engedélyek. Különálló sablonokként állnak rendelkezésre attól függően, hogy Ön bevezetési, egy teljes előfizetés, erőforráscsoport vagy egy előfizetésen belül több erőforráscsoporthoz. Is biztosítunk egy sablont, amely az ügyfelek, akik vásároltak egy felügyelt ajánlat, amely az Azure Marketplace-en közzétett, ha szívesebben készítse elő a előfizetés(ek) ezzel a módszerrel is használható.

|**A bevezetni ezt**  |**Az Azure Resource Manager-sablonnal**  |**És ez a paraméter a fájl módosítása** |
|---------|---------|---------|
|Subscription   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resource group   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Egy előfizetésen belül több erőforráscsoporthoz   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Előfizetés (ha az Azure Marketplace-en közzétett ajánlat használatával)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Az itt leírtak szerint a folyamat egy külön deployment igényel minden egyes előfizetés típusától függenek.
> 
> Különálló üzembe helyezéseket is szükségesek, ha több erőforráscsoportokat belül különböző előfizetésekben bevezetése. Azonban bevezetése több erőforráscsoportok egyetlen előfizetésben jön létre hajtható végre egy központi telepítésben.

Az alábbi példa bemutatja egy módosított **resourceProjection.parameters.json** fájlt, amely lesz üzembe helyezni használt előfizetés. Csoport paramétere erőforrásfájlokat (található a [rg-delegált-erőforrás-kezelő](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) mappa) hasonlóak, de is tartalmazhat egy **rgName** paraméterrel meghatározhatja az adott erőforrás (ok) ban kell előkészítve.

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
A fenti példában az utolsó engedélyt ad egy **principalId** a felhasználói hozzáférés adminisztrátora szerepkörrel (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Ha ez a szerepkör hozzárendelése, meg kell adni a **delegatedRoleDefinitionIds** tulajdonság és a egy vagy több beépített szerepkörrel. A felhasználó, az engedélyezés létrehozott ezek a beépített szerepkörök hozzárendelése a felügyelt identitásokból képes lesz. Vegye figyelembe, hogy nem más, általában a felhasználói hozzáférés rendszergazdája szerepkörrel társított engedélyek vonatkoznak a felhasználó.

## <a name="deploy-the-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok üzembe helyezése

Miután frissítette az alkalmazásparaméter-fájlt, az ügyfél egy előfizetés-szintű központi telepítést, telepítenie kell az ügyfél bérlői a Resource Management-sablon. Egy külön deployment delegált Azure resource Managerrel (vagy az egyes előfizetésekhez, amely tartalmazza az erőforráscsoportok kívánt felvétele) üzembe helyezni kívánt minden egyes előfizetés esetén van szükség.

> [!IMPORTANT]
> A központi telepítés az ügyfél-bérlőjéhez, amely rendelkezik a nem a Vendég fiók használatával kell elvégezni a [beépített szerepkör tulajdonosa](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) az előfizetés típusától függenek (vagy amely tartalmazza az erőforrás-csoportok vannak típusától függenek).

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

## <a name="confirm-successful-onboarding"></a>Erősítse meg a sikeres bevezetése

Amikor egy ügyfél-előfizetés sikeresen lett előkészítve az Azure delegált erőforrás-kezelést, a szolgáltató bérlőjében felhasználók tudják, tekintse meg az előfizetés és erőforrásainak (ha kaptak hozzáférést a fenti folyamat révén önálló és a megfelelő engedélyekkel az Azure AD-csoport tagjaként). A megerősítés érdekében ellenőrizze, hogy ellenőrizze, hogy az előfizetés a következő módszerek valamelyikével megjelenik-e.  

### <a name="azure-portal"></a>Azure Portal

A szolgáltató bérlő:

1. Keresse meg a [az ügyfelek lapon](view-manage-customers.md).
2. Válassza ki **ügyfelek**.
3. Győződjön meg arról, hogy látja-e az egy vagy több, a Resource Manager-sablonban megadott ajánlat nevét.

Az ügyfél bérlő:

1. Keresse meg a [szolgáltatók szolgáltatásoldal](view-manage-service-providers.md).
2. Válassza ki **szolgáltató ajánlatok szolgáltatás**.
3. Győződjön meg arról, hogy látja-e az egy vagy több, a Resource Manager-sablonban megadott ajánlat nevét.

> [!NOTE]
> Igénybe vehet néhány percet, miután a telepítés befejeződése előtt a frissítéseket az Azure Portalon is megjelennek.

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

- Ismerje meg [több-bérlős felhasználói élmény](../concepts/cross-tenant-management-experience.md).
- [Megtekintheti és kezelheti az ügyfelek](view-manage-customers.md) a **ügyfeleim** az Azure Portalon.
