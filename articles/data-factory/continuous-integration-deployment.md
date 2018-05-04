---
title: Folyamatos integrációt és üzembe helyezés az Azure Data Factory |} Microsoft Docs
description: Megtudhatja, hogyan folyamatos integrációt és telepítést segítségével adat-előállító adatcsatornák áthelyezése (fejlesztési, tesztelési, éles) egyik környezetből a másikba.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: douglasl
ms.openlocfilehash: 16eec117514d040dc91b5d18b73d4cc6025c901e
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2018
---
# <a name="continuous-integration-and-deployment-in-azure-data-factory"></a>Folyamatos integrációt és üzembe helyezés az Azure Data Factory

Folyamatos integrációt az eljárást minden módosításakor azelőtt tesztelése a codebase automatikusan és a lehető leghamarabb. Folyamatos üzembe helyezés követi a tesztelés folyamatos integráció során történik, és leküldéses értesítések módosításokat egy átmeneti vagy üzemi rendszerre.

Az Azure Data Factory folyamatos integrációt és a központi telepítés azt jelenti, hogy a másikra a környezetek (fejlesztési, tesztelési, éles) mozgó adat-előállító adatcsatornák. Ehhez az folyamatos integrációt és telepítése, használata Data Factory UI integration Azure Resource Manager-sablonok. A Data Factory felhasználói felület hozhat létre a Resource Manager-sablon, amikor kiválaszt a **ARM-sablon** beállítások. Ha bejelöli **exportálása ARM-sablon**, a portál létrehozza a Resource Manager-sablon az adat-előállító és a kapcsolatok karakterláncok és más paramétereket tartalmazó konfigurációs fájlt. Majd kell létrehoznia egy konfigurációs fájl (fejlesztési, tesztelési, éles) környezetben. A fő Resource Manager sablon fájlt a környezetek változatlan marad.

Kilenc perces bevezető és a szolgáltatás bemutató tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

## <a name="create-a-resource-manager-template-for-each-environment"></a>Minden környezet Resource Manager sablon létrehozása
Válassza ki **exportálása ARM-sablon** a data factory a fejlesztési környezetet az erőforrás-kezelő sablonjának exportálása.

![](media/continuous-integration-deployment/continuous-integration-image1.png)

Ezután nyissa meg a teszt adat-előállító és a termelési adat-előállítót, és válassza **importálási ARM-sablon**.

![](media/continuous-integration-deployment/continuous-integration-image2.png)

Ez a művelet viszi az Azure-portálon, ahol az exportált sablon importálhatja. Válassza ki **a szerkesztőben saját sablon létrehozása** , majd **fájl betöltése** , és válassza ki a létrehozott erőforrás-kezelő sablont. Adja meg a beállításokat, és az adat-előállító és a teljes folyamat importálva van az éles környezetben.

![](media/continuous-integration-deployment/continuous-integration-image3.png)

![](media/continuous-integration-deployment/continuous-integration-image4.png)

Válassza ki **fájl betöltése** jelölje ki az exportált Resource Manager-sablon, majd adja meg az összes konfigurációs értékeket (például összekapcsolt szolgáltatások).

![](media/continuous-integration-deployment/continuous-integration-image5.png)

## <a name="continuous-integration-lifecycle"></a>Folyamatos integrációt életciklusa
Ez a teljes életciklusa folyamatos integrációt és a központi telepítést, amelyet használhat a Data Factory felhasználói felületén VSTS GIT integráció engedélyezése után:

1.  Állítsa be a fejlesztési VSTS, amelyben az összes adat-előállító erőforrásokhoz, mint a folyamatok, adatkészletek és így tovább hozhat létre az adat-előállítóban.

2.  A fejlesztők majd módosíthatja az erőforrások, például a folyamatok. Mivel ezeket a módosításokat, és kiválaszthatja, melyik **Debug** hogyan fut, a feldolgozási sor a legutóbbi módosítások megtekintéséhez.

3.  Után fejlesztők elégedett a módosításokat, akkor létrehozhat egy lekérési kérelmet a fiókiroda lekérni a módosításokat társak vizsgálja felül a főágba (vagy az együttműködés ág).

4.  Miután módosítások vannak a főágba, azokat is közzéteheti a fejlesztési gyári kiválasztásával **közzététel**.

5.  Amikor a csapat lépteti elő a teszt gyári és a termelési gyári módosításait készen áll, exportálhatja a Resource Manager-sablon a főágba, vagy bármely más fiókja abban az esetben, ha a főágba készít az élő fejlesztési adat-Előállítóban.

6.  Az exportált Resource Manager-sablon üzembe helyezhetők a teszt gyári és a termelési gyári különböző paraméterfájlt.

## <a name="automate-continuous-integration-with-vsts-releases"></a>Folyamatos integrációt VSTS kiadásainak automatizálásához

Az alábbiakban egy VSTS-Feloldás beállítása, úgy automatizálható, hogy egy adat-előállító több környezetekben való központi telepítésének lépéseit.

![Folyamatos integráció a VSTS ábrája](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Követelmények

-   Azure-előfizetés csatolva a Team Foundation Server vagy a VSTS használja a [ *Azure Resource Manager szolgáltatási végpont*](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

-   A Data Factory VSTS git konfigurálva.

-   Egy [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) tartalmazó a titkos kulcsok.

### <a name="set-up-a-vsts-release"></a>A VSTS-Feloldás beállítása

1.  Nyissa meg az ugyanabban a projektben megegyezik a konfigurálva a Data Factory VSTS oldalát.

2.  Kattintson a felső menüben **összeállítása, és felszabadíthatja a** &gt; **kiadásokban** &gt; **létrehozás kiadás definition**.

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

3.  Válassza ki a **üres folyamat** sablont.

4.  Adja meg a környezet nevét.

5.  A Git-összetevő hozzáadása, és válassza ki az egyazon adattárban konfigurálva a Data Factory. Válasszon `adf\_publish` , az alapértelmezett ág legújabb alapértelmezett verziójával.

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

6.  A titkos kulcsok Azure Key Vault beolvasása sikertelen. A titkos kulcsok kezeléséhez két módja van:

    a.  Vegye fel a titkos kulcsok paraméterfájl:

       -   Másolatot készít a paraméterfájlban a közzététel fiókirodai feltöltött, és adja meg a paraméterek le szeretné kérdezni a kulcstároló, az alábbi formátumban:

        ```json
        {
            "parameters": {
                "azureSqlReportingDbPassword": {
                    "reference": {
                        "keyVault": {
                            "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                        },
                        "secretName": " < secret - name > "
                    }
                }
            }
        }
        ```

       -   Ha ezt a módszert használja, a titkos kulcs van lekért a key vault automatikusan.

       -   A paraméterek fájlnak kell lennie a közzététel ágában is.

    b.  Adja hozzá egy [Azure Key Vault feladat](https://docs.microsoft.com/vsts/build-release/tasks/deploy/azure-key-vault):

       -   Válassza ki a **feladatok** lapra, hozzon létre egy új feladatot, keressen **Azure Key Vault** , és adja hozzá.

       -   A Key Vault tevékenységhez, válassza ki az előfizetést, amelyben a kulcstartót létrehozó, adjon meg hitelesítő adatokat, ha szükséges, és válassza a a key vault.

       ![](media/continuous-integration-deployment/continuous-integration-image8.png)

7.  Adja hozzá az Azure Resource Manager központi telepítési feladatot:

    a.  Új feladatot létrehozni, keresse meg **Azure erőforrás-csoport központi telepítésének**, majd vegye fel azt.

    b.  A központi telepítési feladatot válassza ki az előfizetést, erőforráscsoport és a Data Factory cél helyét, és adja meg a hitelesítő adatokat, ha szükséges.

    c.  Válassza ki a **létrehozás vagy frissítés erőforráscsoport** művelet.

    d.  Válassza ki **...** az a "**sablon**" mező. Tallózással keresse meg a Resource Manager-sablon (*ARMTemplateForFactory.json*), amely a portál a közzététel művelet hozta létre. Keresse meg a fájl a gyökérmappában található azon a `adf\_publish` ág.

    e.  Hajtsa végre ugyanezt a paraméterfájlban. Válassza ki a megfelelő fájlt, attól függően, hogy létrehozott egy másolatot, vagy az alapértelmezett fájl használata *ARMTemplateParametersForFactory.json*.

    f.  Válassza ki **...** mellett a **sablon paraméterének** mezőben, majd töltse ki a cél adat-előállító vonatkozó információkat. A hitelesítő adatokat, amelyeket a kulcstároló, a titkos kulcs ugyanazt a nevet használja a következő formátumban: feltéve, hogy a titkos kulcs neve van `cred1`, adja meg `"$(cred1)"` (ajánlatok) között.

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

8.  A kiadási definíció mentése.

9.  Ebben a kiadásban definícióból új verziót kell létrehoznia.

    ![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="grant-permissions-to-the-vsts-agent"></a>A hozzáférési jogot a VSTS-ügynökök
Az Azure Key Vault feladat megtagadta a hozzáférést az első alkalommal sikertelen lehet. A kiadás a naplók letöltéséhez, és keresse meg a `.ps1` fájl engedélyeket adhat a VSTS-ügynökök paranccsal. A parancs futtatható közvetlenül, vagy az egyszerű azonosító átmásolni a fájlt, és adja hozzá manuálisan a hozzáférési házirendben az Azure-portálon. (*Beolvasása* és *lista* rendszer szükséges minimális engedélyeket).

### <a name="update-active-triggers"></a>Aktív eseményindítók frissítése
Központi telepítés sikertelen lehet, ha aktív eseményindítók frissíti. Aktív eseményindítók frissítéséhez szükség manuális őket, és elindítja azokat a telepítés után. Erre a célra az Azure Powershell feladat adhat hozzá a következő példában látható módon:

1.  A feladatok lapon a VSTS-kiadás, keresse meg a **Azure Powershell** , és adja hozzá.

2.  Válasszon **Azure Resource Manager** kapcsolattal írja be, és jelölje ki az előfizetését.

3.  Válasszon **beágyazott parancsfájlja** a parancsfájlként írja be, és adja meg a kódot. A következő példakód leállítja az eseményindítók:

    ```powershell
    $armTemplate="$(env:System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json"

    $templateJson = Get-Content "$(env:System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json" | ConvertFrom-Json

    $triggersADF = Get-AzureRmDataFactoryV2Trigger -DataFactoryName
    $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $\_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

Hasonló lépésekkel és a hasonló kód használata (a a `Start-AzureRmDataFactoryV2Trigger` függvény) való telepítést követően újraindítható az eseményindítók.

## <a name="sample-template-and-script"></a>Minta sablon és a parancsfájl
Az alábbiakban a két minta használatába folyamatos integrációt és telepítést a Data Factory használható:

-   Példa a központi telepítési sablont, amelyet importálhat a VSTS.
-   Ezután elindítja a egy minta parancsfájlt eseményindítók központi telepítés előtt állítsa le és indítsa újra. A parancsfájl törli az erőforrást, amelyek el lettek távolítva a kódot is.

Íme egy minta központi telepítési sablont, amelyet importálhat a VSTS.

```json
{
    "source": 2,
    "id": 1,
    "revision": 51,
    "name": "Data Factory Prod Deployment",
    "description": null,
    "createdBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "createdOn": "2018-03-01T22:57:25.660Z",
    "modifiedBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "modifiedOn": "2018-03-14T17:58:11.643Z",
    "isDeleted": false,
    "path": "\\",
    "variables": {},
    "variableGroups": [],
    "environments": [{
        "id": 1,
        "name": "Prod",
        "rank": 1,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserprod"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 1
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 2
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 3
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param\n(\n    [parameter(Mandatory = $false)] [String] $rootFolder=\"C:\\Users\\sampleuser\\Downloads\\arm_template\",\n    [parameter(Mandatory = $false)] [String] $armTemplate=\"$rootFolder\\arm_template.json\",\n    [parameter(Mandatory = $false)] [String] $armTemplateParameters=\"$rootFolder\\arm_template_parameters.json\",\n    [parameter(Mandatory = $false)] [String] $domain=\"microsoft.onmicrosoft.com\",\n    [parameter(Mandatory = $false)] [String] $TenantId=\"72f988bf-86f1-41af-91ab-2d7cd011db47\",\n    [parame",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": "5.*"
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "secret1",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/1"
    }, {
        "id": 2,
        "name": "Staging",
        "rank": 2,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserstaging"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 4
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 5
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 6
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "16a37943-8b58-4c2f-a3d6-052d6f032a07",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param(\n$x,\n$y,\n$z)\nwrite-host \"----------\"\nwrite-host $x\nwrite-host $y\nwrite-host $z | ConvertTo-SecureString\nwrite-host \"----------\"",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/2"
    }],
    "artifacts": [{
        "sourceId": "19749ef3-2f42-49b5-9696-f28b49faebcb:a6c88f30-5e1f-4de8-b24d-279bb209d85f",
        "type": "Git",
        "alias": "Dev",
        "definitionReference": {
            "branches": {
                "id": "adf_publish",
                "name": "adf_publish"
            },
            "checkoutSubmodules": {
                "id": "",
                "name": ""
            },
            "defaultVersionSpecific": {
                "id": "",
                "name": ""
            },
            "defaultVersionType": {
                "id": "latestFromBranchType",
                "name": "Latest from default branch"
            },
            "definition": {
                "id": "a6c88f30-5e1f-4de8-b24d-279bb209d85f",
                "name": "Dev"
            },
            "fetchDepth": {
                "id": "",
                "name": ""
            },
            "gitLfsSupport": {
                "id": "",
                "name": ""
            },
            "project": {
                "id": "19749ef3-2f42-49b5-9696-f28b49faebcb",
                "name": "Prod"
            }
        },
        "isPrimary": true
    }],
    "triggers": [{
        "schedule": {
            "jobId": "b5ef09b6-8dfd-4b91-8b48-0709e3e67b2d",
            "timeZoneId": "UTC",
            "startHours": 3,
            "startMinutes": 0,
            "daysToRelease": 31
        },
        "triggerType": 2
    }],
    "releaseNameFormat": "Release-$(rev:r)",
    "url": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1",
    "_links": {
        "self": {
            "href": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1"
        },
        "web": {
            "href": "https://sampleuser.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_release?definitionId=1"
        }
    },
    "tags": [],
    "properties": {
        "DefinitionCreationSource": {
            "$type": "System.String",
            "$value": "ReleaseNew"
        }
    }
}
```

Íme egy minta parancsfájlt eseményindítók központi telepítés előtt állítsa le, és ezt követően indítsa újra az eseményindítók:

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $rootFolder="$(env:System.DefaultWorkingDirectory)/Dev/",
    [parameter(Mandatory = $false)] [String] $armTemplate="$rootFolder\arm_template.json",
    [parameter(Mandatory = $false)] [String] $ResourceGroupName="sampleuser-datafactory",
    [parameter(Mandatory = $false)] [String] $DataFactoryName="sampleuserdemo2",
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true

)


$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-AzureRmDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and $_.properties.pipelines.Count -gt 0} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { Stop-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force }
}
else {

    #start Active triggers
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force }

    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzureRmDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzureRmDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #delte resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { Remove-AzureRmDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { Remove-AzureRmDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { Remove-AzureRmDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { Remove-AzureRmDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { Remove-AzureRmDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force }
}
```
