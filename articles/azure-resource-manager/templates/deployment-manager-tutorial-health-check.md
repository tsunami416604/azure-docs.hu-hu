---
title: Az Azure Deployment Manager állapotfelmérésének használata
description: Az állapotfelmérés segítségével biztonságosan üzembe helyezheti az Azure-erőforrásokat az Azure Deployment Manager rel.
author: mumian
ms.date: 10/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 765c73a3ab8d5fa8939abe597d0141b24b59ac52
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76152477"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>Oktatóanyag: Állapot-ellenőrzés használata az Azure Deployment Managerben (nyilvános előzetes verzió)

Ismerje meg, hogyan integrálhatja az állapotfelmérést az [Azure Deployment Managerbe.](./deployment-manager-overview.md) Ez az oktatóanyag az [Azure Deployment Manager használata erőforrás-kezelősablonokhoz](./deployment-manager-tutorial.md) oktatóanyagon alapul. Ezt az oktatóanyagot be kell fejeznie, mielőtt ezt folytatná.

Az [Azure Deployment Manager erőforrás-kezelősablonokkal](./deployment-manager-tutorial.md)való használata című lapban használt bevezetési sablonban várakozási lépést használt. Ebben az oktatóanyagban a várakozási lépést egy állapot-ellenőrzési lépéssel helyettesítheti.

> [!IMPORTANT]
> Ha az előfizetés meg van jelölve a Canary számára az új Azure-funkciók teszteléséhez, csak az Azure Deployment Manager használatával telepítheti a Kanári-régiókban. 

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Állapot-ellenőrzési szolgáltatás szimulátor létrehozása
> * A bevezetési sablon felülvizsgálata
> * A topológia telepítése
> * A bevezetés telepítése nem megfelelő állapottal
> * A bevezetés telepítésének ellenőrzése
> * A bevezetés telepítése kifogástalan állapotú
> * A bevezetés telepítésének ellenőrzése
> * Az erőforrások eltávolítása

További források:

* Az [Azure Deployment Manager REST API-hivatkozása](https://docs.microsoft.com/rest/api/deploymentmanager/).
* [Egy Azure Deployment Manager-minta](https://github.com/Azure-Samples/adm-quickstart).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Teljes [Azure Deployment Manager használata erőforrás-kezelősablonokkal.](./deployment-manager-tutorial.md)

## <a name="install-the-artifacts"></a>Az összetevők telepítése

Töltse le [a sablonokat és az összetevőket,](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) és csomagolja ki helyileg, ha még nem tette meg. Ezután futtassa a [PowerShell-parancsfájlt,](./deployment-manager-tutorial.md#prepare-the-artifacts)amely a Műtermékek előkészítése kor található. A parancsfájl létrehoz egy erőforráscsoportot, létrehoz egy tárolótárolót, létrehoz egy blobtárolót, feltölti a letöltött fájlokat, majd létrehoz egy SAS-jogkivonatot.

Készítsen másolatot az URL-címRől A SAS-jogkivonat. Az URL-címet a két paraméterfájlban, a topológiaparaméterek és a bevezetési paraméterek fájljában kell bemásolni egy mezőbe.

Nyissa meg a CreateADMServiceTopology.Parameters.json programot, és frissítse a **projectName** és **artifactSourceSASLocation**értékét.

Nyissa meg a CreateADMRollout.Parameters.json programot, és frissítse a **projectName** és **artifactSourceSASLocation**értékét.

## <a name="create-a-health-check-service-simulator"></a>Állapot-ellenőrzési szolgáltatás szimulátor létrehozása

Éles környezetben általában egy vagy több figyelési szolgáltatót használ. Annak érdekében, hogy az állapotintegráció a lehető legegyszerűbb legyen, a Microsoft együttműködik néhány, a szolgáltatás állapotfigyelő vállalataival, hogy egy egyszerű másolási/beillesztési megoldást biztosítson az állapotellenőrzések és a központi telepítések integrálásához. Ezeknek a vállalatoknak a listáját az [Egészségfigyelő szolgáltatók című témakörben található.](./deployment-manager-health-check.md#health-monitoring-providers) Ebben az oktatóanyagban hozzon létre egy [Azure-függvényt](/azure/azure-functions/) egy állapotfigyelő szolgáltatás szimulálására. Ez a függvény állapotkódot vesz fel, és ugyanazt a kódot adja vissza. Az Azure Deployment Manager-sablon az állapotkód segítségével határozza meg, hogyan kell eljárni a központi telepítés.

A következő két fájl az Azure-függvény üzembe helyezéséhez használatos. Nem kell letölteni ezeket a fájlokat, hogy menjen át a tutorial.

* Erőforrás-kezelő sablon, [https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json)amely a helyen található. Ezt a sablont egy Azure-függvény létrehozásához telepíti.
* Az Azure Függvény forráskódjának [https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip)zip-fájlja, . Ezt a zip-et az Erőforrás-kezelő sablon nevezi meg.

Az Azure-függvény üzembe helyezéséhez válassza a **Próbálja ki** az Azure Cloud rendszerhéj megnyitásához, majd illessze be a következő parancsfájlt a rendszerhéj ablakába.  A kód beillesztéséhez kattintson a jobb gombbal a rendszerhéj ablakára, majd válassza a **Beillesztés parancsot.**

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json" -projectName $projectName
```

Az Azure-funkció ellenőrzése és tesztelése:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
1. Nyissa meg az erőforráscsoportot.  Az alapértelmezett név a projekt neve **rg** csatolt.
1. Válassza ki az alkalmazásszolgáltatást az erőforráscsoportból.  Az alkalmazásszolgáltatás alapértelmezett neve a **webapp-hoz** csatolt projektnév.
1. Bontsa ki **a Functions**csomópontot, majd válassza a **HttpTrigger1 lehetőséget.**

    ![Azure Deployment Manager állapotfelmérés Azure-függvény](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. Válassza a ** &lt;/> Get függvény URL-címének bekerülése**lehetőséget.
1. Válassza a **Másolás** lehetőséget, ha az URL-címet a vágólapra szeretné másolni.  Az URL-cím hasonló a következőkhöz:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Cserélje `{healthStatus}` le az URL-címet egy állapotkódra. Ebben az oktatóanyagban használja **a nem megfelelő állapotú** a nem megfelelő állapotú forgatókönyv teszteléséhez, és használja a **kifogástalan** vagy **figyelmeztetésa** a kifogástalan forgatókönyv teszteléséhez. Hozzon létre két URL-t, az egyik a nem megfelelő állapotú, a másik pedig kifogástalan állapotú. Példák:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Az oktatóanyag befejezéséhez mindkét URL-re szüksége van.

1. Az állapotfigyelési szimulátor teszteléséhez nyissa meg az utolsó lépésben létrehozott URL-címeket.  Az egészségtelen állapot ra vonatkozó eredményeknek hasonlónak kell lenniük:

    ```
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>A bevezetési sablon felülvizsgálata

A szakasz célja, hogy megmutassa, hogyan vehet fel állapotellenőrzési lépést a bevezetési sablonba.

1. Nyissa **meg a CreateADMRollout.json** t, amelyet az [Azure Deployment Manager használata erőforrás-kezelősablonokkal](./deployment-manager-tutorial.md)létrehozott. Ez a JSON fájl a letöltés része.  Lásd: [Előfeltételek](#prerequisites).
1. Adjon hozzá még két paramétert:

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

1. Cserélje le a várakozási lépés erőforrás-definícióját egy állapotellenőrzési lépés erőforrás-definíciójára:

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

    A definíció alapján a bevezetés akkor folytatódik, ha az állapot *kifogástalan* vagy *figyelmeztetés.*

1. Frissítse a bevezetési definíció **dependsON** szolgáltatását az újonnan meghatározott állapot-ellenőrzési lépéssel:

    ```json
    "dependsOn": [
      "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
      "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. A **stepGroups** frissítése az állapot-ellenőrzési lépéshez. A **healthCheckStep** neve a **stepGroup2** **postDeploymentSteps** című részében történik. **a stepGroup3** és **a stepGroup4** csak akkor van telepítve, ha a kifogástalan állapot *kifogástalan* vagy *figyelmeztetés.*

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

    Ha összehasonlítja a **stepGroup3** szakasz előtt és után is felülvizsgálták, ez a szakasz most attól **függ, stepGroup2**.  Erre akkor van szükség, ha a **3.**

    Az alábbi képernyőkép bemutatja a módosított területeket és az állapotfelmérési lépés használatának módját:

    ![Az Azure Deployment Manager állapotellenőrző sablonja](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>A topológia telepítése

Futtassa a következő PowerShell-parancsfájlt a topológia üzembe helyezéséhez. Ugyanazt a **CreateADMServiceTopology.json** és **CreateADMServiceTopology.Parameters.json parancsot** kell használnia, mint amelyet az [Azure Deployment Manager használata erőforrás-kezelősablonokkal](./deployment-manager-tutorial.md)használt.

```azurepowershell
# Create the service topology
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
    -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
```

Ellenőrizze, hogy a szolgáltatástopológia és az alapjául szolgáló erőforrások sikeresen létrejöttek-e az Azure Portal használatával:

![Azure Deployment Manager-oktatóanyag – üzembe helyezett szolgáltatástopológia-erőforrások](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

Az erőforrások megjelenítéséhez be kell jelölnie a **Rejtett típusok megjelenítése** jelölőnégyzetet.

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>A bevezetés telepítése a nem megfelelő állapotú állapottal

Használja az [Állapotfelmérés szolgáltatás szimulátorának létrehozása](#create-a-health-check-service-simulator)című csoportban létrehozott nem megfelelő állapotú állapot-URL-címet. Szüksége van a módosított **CreateADMServiceTopology.json** és ugyanazt a **CreateADMServiceTopology.Parameters.json,** hogy az [Azure Deployment Manager használata erőforrás-kezelő sablonok](./deployment-manager-tutorial.md).

```azurepowershell-interactive
$healthCheckUrl = Read-Host -Prompt "Enter the health check Azure function URL"
$healthCheckAuthAPIKey = $healthCheckUrl.Substring($healthCheckUrl.IndexOf("?code=")+6, $healthCheckUrl.Length-$healthCheckUrl.IndexOf("?code=")-6)
$healthCheckUrl = $healthCheckUrl.Substring(0, $healthCheckUrl.IndexOf("?"))

# Create the rollout
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
    -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json" `
    -healthCheckUrl $healthCheckUrl `
    -healthCheckAuthAPIKey $healthCheckAuthAPIKey
```

> [!NOTE]
> `New-AzResourceGroupDeployment`egy aszinkron hívás. A sikeres üzenet csak azt jelenti, hogy a telepítés sikeresen megkezdődött. A telepítés ellenőrzéséhez `Get-AZDeploymentManagerRollout`használja a használatát.  Lásd a következő eljárást.

A bevezetési folyamat ellenőrzése a következő PowerShell-parancsfájl használatával:

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

A következő mintakimenet azt mutatja, hogy a telepítés nem megfelelő állapot miatt sikertelen volt:

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

A bevezetés befejezése után egy további erőforráscsoportot hoz létre az USA nyugati részére.

## <a name="deploy-the-rollout-with-the-healthy-status"></a>A bevezetés telepítése kifogástalan állapottal

Ismételje meg ezt a szakaszt a kifogástalan állapot URL-címével történő bevezetés újbóli üzembe helyezéséhez.  A bevezetés befejezése után még egy, az USA keleti részére létrehozott erőforráscsoport jelenik meg.

## <a name="verify-the-deployment"></a>A telepítés ellenőrzése

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
2. Tallózással keresse meg az újonnan létrehozott webalkalmazásokat a bevezetés üzembe helyezése során létrehozott új erőforráscsoportok alatt.
3. Nyissa meg a webalkalmazást egy webböngészőben. Ellenőrizze a helyet és a verziót az index.html fájlban.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza a bal oldali menü **Erőforráscsoport** lehetőséget.
2. A **Szűrés név alapján** mezővel szűkítse a keresést az oktatóanyagban létrehozott erőforráscsoportokra. 3–4 erőforrásnak kell lennie:

    * projectName>rg : a Deployment Manager erőforrásait tartalmazza. ** &lt;**
    * projectName>ServiceWUSrg : a ServiceWUS által meghatározott erőforrásokat tartalmazza. ** &lt;**
    * projectName>ServiceEUSrg : a ServiceEUS által meghatározott erőforrásokat tartalmazza. ** &lt;**
    * A felhasználó által meghatározott felügyelt identitás erőforráscsoportja.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza a felső menü **Erőforráscsoport törlése** parancsát.
5. Ennek a két lépésnek az ismétlésével törölje az oktatóanyagban létrehozott több erőforráscsoportot is.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja az Azure Deployment Manager állapot-ellenőrzési szolgáltatását. További információért tekintse meg [az Azure Resource Manager dokumentációját](/azure/azure-resource-manager/).
