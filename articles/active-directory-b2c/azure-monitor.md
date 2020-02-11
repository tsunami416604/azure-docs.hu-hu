---
title: Azure AD B2C figyelése Azure Monitor
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan naplózhatja Azure AD B2C eseményeit Azure Monitorekkel a delegált erőforrás-kezelés használatával.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.author: marsma
ms.subservice: B2C
ms.date: 02/10/2020
ms.openlocfilehash: 6f7f0252a6377397ccaccdc44c9c8561da7c9d29
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121380"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Azure AD B2C figyelése Azure Monitor

A Azure Monitor használatával átirányíthatja Azure Active Directory B2C (Azure AD B2C) bejelentkezési és [naplózási](view-audit-logs.md) naplókat különböző figyelési megoldásokhoz. Megtarthatja a naplókat a hosszú távú használatra, vagy integrálhatja a külső felek biztonsági információit és esemény-felügyeleti (SIEM) eszközöket, hogy betekintést nyerjen a környezetbe.

A naplózási eseményeket a következő módon irányíthatja át:

* Egy Azure [Storage-fiók](../storage/blobs/storage-blobs-introduction.md).
* Egy Azure [Event hub](../event-hubs/event-hubs-about.md) (és integrálható a splunk és a szumó logikai példányokkal).
* Egy [log Analytics munkaterület](../azure-monitor/platform/resource-logs-collect-workspace.md) (az adatelemzéshez, az irányítópultok létrehozásához és a riasztáshoz adott eseményeken).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések végrehajtásához a Azure PowerShell modul használatával helyezzen üzembe egy Azure Resource Manager-sablont.

* [Azure PowerShell modul](https://docs.microsoft.com/powershell/azure/install-az-ps) 6.13.1 vagy újabb verziója

Használhatja a [Azure Cloud Shell](https://shell.azure.com)is, amely tartalmazza a Azure PowerShell modul legújabb verzióját.

## <a name="delegated-resource-management"></a>Delegált erőforrás-kezelés

A Azure AD B2C [Azure Active Directory monitorozást](../active-directory/reports-monitoring/overview-monitoring.md)használ. Az Azure AD B2C-bérlőn belüli Azure Active Directory *diagnosztikai beállításainak* engedélyezéséhez [delegált erőforrás-kezelést](../lighthouse/concepts/azure-delegated-resource-management.md)használ.

Az Azure-előfizetést (az **ügyfelet**) tartalmazó bérlőn belül a Azure ad B2C könyvtárban (a **szolgáltatóban**) engedélyezheti a Azure monitor példány konfigurálását. Az engedélyezés létrehozásához üzembe kell helyeznie egy [Azure Resource Manager](../azure-resource-manager/index.yml) sablont az előfizetést tartalmazó Azure ad-bérlőn. A következő szakasz végigvezeti a folyamaton.

## <a name="create-or-choose-resource-group"></a>Erőforráscsoport létrehozása vagy kiválasztása

Ez az az erőforráscsoport, amely a cél Azure Storage-fiókot, az Event hub-t vagy Log Analytics munkaterületet tartalmazza az adatok Azure Monitorból való fogadásához. Az erőforráscsoport nevét a Azure Resource Manager sablonjának telepítésekor kell megadnia.

[Hozzon létre egy erőforráscsoportot](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) , vagy válasszon ki egy meglévőt az Azure-előfizetését tartalmazó Azure Active Directory (Azure ad) bérlőn, *ne* pedig a Azure ad B2C bérlőt tartalmazó könyvtárat.

Ez a példa egy *Azure-ad-B2C-monitor* nevű erőforráscsoportot használ az *USA középső* régiójában.

## <a name="delegate-resource-management"></a>Erőforrás-kezelés delegálása

Ezután Gyűjtse össze a következő információkat:

A Azure AD B2C könyvtárának (más néven bérlői azonosító) **címtár-azonosítója** .

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/) felhasználóként a *felhasználói rendszergazda* szerepkörrel (vagy újabb).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. Válassza a **Azure Active Directory**lehetőséget, majd válassza a **Tulajdonságok**lehetőséget.
1. Jegyezze fel a **könyvtár azonosítóját**.

Annak a Azure AD B2C csoportnak vagy felhasználónak a **azonosítója** , amelyhez *közreműködői* engedélyt kíván adni az előfizetést tartalmazó könyvtárban korábban létrehozott erőforráscsoporthoz.

A felügyelet egyszerűbbé tételéhez ajánlott az Azure AD felhasználói *csoportok* használata az egyes szerepkörökhöz, ami lehetővé teszi az egyes felhasználók hozzáadását vagy eltávolítását, nem pedig közvetlenül az adott felhasználóhoz rendel hozzá engedélyeket. Ebben az útmutatóban egy felhasználót ad hozzá.

1. Ha a **Azure Active Directory** továbbra is ki van választva a Azure Portal, válassza a **felhasználók**lehetőséget, majd válasszon ki egy felhasználót.
1. Jegyezze fel a felhasználó **objektum-azonosítóját**.

### <a name="create-an-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon létrehozása

Az Azure AD-bérlő (az **ügyfél**) beléptetéséhez hozzon létre egy [Azure Resource Manager sablont](../lighthouse/how-to/onboard-customer.md) az ajánlatához a következő információkkal. A `mspOfferName` és a `mspOfferDescription` értékek láthatók, ha az ajánlat részleteit a Azure Portal [szolgáltatók lapján](../lighthouse/how-to/view-manage-service-providers.md) tekinti meg.

| Mező   | Meghatározás |
|---------|------------|
| `mspOfferName`                     | A definíciót leíró név. Például *Azure ad B2C felügyelt szolgáltatásokat*. Ez az érték jelenik meg az ügyfél számára az ajánlat címeként. |
| `mspOfferDescription`              | Az ajánlat rövid leírása. Például *engedélyezi a Azure AD B2C Azure monitorét*.|
| `rgName`                           | Az Azure AD-bérlőben korábban létrehozott erőforráscsoport neve. Például: *Azure-ad-B2C-monitor*. |
| `managedByTenantId`                | A Azure AD B2C bérlő (más néven bérlői azonosító) **címtár-azonosítója** . |
| `authorizations.value.principalId` | Az Azure-előfizetés erőforrásaihoz hozzáférő B2C-csoport vagy-felhasználó **objektumazonosító** . Ebben a bemutatóban a korábban feljegyzett felhasználó objektumazonosítót kell megadnia. |

Töltse le a Azure Resource Manager sablon és paraméter fájljait:

- [rgDelegatedResourceManagement. JSON](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement. Parameters. JSON](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

Ezután frissítse a paramétereket tartalmazó fájlt a korábban feljegyzett értékekkel. A következő JSON-kódrészlet példát mutat be Azure Resource Manager sablon paramétereinek fájljára. `authorizations.value.roleDefinitionId`esetén használja a *közreműködő szerepkör* [beépített szerepkörének](../role-based-access-control/built-in-roles.md) értékét `b24988ac-6180-42a0-ab88-20f7382dd24c`.

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

### <a name="deploy-the-azure-resource-manager-templates"></a>A Azure Resource Manager-sablonok üzembe helyezése

Miután frissítette a paramétereket tartalmazó fájlt, telepítse az Azure Resource Manager sablont az Azure-bérlőbe előfizetési szintű telepítésként. Mivel ez egy előfizetési szintű telepítés, nem indítható el a Azure Portalban. A Azure PowerShell modullal vagy az Azure CLI-vel is üzembe helyezhető. Az Azure PowerShell metódus alább látható.

Jelentkezzen be az előfizetést tartalmazó könyvtárba a [AzAccount](/powershell/azure/authenticate-azureps)használatával. A `-tenant` jelző használatával kényszerítheti a hitelesítést a megfelelő könyvtárba.

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

A [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) parancsmaggal listázhatja azokat az előfizetéseket, amelyekhez az aktuális fiók hozzáfér az Azure ad-bérlőben. Jegyezze fel a projektbe felvenni kívánt előfizetés AZONOSÍTÓját a Azure AD B2C bérlőbe.

```PowerShell
Get-AzSubscription
```

Ezután váltson arra az előfizetésre, amelyet szeretne projektbe venni a Azure AD B2C bérlőbe:

``` PowerShell
Select-AzSubscription <subscription ID>
```

Végül telepítse a korábban letöltött és frissített Azure Resource Manager sablon és paraméter fájljait. Ennek megfelelően cserélje le a `Location`, `TemplateFile`és `TemplateParameterFile` értékeket.

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

A sablon sikeres üzembe helyezése a következőhöz hasonló kimenetet eredményez (rövidített kimenet):

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

A sablon üzembe helyezése után néhány percet is igénybe vehet, amíg az erőforrás-leképezés befejeződik. Előfordulhat, hogy néhány percet várnia kell (általában legfeljebb öt), mielőtt továbblép a következő szakaszra az előfizetés kiválasztásához.

## <a name="select-your-subscription"></a>Válassza ki előfizetését.

Miután telepítette a sablont, és néhány percet várt az erőforrás-kivetítés befejezéséhez, társítsa az előfizetést a Azure AD B2C-címtárhoz a következő lépésekkel.

1. **Jelentkezzen ki** a Azure Portal, ha éppen be van jelentkezve. A következő lépés a hitelesítő adatok frissítése a portál munkamenetében.
1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) a Azure ad B2C rendszergazdai fiókjával.
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán.
1. Válassza ki az előfizetést tartalmazó könyvtárat.

    ![Könyvtár váltása](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. Ellenőrizze, hogy a megfelelő könyvtárat és előfizetést jelölte-e ki. Ebben a példában az összes könyvtárat és előfizetést kiválasztjuk.

    ![A címtár & előfizetés-szűrőben kiválasztott összes könyvtár](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>Diagnosztikai beállítások konfigurálása

A diagnosztikai beállítások határozzák meg, hogy az erőforráshoz tartozó naplókat és mérőszámokat kell-e elküldeni. A lehetséges célpontok:

- [Azure Storage-fiók](../azure-monitor/platform/resource-logs-collect-storage.md)
- [Event hub](../azure-monitor/platform/resource-logs-stream-event-hubs.md) -megoldások.
- [Log Analytics-munkaterület](../azure-monitor/platform/resource-logs-collect-workspace.md)

Ha még nem tette meg, hozzon létre egy példányt a kiválasztott rendeltetési helyhez a [Azure Resource Manager sablonban](#create-an-azure-resource-manager-template)megadott erőforráscsoporthoz.

### <a name="create-diagnostic-settings"></a>Diagnosztikai beállítások létrehozása

Készen áll a Azure Portal [diagnosztikai beállításainak létrehozására](../active-directory/reports-monitoring/overview-monitoring.md) .

Azure AD B2C tevékenység naplóinak figyelési beállításainak konfigurálása:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. **Azure Active Directory** kiválasztása
1. A **figyelés**területen válassza a **diagnosztikai beállítások**elemet.
1. Ha vannak meglévő beállítások az erőforráson, látni fogja a már konfigurált beállítások listáját. Válassza a **diagnosztikai beállítás hozzáadása** lehetőséget egy új beállítás hozzáadásához, vagy a beállítás **szerkesztéséhez** egy meglévőt. Az egyes beállítások nem lehetnek többek között a célhelyek közül.

    ![Diagnosztikai beállítások ablaktábla Azure Portal](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Adja meg a beállítás nevét, ha még nem rendelkezik ilyennel.
1. Jelölje be az egyes célhelyek jelölőnégyzetét a naplók elküldéséhez. Válassza a **Konfigurálás** lehetőséget a beállítások megadásához az alábbi táblázatban leírtak szerint.

    | Beállítás | Leírás |
    |:---|:---|
    | Archiválás egy Storage-fiókba | A Storage-fiók neve. |
    | Stream az Event hub-ba | Az a névtér, amelyben az Event hub létre lett hozva (ha ez az első adatfolyam-naplók), vagy adatfolyamként továbbítja a (ha már van olyan erőforrás, amely ezen a névtéren keresztül továbbítja a naplózási kategóriát).
    | Küldés a Log Analyticsnek | Munkaterület neve. |

1. Válassza a **AuditLogs** és a **SignInLogs**lehetőséget.
1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

A Azure Monitor diagnosztikai beállításainak hozzáadásával és konfigurálásával kapcsolatos további információkért lásd [: oktatóanyag: erőforrás-naplók összegyűjtése és elemzése az Azure-erőforrásokból](../azure-monitor/insights/monitor-azure-resource.md).

További információ az Azure AD-naplók esemény-központba való továbbításáról [: oktatóanyag: Stream Azure Active Directory-naplók az Azure Event hub](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)-ba.
