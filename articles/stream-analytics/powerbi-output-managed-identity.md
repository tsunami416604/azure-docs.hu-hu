---
title: Az Azure Stream Analytics-feladat Power BI-kimenetre való hitelesítéséhez használja a Felügyelt identitást
description: Ez a cikk ismerteti, hogyan használhatja a felügyelt identitások az Azure Stream Analytics-feladat power BI-kimenethitelesítésére.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/10/2020
ms.openlocfilehash: 31a5195038ef25acadc08e2acbedf8471b25833c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261414"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi"></a>Az Azure Stream Analytics-feladat Power BI-ba való hitelesítéséhez használja a Felügyelt identitást

A Power BI-ba történő kimenet [felügyelt identitáshitelesítése](../active-directory/managed-identities-azure-resources/overview.md) közvetlen hozzáférést biztosít a Stream Analytics-feladatoknak a Power BI-fiókon belüli munkaterülethez. Ez a funkció lehetővé teszi a Stream Analytics-feladatok központi telepítésének teljes körű automatizáltságát, mivel a felhasználónak már nincs szüksége arra, hogy interaktívan jelentkezzen be a Power BI-ba az Azure Portalon keresztül. Emellett a Power BI-ba író, hosszú ideig futó feladatok mostantól jobban támogatottak, mivel nem kell rendszeres időközönként újra engedélyeznie a feladatot.

Ez a cikk bemutatja, hogyan engedélyezheti a felügyelt identitást a Stream Analytics-feladat Power BI-kimenete(i)t az Azure Portalon és az Azure Resource Manager központi telepítésén keresztül.

## <a name="prerequisites"></a>Előfeltételek

A szolgáltatás használatához a következők szükségesek:

- [Pro licenccel](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro)rendelkező Power BI-fiók .

- Frissített munkaterület a Power BI-fiókban. További részletekért olvassa el a Power BI ezen funkcióról tett [bejelentését.](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/)

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>Stream Analytics-feladat létrehozása az Azure Portalhasználatával

1. Hozzon létre egy új Stream Analytics-feladatot, vagy nyisson meg egy meglévő feladatot az Azure Portalon. A képernyő bal oldalán található menüsorban válassza a **Konfigurálás**csoportban a **Felügyelt identitás** lehetőséget. Győződjön meg arról, hogy a "Rendszeráltal hozzárendelt felügyelt identitás használata" lehetőség van kiválasztva, majd válassza a **Mentés** gombot a képernyő alján.

   ![A Stream Analytics felügyelt identitásának konfigurálása](./media/common/stream-analytics-enable-managed-identity.png)

2. A kimenet konfigurálása előtt adjon hozzáférést a Stream Analytics-feladatnak a Power BI-munkaterülethez a cikk [Power BI-munkaterülethez való hozzáférésének a Stream Analytics-feladathoz való hozzáférése](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) című témakör utasításait követve.

3. Nyissa meg a Stream Analitikus **feladatának Kimenetek** szakaszát, válassza a **+ Hozzáadás**lehetőséget, majd válassza a **Power BI**lehetőséget. Ezután válassza az **Engedélyezés** gombot, és jelentkezzen be a Power BI-fiókjával.

   ![Engedélyezés Power BI-fiókkal](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. Az engedélyezést követően a legördülő lista fel lesz töltve az összes olyan munkaterülettel, amelyhez hozzáférése van. Válassza ki az előző lépésben engedélyezett munkaterületet. Ezután válassza **a Felügyelt identitás lehetőséget** a "Hitelesítési módként". Végül kattintson a **Mentés** gombra.

   ![Power BI-kimenet konfigurálása felügyelt identitással](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Az Azure Resource Manager üzembe helyezése

Az Azure Resource Manager lehetővé teszi, hogy teljes mértékben automatizálja a Stream Analytics-feladat üzembe helyezését. A Resource Manager-sablonokat az Azure PowerShell vagy az [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)használatával telepítheti. Az alábbi példák az Azure CLI használatával.


1. **Microsoft.StreamAnalytics/streamingjobs** erőforrást felügyelt identitással úgy hozhat létre, hogy a következő tulajdonságot építi fel az Erőforrás-kezelő sablon erőforrásszakaszában:

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   Ez a tulajdonság arra utasítja az Azure Resource Managert, hogy hozza létre és kezelje a Stream Analytics-feladat identitását. Az alábbiakban egy példa: Resource Manager-sablon, amely egy Stream Analytics-feladatot telepít felügyelt identitással és felügyelt identitást használó Power BI-kimeneti fogadóval:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "pbi_managed_id",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "datasource":{
                                    "type":"PowerBI",
                                    "properties":{
                                        "dataset": "dataset_name",
                                        "table": "table_name",
                                        "groupId": "01234567-89ab-cdef-0123-456789abcdef",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    Telepítse a fenti feladatot az **ExampleGroup** erőforráscsoportba az alábbi Azure CLI-parancs használatával:

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. A feladat létrehozása után az Azure Resource Manager segítségével olvassa be a feladat teljes definícióját.

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    A fenti parancs az alábbihoz hasonló választ ad vissza:

    ```json
    {
        "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/streamingjobs/<resource-name>",
        "identity": {
            "principalId": "<principal-id>",
            "tenantId": "<tenant-id>",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "<resource-name>",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "<job-id>",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "<resource-group>",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

    Ha a Power BI REST API-jával szeretné hozzáadni a Stream Analytics-feladatot a Power BI-munkaterülethez, jegyezze fel a visszaadott "principalId"-ot.

3. Most, hogy a feladat létrejött, folytassa [a Cikk Power BI-munkaterületi elérésének a Stream Analytics-feladathoz való hozzáféréssel](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) című szakaszával.


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>A Stream Analytics-feladat hozzáférésének leengedése a Power BI-munkaterülethez

Most, hogy a Stream Analytics-feladat létrejött, hozzáférést kaphat egy Power BI-munkaterülethez.

### <a name="use-the-power-bi-ui"></a>A Power BI felhasználói felületének használata

   > [!Note]
   > Ahhoz, hogy a Stream Analytics-feladatot a Felhasználói felület használatával hozzáadja a Power BI-munkaterülethez, a Power BI felügyeleti **portálfejlesztői beállításaiban** is engedélyeznie kell a szolgáltatásegyszerű hozzáférést. További részletekért olvassa el az [Első lépések szolgáltatástaggal.](https://docs.microsoft.com/power-bi/developer/embed-service-principal)

1. Nyissa meg a munkaterület hozzáférési beállításait. Ebben a cikkben további részleteket talál: [Adjon hozzáférést a munkaterülethez.](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace)

2. Írja be a Stream Analytics-feladat nevét a szövegmezőbe, és válassza a **Közreműködő** lehetőséget hozzáférési szintként.

3. Válassza **a Hozzáadás** és az ablaktábla bezárása lehetőséget.

   ![Stream Analytics-feladat hozzáadása a Power BI-munkaterülethez](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>A Power BI PowerShell-parancsmagok használata

1. Telepítse a `MicrosoftPowerBIMgmt` Power BI PowerShell-parancsmagjait.

   > [!Important]
   > Győződjön meg arról, hogy a parancsmagok 1.0.821-es vagy újabb verzióját használja.

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. Jelentkezzen be a Power BI-ba.

```powershell
Login-PowerBI
```

3. Adja hozzá a Stream Analytics-feladatot közreműködőként a munkaterülethez.

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>A Power BI REST API használata

A Stream Analytics-feladat a "Csoportfelhasználó hozzáadása" REST API közvetlen használatával is hozzáadható a munkaterület közreműködőjeként. Az API teljes dokumentációja itt található: [Csoportok - Csoportfelhasználó hozzáadása](https://docs.microsoft.com/rest/api/power-bi/groups/addgroupuser).

**Kérésminta**
```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/users
```
A kérelem törzse
```json
{
    "groupUserAccessRight": "Contributor",
    "identifier": "<principal-id>",
    "principalType": "App"
}
```

## <a name="limitations"></a>Korlátozások
Az alábbiakban a korlátozások ezt a funkciót:

- A klasszikus Power BI-munkaterületek nem támogatottak.

- Azure-fiókok Az Azure Active Directory nélkül.

- A több-bérlős hozzáférés nem támogatott. Egy adott Stream Analytics-feladathoz létrehozott egyszerű szolgáltatásnak ugyanabban az Azure Active Directory-bérlőben kell lennie, amelyben a feladatot létrehozták, és nem használható egy másik Azure Active Directory-bérlőben található erőforrással.

- [A felhasználó hozrendelt identitása](../active-directory/managed-identities-azure-resources/overview.md) nem támogatott. Ez azt jelenti, hogy nem adhatja meg a saját egyszerű szolgáltatását, amelyet a Stream Analytics-feladat használ. Az egyszerű szolgáltatást az Azure Stream Analytics-nek kell létrehoznia.

## <a name="next-steps"></a>További lépések

* [A Power BI irányítópultjának integrációja az Azure Stream Analytics szolgáltatással](./stream-analytics-power-bi-dashboard.md)
* [Az Azure Stream Analytics kimeneteinek megismerése](./stream-analytics-define-outputs.md)
