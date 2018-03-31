---
title: Programozott módon szabályzatok létrehozása és megtekintése megfelelőségi adatait az Azure-házirend |} Microsoft Docs
description: Ez a cikk útmutatást nyújt a programozott módon létrehozása és házirendek kezelése az Azure-házirend.
services: azure-policy
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/28/2018
ms.topic: article
ms.service: azure-policy
manager: carmonm
ms.custom: ''
ms.openlocfilehash: 1809f0b7ef386bb9eeaa55982178e4cd5e1dd2e2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="programmatically-create-policies-and-view-compliance-data"></a>Programozott módon szabályzatok létrehozása és a megfelelőségi adatok megtekintése

Ez a cikk útmutatást nyújt a programozott módon létrehozása és házirendek kezelése. Azt is bemutatja, hogyan erőforrás megfelelőségi állapotok megtekintéséhez, és a házirendeket. Házirend-definíciók kényszerítése a különböző szabályok és a műveletek az erőforrások szerint. Érvényesítési gondoskodik arról, hogy erőforrások megfeleljenek a vállalati szabványoknak és a szolgáltatásszint-szerződések maradnak.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy a következő előfeltételek teljesülését:

1. Ha még nem tette meg, telepítse a [ARMClient](https://github.com/projectkudu/ARMClient). Olyan eszköz, amely HTTP-kérelmeket küld az Azure Resource Manager-alapú API-k.
2. Frissítse az AzureRM PowerShell-modult a legújabb verzióra. A legfrissebb verziójával kapcsolatos további információkért tekintse meg az Azure PowerShell https://github.com/Azure/azure-powershell/releases.
3. Az Azure PowerShell használatával győződjön meg arról, hogy működik-e az előfizetés az erőforrás-szolgáltató a házirend Insights erőforrás-szolgáltató regisztrálása. Egy erőforrás-szolgáltató regisztrálásához rendelkeznie kell az erőforrás-szolgáltató regisztrálási műveletének elvégzésére vonatkozó engedéllyel. Ezt a műveletet a Közreműködői és Tulajdonosi szerepkörök magukba foglalják. Az erőforrás-szolgáltató regisztrálásához futtassa az alábbi parancsot:

    ```
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.PolicyInsights
    ```

    További információ a regisztráció és erőforrás-szolgáltatók megtekintése: [erőforrás-szolgáltatók és típusok](../azure-resource-manager/resource-manager-supported-services.md).

4. Ha még nem tette meg, telepítse az Azure parancssori felület. A legújabb verzió kaphat [Azure CLI 2.0 telepítése a Windows](/azure/install-azure-cli-windows?view=azure-cli-latest).

## <a name="create-and-assign-a-policy-definition"></a>Hozzon létre, és rendelje hozzá a házirend-definíció

Az első lépés jobb látható-e az erőforrások létrehozására és hozzárendelésére házirendeket az erőforrások keresztül. A következő lépésben megtudhatja, hogyan programozott módon létrehozásához, és rendelje hozzá a házirend. A példa házirend eseményeket, amelyek PowerShell, az Azure CLI és a HTTP-kérelmek használatával az összes nyilvános hálózatokon storage-fiókok.

Az alábbi parancsokat a Standard csomagot házirend-definíciók létrehozása. A Standard csomagot segít a skála felügyeleti, a megfelelés kiértékelésének és javítási érhető el. Tarifacsomagok kapcsolatos további információkért lásd: [Azure házirend árképzési](https://azure.microsoft.com/pricing/details/azure-policy).

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Hozzon létre, és rendelje hozzá a házirend-definíció a PowerShell használatával

1. A következő JSON kódrészletet használja AuditStorageAccounts.json nevű JSON-fájl létrehozásához.

    ```
    {
    "if": {
      "allOf": [
        {
          "field": "type",
          "equals": "Microsoft.Storage/storageAccounts"
        },
        {
          "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
          "equals": "Allow"
        }
      ]
    },
    "then": {
      "effect": "audit"
    }
  }

    ```

    Szerzői házirend-definíció kapcsolatos további információkért lásd: [Azure házirend szerkezete](policy-definition.md).

2. A következő parancsot a AuditStorageAccounts.json fájllal házirend-definíció létrehozása.

    ```
    PS C:\>New-AzureRmPolicyDefinition -Name "AuditStorageAccounts" -DisplayName "Audit Storage Accounts Open to Public Networks" -Policy C:\AuditStorageAccounts.json
    ```

    A parancs létrehoz egy házirend-definíció nevű _naplózási tároló fiókok nyissa meg a nyilvános hálózatokhoz_. Használhatja más paraméterekkel kapcsolatos további információkért lásd: [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition?view=azurermps-4.4.1).

3. A házirend-definíció létrehozása után létrehozhat egy házirend-hozzárendelést a következő parancsok futtatásával:

    ```
$rg = Get-AzureRmResourceGroup -Name "ContosoRG"
```

    ```
$Policy = Get-AzureRmPolicyDefinition -Name "AuditStorageAccounts"
    ```

    ```
New-AzureRmPolicyAssignment -Name "AuditStorageAccounts" -PolicyDefinition $Policy -Scope $rg.ResourceId –Sku @{Name='A1';Tier='Standard'}
    ```

    Cserélje le _ContosoRG_ a megfelelő erőforráscsoport nevével.

Erőforrás-házirendek az Azure Resource Manager PowerShell-modullal kezelésével kapcsolatos további információkért lásd: [AzureRM.Resources](/powershell/module/azurerm.resources/?view=azurermps-4.4.1#policies).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Hozzon létre, és rendelje hozzá a házirend-definíció ARMClient használatával

A következő eljárással hozhat létre a házirend-definíció.

1. Másolja a következő JSON-részlet JSON-fájl létrehozásához. A fájl a következő lépésben értesítjük telefonon.

    ```
    {
    "properties": {
        "displayName": "Audit Storage Accounts Open to Public Networks",
        "policyType": "Custom",
        "mode": "Indexed",
        "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
        "parameters": {},
        "policyRule": {
              "if": {
                "allOf": [
                  {
                    "field": "type",
                    "equals": "Microsoft.Storage/storageAccounts"
                  },
                  {
                    "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                    "equals": "Allow"
                  }
                ]
              },
              "then": {
                "effect": "audit"
              }
            }
    }
}
```

2. Hozza létre a házirend-definíció használatával a következő hívást:

    ```
    armclient PUT "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01 @<path to policy definition JSON file>"
    ```

    Cserélje le a preceding_ &lt;subscriptionId&gt; , azonosító: a kívánt előfizetést.

A lekérdezés szerkezete kapcsolatos további információkért lásd: [házirend-definíciók – létrehozás vagy frissítés](/rest/api/resources/policydefinitions/createorupdate).


A következő eljárással hozzon létre egy házirend-hozzárendelést, és rendelje hozzá a házirend-definíció az erőforráscsoport szintjén.

1. Másolja a következő JSON-részlet JSON házirend-hozzárendelés fájl létrehozásához. Cserélje le a példaadatok &lt; &gt; szimbólumok saját értékekkel.

    ```
    {
  "properties": {
"description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
"displayName": "Audit Storage Accounts Open to Public Networks Assignment",
"parameters": {},
"policyDefinitionId":"/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
"scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
},
"sku": {
    "name": "A1",
    "tier": "Standard"
    }
}
    ```

2. A következő hívást használ a házirend-hozzárendelés létrehozása:

    ```
    armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
    ```

    Cserélje le a példaadatok &lt; &gt; szimbólumok saját értékekkel.

 HTTP-hívás a REST API-val kapcsolatos további információkért lásd: [Azure REST API-erőforrások](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Hozzon létre, és rendelje hozzá a házirend-definíció Azure parancssori felülettel

Az alábbi eljárással házirend-definíció létrehozása:

1. Másolja a következő JSON-részlet JSON házirend-hozzárendelés fájl létrehozásához.

    ```
    {
                  "if": {
                    "allOf": [
                      {
                        "field": "type",
                        "equals": "Microsoft.Storage/storageAccounts"
                      },
                      {
                        "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                        "equals": "Allow"
                      }
                    ]
                  },
                  "then": {
                    "effect": "audit"
                  }
    }
    ```

2. A következő parancsot a házirend-definíció létrehozása:

    ```
az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
    ```

A következő paranccsal házirend-hozzárendelés létrehozása. Cserélje le a példaadatok &lt; &gt; szimbólumok saját értékekkel.

```
az policy assignment create --name '<Audit Storage Accounts Open to Public Networks in Contoso RG' --scope '<scope>' --policy '<policy definition ID>' --sku 'standard'
```

A házirend-definíció azonosítója kaphat a PowerShell használatával a következő parancsot:

```
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

A házirend-definíció azonosítója a házirend-definíció létrehozott az alábbihoz kell hasonlítania:

```
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Hogyan kezelheti az Azure parancssori felülettel erőforrás-házirendekkel kapcsolatos további információkért lásd: [Azure CLI erőforrás-házirendek](/cli/azure/policy?view=azure-cli-latest).

## <a name="identify-non-compliant-resources"></a>Nem megfelelő erőforrások azonosítása

A hozzárendelés erőforrás állapota nem megfelelő, ha azt nem követi a házirend vagy kezdeményezésére szabályok. Az alábbi táblázat a különböző házirendműveletek a feltétel kiértékelése az eredményül kapott megfelelőségi állapotát:

| **Erőforrás állapota** | **Művelet** | **Házirend kiértékelése** | **Megfelelőségi állapot** |
| --- | --- | --- | --- |
| Létezik | Megtagadási, naplózni, hozzáfűző\*, DeployIfNotExist\*, AuditIfNotExist\* | Igaz | Nem megfelelő |
| Létezik | Megtagadási, naplózni, hozzáfűző\*, DeployIfNotExist\*, AuditIfNotExist\* | Hamis | Megfelelő |
| Új | Naplózási, AuditIfNotExist\* | Igaz | Nem megfelelő |
| Új | Naplózási, AuditIfNotExist\* | Hamis | Megfelelő |

\* A Hozzáfűzés DeployIfNotExist és AuditIfNotExist műveleteket igényelnek az IF utasítást, hogy lehet igaz. A műveletek is szükség lehet a fenntartása feltétel hamis nem kompatibilis. Amikor igaz értékű, a következő feltételt: Ha a kapcsolódó erőforrások meglétét feltételének értékelése váltja ki.

Jobb megértése, hogyan vannak megjelölve az erőforrások nem megfelelő, most használja az előbb létrehozott házirend hozzárendelés példa.

Tegyük fel, hogy rendelkezik egy erőforráscsoport – ContsoRG, néhány tárfiókok (pirossal kiemelt), nyilvános hálózatokhoz elérhetővé tett tárolókra.

![Storage-fiókok számára nyilvános hálózatokon](./media/policy-insights/resource-group01.png)

Ebben a példában kell lennie a biztonsági kockázatok óvatos. Most, hogy a házirend-hozzárendelés létrehozott, a rendszer kiértékeli az összes tárfiók ContosoRG erőforráscsoportban. Azt a három nem megfelelő tárfiókok esetén ebből következően módosítása az állapotot, és naplózás **nem megfelelő.**

![Nem megfelelő tárfiókok naplózása](./media/policy-insights/resource-group03.png)

Az alábbi eljárás segítségével azonosíthatók, amelyek nem felelnek meg a házirend-hozzárendelést az erőforrások erőforráscsoportban. A példában az erőforrások tárfiókok ContosoRG az erőforráscsoportban.

1. Töltse le a házirend-hozzárendelés azonosítója a következő parancsok futtatásával:

    ```
    $policyAssignment = Get-AzureRmPolicyAssignment | where {$_.properties.displayName -eq "Audit Storage Accounts with Open Public Networks"}
    ```

    ```
    $policyAssignment.PolicyAssignmentId
    ```

    A házirend-hozzárendelés azonosítója első kapcsolatos további információkért lásd: [Get-AzureRMPolicyAssignment](https://docs.microsoft.com/en-us/powershell/module/azurerm.resources/Get-AzureRmPolicyAssignment?view=azurermps-4.4.1).

2. A következő parancsot az erőforrás-azonosítók átmásolja a JSON-fájl nem kompatibilis erőforrás van:

    ```
    armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
    ```

3. Az eredményeket az alábbihoz kell hasonlítania:

  ```
      {
  "@odata.context":"https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
  "@odata.count": 3,
  "value": [
  {
      "@odata.id": null,
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount1Id>"
      },
      {
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount2Id>"
             },
  {
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount3ID>"
             }
  ]
  }
  ```

Az eredmények egyenértékűek mi általában mutatunk be tartozó **nem megfelelő erőforrások** a a [az Azure portál nézet](assign-policy-definition.md#identify-non-compliant-resources).

Nem kompatibilis erőforrások jelenleg csak az Azure portál használata azonosított, és a HTTP-kérelmekre. Házirend állapotok lekérdezésével kapcsolatban további információkért lásd: a [házirend állapota](/rest/api/policy-insights/policystates) API áttekintésével foglalkozó cikkben.

## <a name="view-policy-events"></a>Házirend-események megtekintése

Amikor egy erőforrást jön létre vagy frissül, a házirend kiértékelésének eredménye jön létre. Eredmények nevezzük _házirendeseményeket_. Futtassa a következő lekérdezést a házirend-hozzárendelés társított összes házirend-események megtekintése.

```
armclient POST "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2017-12-12-preview"
```

Az eredmények a következő példához hasonlók:

```
{
  "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
  "@odata.count": 1,
  "value": [
    {
      "@odata.id": null,
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
      "NumAuditEvents": 3
    }
  ]
}

```

Házirend állapotok, például csak megtekintheti a házirend-események a HTTP-kérelmekre. Házirend-események lekérdezésével kapcsolatban további információkért lásd: a [házirend-események](/rest/api/policy-insights/policyevents) áttekintésével foglalkozó cikkben.

## <a name="change-a-policy-assignments-pricing-tier"></a>A tarifacsomag módosítása házirend-hozzárendelés

Használhatja a *Set-AzureRmPolicyAssignment* PowerShell-parancsmag használatával frissítse a díjszabás réteg Standard vagy ingyenes egy meglévő házirend-hozzárendelés. Példa:

```
Set-AzureRmPolicyAssignment -Id /subscriptions/<subscriptionId/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/<policyAssignmentID> -Sku @{Name='A1';Tier='Standard'}
```

A parancsmaggal kapcsolatban további információkért lásd: [Set-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/Set-AzureRmPolicyAssignment?view=azurermps-4.4.1).

## <a name="next-steps"></a>További lépések

Tekintse át a következő cikkekben további információt a parancsok és lekérdezések ebben a cikkben.

- [Az Azure REST API-erőforrások](/rest/api/resources/)
- [Az Azure erőforrás-kezelő PowerShell-modulok](/powershell/module/azurerm.resources/?view=azurermps-4.4.1#policies)
- [Az Azure CLI házirend parancsok](/cli/azure/policy?view=azure-cli-latest)
- [Házirend Insights erőforrás-szolgáltató REST API-referencia](/rest/api/policy-insights)
