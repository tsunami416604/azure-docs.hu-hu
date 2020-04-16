---
title: Az Azure AD B2C figyelése az Azure Monitorsegítségével
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan naplózhatja az Azure AD B2C-eseményeket az Azure Monitor használatával delegált erőforrás-kezelés használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.author: mimart
ms.subservice: B2C
ms.date: 02/10/2020
ms.openlocfilehash: 99e04c95156e40eed8c2b9aa88a2bee6f39e90c9
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392881"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Az Azure AD B2C figyelése az Azure Monitorsegítségével

Az Azure Monitor használatával az Azure Active Directory B2C (Azure AD B2C) bejelentkezési és [naplózási](view-audit-logs.md) naplókat különböző figyelési megoldásokra irányítsa. A naplók at hosszú távú használatra, vagy integrálni harmadik fél biztonsági információk és eseménykezelő (SIEM) eszközök betekintést nyerhet a környezetbe.

A naplóeseményeket a következő kretálhatja:

* Egy [Azure-tárfiók.](../storage/blobs/storage-blobs-introduction.md)
* Egy [Azure-eseményközpont](../event-hubs/event-hubs-about.md) (és integrálja a Splunk és a Sumo Logic példányok).
* [A Log Analytics munkaterület](../azure-monitor/platform/resource-logs-collect-workspace.md) (adatok elemzéséhez, irányítópultok létrehozásához és adott eseményekriasztáshoz).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések végrehajtásához üzembe helyez egy Azure Resource Manager-sablont az Azure PowerShell-modul használatával.

* [Az Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) 6.13.1-es vagy újabb verziója

Használhatja az [Azure Cloud Shellt](https://shell.azure.com)is, amely az Azure PowerShell-modul legújabb verzióját tartalmazza.

## <a name="delegated-resource-management"></a>Delegált erőforrás-kezelés

Az Azure AD B2C az [Azure Active Directory figyelését](../active-directory/reports-monitoring/overview-monitoring.md)használja. Ha engedélyezni szeretné *a diagnosztikai beállításokat* az Azure Active Directoryban az Azure AD B2C bérlőn belül, [delegált erőforrás-kezelést kell használnia.](../lighthouse/concepts/azure-delegated-resource-management.md)

Ön felhatalmazza az Azure AD B2C címtárban (a **szolgáltatóban)** lévő felhasználót vagy csoportot az Azure Monitor-példány konfigurálásához az Azure-előfizetést (az **ügyfelet)** tartalmazó bérlőn belül. Az engedélyezés létrehozásához üzembe helyez egy [Azure Resource Manager-sablont](../azure-resource-manager/index.yml) az előfizetést tartalmazó Azure AD-bérlőben. A következő szakaszok végigvezetik a folyamaton.

## <a name="create-or-choose-resource-group"></a>Erőforráscsoport létrehozása vagy kiválasztása

Ez az az erőforráscsoport, amely a cél Azure storage-fiókot, eseményközpontot vagy Log Analytics-munkaterületet tartalmaz az Azure Monitortól történő adatok fogadásához. Az Azure Resource Manager-sablon telepítésekor adja meg az erőforráscsoport nevét.

[Hozzon létre egy erőforráscsoportot,](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) vagy válasszon egy meglévőt az Azure Active Directory (Azure AD) bérlőben, amely az Azure-előfizetést tartalmazza, *nem* pedig az Azure AD B2C-bérlőt tartalmazó könyvtárat.

Ez a példa egy *azure-ad-b2c-monitor* nevű erőforráscsoportot használ az *USA középső* régiójában.

## <a name="delegate-resource-management"></a>Erőforrás-kezelés delegálása

Ezután gyűjtse össze a következő információkat:

Az Azure AD B2C könyvtár (más néven a bérlői azonosító) **címtárazonosítója.**

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) a *felhasználó rendszergazdai* szerepkörrel (vagy újabb) felhasználóként.
1. Válassza ki a **Könyvtár + Előfizetés** ikont a portál eszköztárán, majd válassza ki az Azure AD B2C-bérlőt tartalmazó könyvtárat.
1. Válassza az **Azure Active Directory**lehetőséget, válassza a **Tulajdonságok**lehetőséget.
1. Rögzítse a **címtárazonosítót**.

Az Azure AD B2C-csoport vagy a felhasználó **objektumazonosítóját** szeretné megadni *a közreműködői* engedélyt az előfizetést tartalmazó címtárban korábban létrehozott erőforráscsoportnak.

A felügyelet megkönnyítése érdekében azt javasoljuk, hogy az Azure AD felhasználói *csoportok* minden szerepkör, amely lehetővé teszi, hogy adjunk hozzá, vagy távolítsa el az egyes felhasználók a csoporthoz ahelyett, hogy engedélyeket közvetlenül az adott felhasználóhoz. Ebben a forgatókönyvben hozzáad egy felhasználót.

1. Ha az **Azure Active Directory** továbbra is ki van jelölve az Azure Portalon, válassza a **Felhasználók**lehetőséget, majd válasszon ki egy felhasználót.
1. Rögzítse a felhasználó **objektumazonosítóját**.

### <a name="create-an-azure-resource-manager-template"></a>Azure Resource Manager-sablon létrehozása

Az Azure AD-bérlő (az **ügyfél)** fedélzetre vételéhez hozzon létre egy [Azure Resource Manager-sablont](../lighthouse/how-to/onboard-customer.md) az ajánlathoz a következő információkkal. A `mspOfferName` `mspOfferDescription` és az értékek akkor láthatók, ha az Ajánlat részleteit az Azure Portal [Szolgáltatói lapján](../lighthouse/how-to/view-manage-service-providers.md) tekinti meg.

| Mező   | Meghatározás |
|---------|------------|
| `mspOfferName`                     | A definíciót leíró név. Például az *Azure AD B2C felügyelt szolgáltatások*. Ez az érték jelenik meg az ügyfél számára az ajánlat címeként. |
| `mspOfferDescription`              | Az ajánlat rövid leírása. Például *engedélyezi az Azure Monitort az Azure AD B2C-ben.*|
| `rgName`                           | Az Azure AD-bérlőben korábban létrehozott erőforráscsoport neve. Például *az azure-ad-b2c-monitor*. |
| `managedByTenantId`                | Az Azure AD B2C-bérlő (más néven a bérlőazonosító) **címtárazonosítója.** |
| `authorizations.value.principalId` | A B2C-csoport vagy -felhasználó **objektumazonosítója,** amely hozzáférést biztosít az Azure-előfizetés erőforrásaihoz. Ebben a forgatókönyvben adja meg a felhasználó korábban rögzített objektumazonosítóját. |

Töltse le az Azure Resource Manager sablont és paraméterfájlokat:

- [rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

Ezután frissítse a paraméterfájlt a korábban rögzített értékekkel. A következő JSON-kódrészlet egy Azure Resource Manager-sablon paraméterfájlpéldáját mutatja be. A `authorizations.value.roleDefinitionId`esetén használja a *közreműködői szerepkör* `b24988ac-6180-42a0-ab88-20f7382dd24c` [beépített szerepkörértékét](../role-based-access-control/built-in-roles.md) .

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Azure AD B2C Managed Services"
        },
        "mspOfferDescription": {
            "value": "Enables Azure Monitor in Azure AD B2C"
        },
        "rgName": {
            "value": "azure-ad-b2c-monitor"
        },
        "managedByTenantId": {
            "value": "<Replace with DIRECTORY ID of Azure AD B2C tenant (tenant ID)>"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "<Replace with user's OBJECT ID>",
                    "principalIdDisplayName": "Azure AD B2C tenant administrator",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                }
            ]
        }
    }
}
```

### <a name="deploy-the-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok üzembe helyezése

Miután frissítette a paraméterfájlt, üzembe helyezheti az Azure Resource Manager sablont az Azure-bérlőbe előfizetési szintű telepítésként. Mivel ez egy előfizetési szintű központi telepítés, nem indítható az Azure Portalon. Az Azure PowerShell-modul vagy az Azure CLI használatával üzembe helyezheti. Az Azure PowerShell-módszer alább látható.

Jelentkezzen be az előfizetést tartalmazó könyvtárba a [Connect-AzAccount](/powershell/azure/authenticate-azureps)segítségével. A `-tenant` jelző vel kényszerítheti a hitelesítést a megfelelő könyvtárra.

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

A [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) parancsmag használatával sorolja fel azokat az előfizetéseket, amelyeket az aktuális fiók az Azure AD-bérlő alatt érhet el. Rögzítse az Azure AD B2C-bérlőbe kivetíteni kívánt előfizetés azonosítóját.

```PowerShell
Get-AzSubscription
```

Ezután váltson át az Azure AD B2C-bérlőbe kivetíteni kívánt előfizetésre:

``` PowerShell
Select-AzSubscription <subscription ID>
```

Végül telepítse az Azure Resource Manager-sablont és a korábban letöltött és frissített paraméterfájlokat. Ennek `Location`megfelelően cserélje ki a és `TemplateFile`az `TemplateParameterFile` értékeket.

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

A sablon sikeres telepítése a következőhöz hasonló kimenetet eredményez (a kimenet rövidségből csonkolva):

```Console
PS /usr/csuser/clouddrive> New-AzDeployment -Name "AzureADB2C" `
>>                  -Location "centralus" `
>>                  -TemplateFile "rgDelegatedResourceManagement.json" `
>>                  -TemplateParameterFile "rgDelegatedResourceManagement.parameters.json" `
>>                  -Verbose
WARNING: Breaking changes in the cmdlet 'New-AzDeployment' :
WARNING:  - The cmdlet 'New-AzSubscriptionDeployment' is replacing this cmdlet.


WARNING: NOTE : Go to https://aka.ms/azps-changewarnings for steps to suppress this breaking change warning, and other information on breaking changes in Azure PowerShell.
VERBOSE: 7:25:14 PM - Template is valid.
VERBOSE: 7:25:15 PM - Create template deployment 'AzureADB2C'
VERBOSE: 7:25:15 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:42 PM - Resource Microsoft.ManagedServices/registrationDefinitions '44444444-4444-4444-4444-444444444444' provisioning status is succeeded
VERBOSE: 7:25:48 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:53 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is running
VERBOSE: 7:25:53 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:59 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is running
VERBOSE: 7:26:17 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:23 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is succeeded
VERBOSE: 7:26:23 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:29 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is succeeded

DeploymentName          : AzureADB2C
Location                : centralus
ProvisioningState       : Succeeded
Timestamp               : 1/31/20 7:26:24 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                   Type                       Value
                          =====================  =========================  ==========
                          mspOfferName           String                     Azure AD B2C Managed Services
                          mspOfferDescription    String                     Enables Azure Monitor in Azure AD B2C
...
```

A sablon üzembe helyezése után eltarthat néhány percig az erőforrás-kivetítés befejezése. Előfordulhat, hogy várnia kell néhány percet (általában legfeljebb öt percet), mielőtt továbblépne a következő szakaszra az előfizetés kiválasztásához.

## <a name="select-your-subscription"></a>Válassza ki előfizetését.

Miután üzembe helyezte a sablont, és várt néhány percet az erőforrás-vetület befejezésére, társítsa az előfizetést az Azure AD B2C könyvtárhoz a következő lépésekkel.

1. **Jelentkezzen ki** az Azure Portalon, ha jelenleg be van jelentkezve. Ez és a következő lépés a hitelesítő adatok frissítése a portálmunkamenetben történik.
1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) az Azure AD B2C felügyeleti fiókjával.
1. A portál eszköztárán válassza a **Könyvtár + Előfizetés** ikont.
1. Válassza ki az előfizetést tartalmazó könyvtárat.

    ![Címtár váltása](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. Ellenőrizze, hogy a megfelelő könyvtárat és előfizetést választotta-e. Ebben a példában az összes könyvtár és előfizetés van kiválasztva.

    ![A Címtár & Előfizetés szűrőben kijelölt összes könyvtár](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>Diagnosztikai beállítások konfigurálása

A diagnosztikai beállítások határozzák meg, hogy egy erőforrás naplóit és metrikáit hol kell elküldeni. A lehetséges úti célok a következők:

- [Azure tárfiók](../azure-monitor/platform/resource-logs-collect-storage.md)
- [Eseményközpontok megoldásai.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)
- [Log Analytics-munkaterület](../azure-monitor/platform/resource-logs-collect-workspace.md)

Ha még nem tette meg, hozzon létre egy példányt a kiválasztott céltípusból az [Azure Resource Manager sablonban](#create-an-azure-resource-manager-template)megadott erőforráscsoportban.

### <a name="create-diagnostic-settings"></a>Diagnosztikai beállítások létrehozása

Készen áll a [diagnosztikai beállítások létrehozása](../active-directory/reports-monitoring/overview-monitoring.md) az Azure Portalon.

Az Azure AD B2C tevékenységnaplók figyelési beállításainak konfigurálása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Válassza ki a **Könyvtár + Előfizetés** ikont a portál eszköztárán, majd válassza ki az Azure AD B2C-bérlőt tartalmazó könyvtárat.
1. **Az Azure Active Directory** kiválasztása
1. A **Monitorozás** területen kattintson a **Diagnosztikai beállítások** elemre.
1. Ha az erőforráson vannak beállítások, megjelenik a már konfigurált beállítások listája. Új beállítás hozzáadásához válassza **a Diagnosztikai beállítás hozzáadása** lehetőséget, vagy a **Szerkesztés** beállítást egy meglévő szerkesztéséhez. Minden beállítás hoz egy-egy a céltípusok közül..

    ![Diagnosztikai beállítások ablaktábla az Azure Portalon](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Adjon nevet a beállításnak, ha még nem rendelkezik ilyensel.
1. A naplók elküldéséhez jelölje be az egyes célhelyek jelölőnégyzetét. A **Konfigurálás gombra** a következő táblázatban ismertetett beállítások megadásához válassza a beállításokat.

    | Beállítás | Leírás |
    |:---|:---|
    | Archiválás tárfiókba | A tárfiók neve. |
    | Streamelés eseményközpontba | Az a névtér, ahol az eseményközpontot létrehozták (ha ez az első alkalommal streamelési naplók) vagy streamelt (ha már vannak olyan erőforrások, amelyek streamelés, hogy a napló kategóriában erre a névtérbe).
    | Küldés a Log Analyticsnek | Munkaterület neve. |

1. Válassza **az AuditLogs** and **SignInLogs lehetőséget.**
1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

A diagnosztikai beállítások Azure Monitorban való hozzáadásáról és konfigurálásáról az [Oktatóanyag: Erőforrásnaplók gyűjtése és elemzése Azure-erőforrásból](../azure-monitor/insights/monitor-azure-resource.md)című témakörben olvashat bővebben.

Az Azure AD-naplók eseményközpontba való streameléséről az [Oktatóanyag: Stream Azure Active Directory-naplók egy Azure-eseményközpontba](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)című témakörben talál további információt.
