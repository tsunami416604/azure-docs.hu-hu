---
title: Az Azure Deployment Manager Resource Manager-sablonokkal való használata | Microsoft Docs
description: Az Azure Deployment Managerben használhat Resource Manager-sablonokat az Azure-erőforrások üzembe helyezéséhez.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.date: 05/31/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 4d6832346fb25ee09d97575c755cb2d85eb73cbe
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206605"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>Oktatóanyag: Állapot-ellenőrzése az Azure Deployment Manager (nyilvános előzetes verzió) használata

Ismerje meg, hogyan erőforrásállapot-ellenőrzése integrálását [Azure Deployment Manager](./deployment-manager-overview.md). Ebben az oktatóanyagban alapul a [használata az Azure Deployment Manager Resource Manager-sablonokkal](./deployment-manager-tutorial.md) oktatóanyag. Az oktatóanyag el kell végeznie, akkor erre a folytatás előtt.

A bevezetési sablonban használt [használata az Azure Deployment Manager Resource Manager-sablonokkal](./deployment-manager-tutorial.md), egy várakozási lépésben használt. Ebben az oktatóanyagban, cserélje le a várakozási lépés egy állapot-ellenőrzési lépést.

> [!IMPORTANT]
> Az előfizetés az új Azure-funkciók teszteléséhez Kanári van megjelölve, ha az Azure Deployment Manager csak használhatja a Tesztcsoportos régiók telepítését. 

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Hozzon létre a szolgáltatás szimulátort állapotának ellenőrzése
> * A bevezetési sablon módosítása
> * A topológia üzembe helyezése
> * A bevezetés nem kifogástalan állapotú üzembe helyezése
> * A bevezetési telepítés ellenőrzése
> * A bevezetés kifogástalan állapotú üzembe helyezése
> * A bevezetési telepítés ellenőrzése
> * Az erőforrások eltávolítása

További források:

- A [Azure Deployment Manager REST API-referencia](https://docs.microsoft.com/rest/api/deploymentmanager/).
- [Az Azure Deployment Manager minta](https://github.com/Azure-Samples/adm-quickstart).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Teljes [használata az Azure Deployment Manager Resource Manager-sablonokkal](./deployment-manager-tutorial.md).
* Töltse le [a sablonok és összetevők](https://armtutorials.blob.core.windows.net/admtutorial/ADMTutorial.zip) , amely ebben az oktatóanyagban használja.

## <a name="create-a-health-check-service-simulator"></a>Hozzon létre a szolgáltatás szimulátort állapotának ellenőrzése

Éles környezetben általában használhat egy vagy több figyelési szolgáltatót. Annak érdekében, hogy lehető egészségügyi integráció, a Microsoft dolgozott a egyes felső szolgáltatás állapotfigyelési cégeket azzal, hogy Ön egy egyszerű, másolási és beillesztési megoldással állapot-ellenőrzések integrálhatja az üzemelő példányok. Ezek a vállalatok listáját lásd: [állapotfigyelési szolgáltatók](./deployment-manager-health-check.md#health-monitoring-providers). A jelen oktatóanyag létrehoz egy [Azure-függvény](/azure/azure-functions/) egy állapotfigyelő szolgáltatás szimulálásához. Ez a függvény állapotkódot vesz igénybe, és ugyanazt a kódot adja vissza. Az Azure Deployment Manager-sablon az üzembe helyezést, hogyan lehet az állapotkódot használja.

A következő két fájlt az Azure-függvény üzembe helyezésére szolgálnak. Nem kell letölteni ezeket a fájlokat az oktatóanyagot.

* A Resource Manager-sablon található [ https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json ](https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json). Azure-függvény létrehozása a sablon központi telepítése.
* Az Azure-függvény-forráskódot zip-fájlban [ http://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip ](http://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip). A zip nevű hívja meg a Resource Manager-sablon.

Az Azure-függvény üzembe helyezéséhez válassza **kipróbálás** nyissa meg az Azure Cloud shellt, és illessze be a következő szkriptet a shell ablakába.  Illessze be a kódot, kattintson a jobb gombbal a rendszerhéj ablakát, és jelölje ki **illessze be**.

> [!IMPORTANT]
> **Projektnév** a PowerShell parancsfájl létrehozásához ebben az oktatóanyagban üzembe helyezett Azure-szolgáltatások neveit használja. Különböző Azure-szolgáltatásokat van eltérő követelmények vonatkoznak a nevek alapján. Annak érdekében, hogy a központi telepítés sikeres-e, válassza ki egy legalább 12 karakter csak kisbetűket és számokat a nevét.
> Mentse a projekt nevét. Használja az ugyanazon projectName az oktatóanyagot.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json" -projectName $projectName
```

Győződjön meg arról, és az Azure-függvény teszteléséhez:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
1. Nyissa meg az erőforráscsoportot.  Alapértelmezés szerint ez a projekt nevére a **rg** hozzáfűzve.
1. Válassza ki az app Service-ben az erőforráscsoportból.  Az app service alapértelmezett név a projekt nevére a **webapp** hozzáfűzve.
1. Bontsa ki a **funkciók**, majd válassza ki **HttpTrigger1**.

    ![Az Azure Deployment Manager állapot-ellenőrzése az Azure-függvény](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. Válassza ki  **&lt;/ > függvény URL-Címének lekérése**.
1. Válassza ki **másolási** az URL-cím másolása a vágólapra.  Az URL-cím hasonlít:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Cserélje le `{healthStatus}` az állapotkódot az URL-CÍMÉT. Ebben az oktatóanyagban használja **nem megfelelő állapotú** a nem megfelelő állapotú forgatókönyv teszteléséhez, és választhat **kifogástalan** vagy **figyelmeztetés** a megfelelő forgatókönyv teszteléséhez. Hozzon létre két URL-címet, az egyiket a nem kifogástalan állapot, pedig a kifogástalan állapotú. Példák:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Meg kell kitölteni, ebben az oktatóanyagban mindkét URL-címeket.

1. A figyelési állapot-szimulátort teszteléséhez nyissa meg az előző lépésben létrehozott URL-címeket.  Az eredményeket a nem kifogástalan állapot kell hasonló lesz:

    ```
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>A bevezetési sablon módosítása

Ez a szakasz az a célja, hogy bemutatják, hogyan foglalhat bele egy állapot-ellenőrzési lépést a bevezetési sablonban. Nem kell létrehozni a saját CreateADMRollout.json fájlt az oktatóanyag elvégzéséhez. A módosított bevezetési sablon megosztott egy tárfiókot, amelyet az ezt követő szakaszokban használatban van.

1. Nyissa meg **CreateADMRollout.json**. A JSON-fájlt egy a letöltés részét képezi.  Lásd: [Előfeltételek](#prerequisites).
1. Adja hozzá a két paramétert:

    ```json
    "healthCheckUrl": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check URL."
        }
    },
    "healthCheckAuthAPIKey": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check Azure Function function authorization key."
        }
    }
    ```

1. Cserélje le a várakozási lépés erőforrás-definíció egy állapotfigyelő ellenőrzési lépés erőforrás-definíció:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

    A definíció alapján a bevezetés folytatja-e az állapot vagy *kifogástalan* vagy *figyelmeztetés*.

1. Frissítés a **dependsON** a bevezetési definícióját tartalmazza az újonnan definiált állapot-ellenőrzési lépést:

    ```json
    "dependsOn": [
        "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
        "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. Frissítés **stepGroups** tartalmazza az állapot-ellenőrzési lépést. A **healthCheckStep** neve **postDeploymentSteps** , **stepGroup2**. **stepGroup3** és **stepGroup4** csak vannak telepítve, ha a megfelelő állapot *kifogástalan* vagy *figyelmeztetés*.

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

    Ha összehasonlítja a **stepGroup3** szakasz előtt és után módosult, ez a szakasz most függ **stepGroup2**.  Erre azért szükség mikor **stepGroup3** és a későbbi lépés csoportok függ a Szolgáltatásállapot-figyelést.

    Az alábbi képernyőképen látható a módosított területeket, és hogyan használja fel az állapot-ellenőrzési lépést:

    ![Az Azure Deployment Manager állapotának ellenőrzése sablon](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>A topológia üzembe helyezése

Az oktatóanyag leegyszerűsítése a topológia sablonok és összetevők meg vannak osztva a következő helyeken, hogy nem kell előkészíteni a saját példányát. Ha szeretné használni a saját, kövesse a [oktatóanyag: Az Azure Deployment Manager használata a Resource Manager-sablonok](./deployment-manager-tutorial.md).

* Topológia sablon: https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplates/CreateADMServiceTopology.json
* Összetevők tároló: https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore

A topológia üzembe helyezéséhez válassza **kipróbálás** a Cloud shellt, és illessze be a PowerShell-parancsfájlt.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"
$artifactLocation = "https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore?st=2019-05-06T03%3A57%3A31Z&se=2020-05-07T03%3A57%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=gOh%2Bkhi693rmdxiZFQ9xbKZMU1kbLJDqXw7EP4TaGlI%3D" | ConvertTo-SecureString -AsPlainText -Force

# Create the service topology
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMServiceTopology.json" `
    -namePrefix $projectName `
    -azureResourceLocation $location `
    -artifactSourceSASLocation $artifactLocation
```

Ellenőrizze, hogy a szolgáltatástopológia és az alapjául szolgáló erőforrások sikeresen létrejöttek-e az Azure Portal használatával:

![Azure Deployment Manager-oktatóanyag – üzembe helyezett szolgáltatástopológia-erőforrások](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

Az erőforrások megjelenítéséhez be kell jelölnie a **Rejtett típusok megjelenítése** jelölőnégyzetet.

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>A bevezetés nem kifogástalan állapotú üzembe helyezése

Az oktatóanyag leegyszerűsítése a módosított bevezetési sablon megosztott a következő helyeken, hogy nem kell előkészíteni a saját példányát. Ha szeretné használni a saját, kövesse a [oktatóanyag: Az Azure Deployment Manager használata a Resource Manager-sablonok](./deployment-manager-tutorial.md).

* Topológia sablon: https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json
* Összetevők tároló: https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore

A nem kifogástalan állapot létrehozott URL-cím használata [ellenőrzés Állapotfigyelő szolgáltatás szimulátort létrehozása](#create-a-health-check-service-simulator). A **managedIdentityID**, lásd: [hozzon létre felügyelt felhasználó által hozzárendelt identitások](./deployment-manager-tutorial.md#create-the-user-assigned-managed-identity).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$managedIdentityID = Read-Host -Prompt "Enter a user-assigned managed identity"
$healthCheckUrl = Read-Host -Prompt "Enter the health check Azure function URL"
$healthCheckAuthAPIKey = $healthCheckUrl.Substring($healthCheckUrl.IndexOf("?code=")+6, $healthCheckUrl.Length-$healthCheckUrl.IndexOf("?code=")-6)
$healthCheckUrl = $healthCheckUrl.Substring(0, $healthCheckUrl.IndexOf("?"))

$resourceGroupName = "${projectName}rg"
$artifactLocation = "https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore?st=2019-05-06T03%3A57%3A31Z&se=2020-05-07T03%3A57%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=gOh%2Bkhi693rmdxiZFQ9xbKZMU1kbLJDqXw7EP4TaGlI%3D" | ConvertTo-SecureString -AsPlainText -Force

# Create the rollout
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json" `
    -namePrefix $projectName `
    -azureResourceLocation $location `
    -artifactSourceSASLocation $artifactLocation `
    -managedIdentityID $managedIdentityID `
    -healthCheckUrl $healthCheckUrl `
    -healthCheckAuthAPIKey $healthCheckAuthAPIKey
```

> [!NOTE]
> `New-AzResourceGroupDeployment` egy aszinkron hívás van. A sikeres üzenetet csak azt jelenti, hogy az üzembe helyezés sikeresen megkezdődött. Az üzembe helyezés ellenőrzéséhez használja a `Get-AZDeploymentManagerRollout`.  Tekintse meg a következő eljárással.

A bevezetési folyamatban, a következő PowerShell-szkripttel ellenőrizheti:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$resourceGroupName = "${projectName}rg"
$rolloutName = "${projectName}Rollout"

# Get the rollout status
Get-AzDeploymentManagerRollout `
    -ResourceGroupName $resourceGroupName `
    -Name $rolloutName `
    -Verbose
```

Az alábbi kimeneti példa látható a telepítés nem sikerült, mert a nem kifogástalan állapot:

```output
Service: myhc0417ServiceWUSrg
    TargetLocation: WestUS
    TargetSubscriptionId: <Subscription ID>

    ServiceUnit: myhc0417ServiceWUSWeb
        TargetResourceGroup: myhc0417ServiceWUSrg

        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
Get-AzDeploymentManagerRollout :
Service: myhc0417ServiceWUSrg
    ServiceUnit: myhc0417ServiceWUSWeb
        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
At line:1 char:1
+ Get-AzDeploymentManagerRollout `
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : NotSpecified: (:) [Get-AzDeploymentManagerRollout], Exception
+ FullyQualifiedErrorId : RolloutFailed,Microsoft.Azure.Commands.DeploymentManager.Commands.GetRollout


ResourceGroupName       : myhc0417rg
BuildVersion            : 1.0.0.0
ArtifactSourceId        : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/artifactSources/myhc0417ArtifactSourceRollout
TargetServiceTopologyId : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/serviceTopologies/myhc0417ServiceTopology
Status                  : Failed
TotalRetryAttempts      : 0
Identity                : Microsoft.Azure.Commands.DeploymentManager.Models.PSIdentity
OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
Services                : {myhc0417ServiceWUS, myhc0417ServiceWUSrg}
Name                    : myhc0417Rollout
Type                    : Microsoft.DeploymentManager/rollouts
Location                : centralus
Id                      : /subscriptions/<Subscription ID>/resourcegroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/rollouts/myhc0417Rollout
Tags                    :
```

A bevezetés befejezése után, az USA nyugati Régiójában létrehozott egy további erőforráscsoportot kell láthatja.

## <a name="deploy-the-rollout-with-the-healthy-status"></a>A Bevezetés a kifogástalan állapotú üzembe helyezése

Ismételje meg ezt a szakaszt ismételt üzembe helyezése a Bevezetés a Kifogástalan állapot URL-címet.  A bevezetés befejezése után, az USA keleti Régiójában létrehozott egy további erőforráscsoportot kell láthatja.

## <a name="verify-the-deployment"></a>Az üzemelő példány ellenőrzése

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
2. Tallózással keresse meg az újonnan létrehozott webalkalmazásokat a bevezetés üzembe helyezése során létrehozott új erőforráscsoportok alatt.
3. Nyissa meg a webalkalmazást egy webböngészőben. Ellenőrizze a helyet és a verziót az index.html fájlban.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezővel szűkítse a keresést az oktatóanyagban létrehozott erőforráscsoportokra. 3–4 erőforrásnak kell lennie:

    * **&lt;namePrefix>rg**: A Deployment Manager-erőforrásokat tartalmazza.
    * **&lt;namePrefix>ServiceWUSrg**: A ServiceWUS által definiált erőforrásokat tartalmazza.
    * **&lt;namePrefix>ServiceEUSrg**: A ServiceEUS által definiált erőforrásokat tartalmazza.
    * A felhasználó által meghatározott felügyelt identitás erőforráscsoportja.
3. Válassza ki az erőforráscsoport nevét.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.
5. Ennek a két lépésnek az ismétlésével törölje az oktatóanyagban létrehozott több erőforráscsoportot is.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan állapotának ellenőrzése funkcióját az Azure Deployment Manager. További információért tekintse meg [az Azure Resource Manager dokumentációját](/azure/azure-resource-manager/).
