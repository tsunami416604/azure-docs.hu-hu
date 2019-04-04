---
title: A folyamatos integrációt és teljesítést az Azure Data Factoryban |} A Microsoft Docs
description: Ismerje meg, hogyan lehet folyamatos integrációt és teljesítést segítségével helyezze át a Data Factory-folyamatok (fejlesztési, tesztelési és éles környezetben) egyik környezetből a másikba.
services: data-factory
documentationcenter: ''
author: gauravmalhot
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: gamal
manager: craigg
ms.openlocfilehash: 2edd4e28a0dd67be3c06159bce2e968d681b7f70
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905256"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Folyamatos integráció és teljesítés (CI/CD) az Azure Data Factoryban

Folyamatos integráció az eljárás a tesztelési minden módosítás történik az automatikusan és a lehető leghamarabb kódbázis. Folyamatos Készregyártás követi a tesztelés, amely történik a folyamatos integráció során, majd leküldi a módosítások egy átmeneti és éles rendszerbe.

Az Azure Data Factoryhoz folyamatos integráció és teljesítés azt jelenti, hogy a másikra történő áthelyezésének Data Factory-folyamatok az egyik környezetből (fejlesztési, tesztelési és éles környezetben). Ehhez a folyamatos integráció és teljesítés, Data Factory felhasználói felülete integrációs használhatja az Azure Resource Manager-sablonok. A Data Factory felhasználói felülete egy Resource Manager-sablont hozhat létre, kiválasztásakor a **ARM-sablon** beállítások. Ha bejelöli **exportálása ARM-sablon**, a portál létrehozza a Resource Manager-sablon a data factory és a egy konfigurációs fájl, amely tartalmazza az összes kapcsolat-karakterlánc és más paramétereket. Akkor rendelkezik az egyes környezetekhez (fejlesztési, tesztelési és éles környezetben) egy konfigurációs fájl létrehozása céljából. A fő Resource Manager-sablonfájl minden környezet esetében ugyanaz marad.

A 9 perces bevezető és a funkció bemutatójáért tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-manager-template-for-each-environment"></a>Az egyes környezetekhez Resource Manager-sablon létrehozása
Válassza ki **exportálása ARM-sablon** a Resource Manager-sablon a fejlesztési környezetben a data Factory exportálása.

![](media/continuous-integration-deployment/continuous-integration-image1.png)

Ezután nyissa meg a teszt a data factory és az éles környezetben a data factory, majd válassza ki **importálás ARM-sablon**.

![](media/continuous-integration-deployment/continuous-integration-image2.png)

Ez a művelet megnyílik az Azure Portalon, ahol az exportált sablon importálhatja. Válassza ki **a szerkesztőben saját sablon készítése** , majd **fájl betöltése** , és válassza ki a létrehozott Resource Manager-sablont. Adja meg a beállításokat, és a data factory és a teljes folyamat importálása az éles környezetben.

![](media/continuous-integration-deployment/continuous-integration-image3.png)

![](media/continuous-integration-deployment/continuous-integration-image4.png)

Válassza ki **fájl betöltése** válassza ki az exportált Resource Manager-sablont, és adja meg az összes konfigurációs értékeket (például a társított szolgáltatások).

![](media/continuous-integration-deployment/continuous-integration-image5.png)

**Kapcsolati karakterláncok**. Megtalálhat a kapcsolati karakterláncok a cikkekben tudnivalók az egyéni összekötők létrehozásához szükséges. Az Azure SQL Database, lásd a [másolhat az Azure SQL Database-ből az Azure Data Factory használatával](connector-azure-sql-database.md). Ellenőrizze a megfelelő kapcsolati karakterláncot – a társított szolgáltatás, például: is megnyithatja Kódnézet az erőforrás a Data Factory felhasználói felületén. A kód nézetben azonban a jelszavának vagy fiókjának titkoskulcs-darabjának a kapcsolati karakterláncot a rendszer eltávolítja. Kód nézet megnyitásához, az alábbi képernyőfelvételen kiemelt ikonnal.

![Nyissa meg a kapcsolati karakterlánc kód megtekintéséhez](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="continuous-integration-lifecycle"></a>Folyamatos integráció életciklusa
Itt látható a teljes életciklusát a folyamatos integráció és teljesítés használható a Data Factory felhasználói felületén Azure Adattárakkal Git-integráció engedélyezése után:

1.  Állítsa be egy fejlesztési data factoryt az Azure-Adattárakkal, amelyben minden fejlesztő hozhat létre Data Factory-erőforrások, például folyamatokat, adatkészletek és így tovább.

1.  A fejlesztők ezután módosíthatja az erőforrások, például folyamatokat. Mivel ezek a módosításokat, és kiválaszthatja **Debug** hogyan a folyamatfuttatások a legutóbbi módosítások megtekintéséhez.

1.  A fejlesztők elégedett a módosításokat, miután is létrehozhatnak lekéréses kérelmek azok ágból beolvasni a társaknak véleményező módosításokat a master ággal (vagy az együttműködési ág).

1.  Változások a főágban van, miután azok is közzéteheti a fejlesztési gyári kiválasztásával **közzététel**.

1.  Ha készen áll a módosítások a teszt factory és az éles előállító előléptetése a csapat, akkor exportálhatja a Resource Manager-sablon a master ágról, vagy bármely más ágból abban az esetben a "master" ága biztonsági mentést készít az élő fejlesztése a Data Factory.

1.  Az exportált Resource Manager-sablon különböző paraméterfájlokkal a teszt factory és az éles előállító is telepíthető.

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>Folyamatos integráció Azure folyamatok kiadásokban a automatizálása

Az alábbiakban a lépéseket egy Azure-folyamatok verzió beállításához, adat-előállító több környezethez való telepítésének automatizálásához.

![Folyamatos integráció Azure folyamatok ábrája](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Követelmények

-   A Team Foundation Server vagy az Azure-kódtárak a kapcsolódó Azure-előfizetés a [*Azure Resource Manager-szolgáltatásvégpont*](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

-   Konfigurált Azure-Adattárakkal Git-integrációval rendelkező Data Factoryt.

-   Egy [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) tartalmazó a titkos kulcsok.

### <a name="set-up-an-azure-pipelines-release"></a>Egy Azure-folyamatok-verzió beállítása

1.  Nyissa meg az Azure-Adattárakkal oldalát, a Data factoryval konfigurált ugyanabban a projektben.

1.  Kattintson a felső menüben **Azure folyamatok** &gt; **kiadásokban** &gt; **kiadási definíció létrehozása**.

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Válassza ki a **üres folyamat** sablont.

1.  Adja meg a környezet nevét.

1.  A Git-összetevő hozzáadása, és válassza ki az adat-előállító konfigurálva az egyazon adattárban. Válasszon `adf_publish` , az alapértelmezett ággal alapértelmezett legújabb verziójával.

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Egy Azure Resource Manager üzembe helyezési feladat hozzáadása:

    a.  Új feladat létrehozása, keressen rá a **Azure erőforráscsoport-telepítés**, és adja hozzá.

    b.  A központi telepítési feladatot válassza ki az előfizetést, erőforráscsoportot és a célként megadott adat-előállító helyét, és szükség esetén adja meg a hitelesítő adatokat.

    c.  Válassza ki a **erőforráscsoport létrehozásának vagy frissítésének** művelet.

    d.  Válassza ki **...** az a **sablon** mező. Keresse meg a Resource Manager-sablon (*ARMTemplateForFactory.json*), amely a közzététel a portál által létrehozott. Keresse meg a fájl a mappában `<FactoryName>` , a `adf_publish` ágat.

    e.  Hajtsa végre ugyanezt a paramétereket tartalmazó fájlt. Válassza ki a megfelelő fájlt, attól függően, hogy létrehozott egy másolatot, vagy használja az alapértelmezett fájl *ARMTemplateParametersForFactory.json*.

    f.  Válassza ki **...** mellett a **Sablonparaméterek felülbírálása** mezőben, majd töltse ki a cél a Data Factory az információkat. A hitelesítő adatokat, amelyeket a key vaultban, használja ugyanazt a nevet a titkos kulcsot a következő formátumban: Ha a titkos kód nevét `cred1`, adja meg `"$(cred1)"` (idézőjelek) között.

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    g. Válassza ki a **növekményes** üzembe helyezési mód.

    > [!WARNING]
    > Ha **Complete** üzembe helyezési mód, a meglévő erőforrások törlődhet, többek között az összes erőforrást, amelyek nincsenek meghatározva a Resource Manager-sablon a célként megadott erőforráscsoportban.

1.  Mentse a kiadási folyamathoz.

1.  Hozzon létre egy új kiadás az ebben a kiadásban folyamat.

    ![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="optional---get-the-secrets-from-azure-key-vault"></a>Nem kötelező – a titkos kulcsok lekérése az Azure Key vault

Ha rendelkezik egy Azure Resource Manager-sablonban adja át a titkos kulcsok, az Azure-folyamatok kiadással az Azure Key Vault használatát javasoljuk.

A titkos kulcsok kezeléséhez két módja van:

1.  Adja hozzá a titkos kulcsok paramétereket tartalmazó fájlt. További információ: [használata Azure Key Vaultban történő biztonságos paraméter értéke továbbítása üzembe helyezés során](../azure-resource-manager/resource-manager-keyvault-parameter.md).

    -   Másolatot készít a paramétereket tartalmazó fájlt, amely a közzététel ág töltenek fel, és állítsa az értékeket a paraméterek szeretné megkapni a key vaulttal és a következő formátumban:

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

    -   Ha ezt a módszert használja, a titkos kulcs lehívja a key vaultban tárolt automatikusan.

    -   A paraméterfájl kell lennie, valamint a közzététel ágban.

1.  Adjon hozzá egy [Azure Key Vault feladat](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) az előző szakaszban ismertetett Azure Resource Manager üzembe helyezése előtt:

    -   Válassza ki a **feladatok** lapon, hozzon létre egy új feladatot, keressen rá a **Azure Key Vault** , és adja hozzá.

    -   A Key Vault a feladatban az előfizetés, amelyben létrehozta a key vaultban, és adja meg hitelesítő adatait, ha szükséges, és válassza a key vaultban.

    ![](media/continuous-integration-deployment/continuous-integration-image8.png)

### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Engedélyek megadása az Azure-folyamatok ügynök
Az Azure Key Vault feladat sikertelen lehet a hozzáférés megtagadva hiba fIntegration modul időpontjának. A kiadás a naplók letöltéséhez, és keresse meg a `.ps1` fájlt a paranccsal az Azure-folyamatok ügynök engedélyt. Futtathatja a parancsot közvetlenül, vagy a résztvevő-azonosító átmásolhatja a fájlt, és manuálisan adja hozzá a hozzáférési szabályzat az Azure Portalon. (*Első* és *lista* rendszer szükséges minimális engedélyeket).

### <a name="update-active-triggers"></a>Aktív eseményindítók frissítése
Központi telepítés is sikertelen, ha aktív eseményindítók frissíti. Aktív eseményindítók frissítéséhez szüksége manuális állítják őket, és indítsa el őket az üzembe helyezés után. Erre a célra az Azure PowerShell-lel feladat adhat hozzá az alábbi példában látható módon:

1.  A kiadás a feladatok lapon keresse meg **Azure PowerShell-lel** , és adja hozzá.

1.  Válasszon **Azure Resource Manager** kapcsolatként írja be, és válassza ki az előfizetését.

1.  Válasszon **beágyazott parancsfájlja** szkriptet írja be, és adja meg a kódot. A következő példa leállítja az eseményindítók:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

Hasonló lépésekkel és a hasonló kóddal (az a `Start-AzDataFactoryV2Trigger` függvény) a telepítést követően újraindítható az eseményindítók.

> [!IMPORTANT]
> A folyamatos integrációt és üzembe helyezési forgatókönyvek ugyanaz a saját üzemeltetésű integrációs típusát különböző környezetek között kell lennie. Például, ha rendelkezik egy *saját üzemeltetésű* Integration Runtime (IR) a fejlesztési környezetben, ugyanezt az integrációs Modult típusúnak kell lennie *saját üzemeltetésű* más, például a tesztelési és éles környezetekben is. Hasonlóképpen, ha az integrációs modulok között több szakaszt, hogy az integrációs modulok, konfigurálnia *saját üzemeltetésű társított* összes környezetekben, például fejlesztési, tesztelési és éles környezetben.

## <a name="sample-deployment-template"></a>A mintasablon üzembe helyezés

Íme egy minta központi telepítési sablont, amelyet importálhat az Azure-folyamatokban.

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

## <a name="sample-script-to-stop-and-restart-triggers-and-clean-up"></a>Mintaparancsfájl állítsa le és indítsa újra az eseményindítók és karbantartása

Íme egy minta parancsfájlt eseményindítók telepítés előtt állítsa le, és ezt követően indítsa újra az eseményindítók. A parancsfájl használatával törölje az erőforrást, amelyek el lettek távolítva a kódot is tartalmaz. Telepítse az Azure PowerShell legújabb verzióját, lásd: [Azure PowerShell telepítése a Windows a Powershellgettel](https://docs.microsoft.com/powershell/azure/install-az-ps).

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $rootFolder,
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start Active triggers - After cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Egyéni paraméterek használata a Resource Manager-sablon

A GIT módban van, ha a sablon és a csoport tulajdonságok vannak paraméteres tulajdonságok beállítása a Resource Manager-sablon a felülbírálhatja az alapértelmezett tulajdonságokat. Előfordulhat, hogy szeretné felülbírálni az alapértelmezett paraméterezés sablon a következő használati helyzetekben:

* Automatizált CI/CD használ, és meg szeretné változtatni az egyes tulajdonságok Resource Manager üzembe helyezése során, de a tulajdonságok nem paraméterezett alapértelmezés szerint.
* A gyári olyan nagy méretű, hogy az alapértelmezett Resource Manager-sablon érvénytelen, mert több, mint a megengedett paraméterek (256).

Ezen feltételek mellett felülbírálhatja az alapértelmezett paraméterezés sablont, hozzon létre egy fájlt *arm-sablon-paraméterek-definition.json* a tárház gyökérmappájában. A fájl neve pontosan egyeznie kell. A Data Factory próbál bármelyik ágból, jelenleg az Azure Data Factory-portálon, nem csak az együttműködés ágból a fájl olvasásához. Létrehozhat vagy szerkessze a fájlt egy privát ágban, ahol tesztelheti a módosításokat a használatával a **exportálása ARM-sablon** a felhasználói felületen. A fájlt, majd egyesítheti az együttműködés ággal való. Ha nem fájl található, használja az alapértelmezett sablont.


### <a name="syntax-of-a-custom-parameters-file"></a>Egy egyéni paraméterfájl szintaxisa

Íme néhány a szabályokat, akkor használja, ha az egyéni paramétereket tartalmazó fájlt hoz létre. A fájl minden egyes entitástípus szakasz áll: eseményindító, folyamat, linkedservice, dataset, integrationruntime és így tovább.
* Adja meg az útvonal a kapcsolódó entitás típusa alapján.
* Ha beállította a tulajdonság neve "\*:" %, jelzésére, hogy szeretné-e az összes tulajdonság alapján (csak az első szintű, nem a rekurzív módon) le paraméterezni. Olyan kivételek, ez is megadhatja.
* A tulajdonság értékének beállításakor karakterláncként, jelzésére, hogy szeretné-e paraméterezni a tulajdonságot. Használja a következő formátumot `<action>:<name>:<stype>`.
   *  `<action>` a következő karakterek egyike lehet:
      * `=` azt jelenti, hogy a jelenlegi érték folyamatosan, a paraméter alapértelmezett értéke.
      * `-` azt jelenti, hogy ne legyenek a paraméter alapértelmezett értéke.
      * `|` van egy különleges esetben a kapcsolati karakterláncok vagy a kulcsok Azure Key vault titkos kulcsok.
   * `<name>` a paraméter neve van. Ha üres, a tulajdonság nevét vesz igénybe. Ha az érték kezdődik a `-` karakter, a neve van lerövidítettük. Ha például `AzureStorage1_properties_typeProperties_connectionString` lenne csonkolva `AzureStorage1_connectionString`.
   * `<stype>` a paraméter típusa. Ha `<stype>` van üres, az alapértelmezett típus az `string`. Támogatott értékek: `string`, `bool`, `number`, `object`, és `securestring`.
* Ha megad egy tömböt a szolgáltatásdefiníciós fájlban, jelzi, hogy a megfelelő tulajdonság a sablonban egy tömb. A Data Factory használatával a definíció az Integration Runtime-objektum a tömb a megadott végighalad a tömbben található összes objektumot. A második objektum egy karakterláncot, a tulajdonság, amely minden egyes ismétléskor szolgál a neveként a paraméter neve lesz.
* Nem alkalmas egy adott erőforrás-példány definíciója. Bármely definíció az adott típusú valamennyi erőforrására vonatkozik.
* Alapértelmezés szerint minden biztonságos karakterlánc, például a Key Vault titkos kódok és a biztonságos karakterláncot, például kapcsolati karakterláncokat, a kulcsok és a jogkivonatok, paraméteres vannak.
 
## <a name="sample-parameterization-template"></a>Paraméterezés mintasablon

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```

### <a name="explanation"></a>Leírás:

#### <a name="pipelines"></a>Folyamatok
    
* A tevékenységek/typeProperties/waitTimeInSeconds elérési út minden tulajdonság van paraméterezni. Ez azt jelenti, hogy minden tevékenység egy folyamatot, amely rendelkezik a kód szintű vlastnost s `waitTimeInSeconds` (például a `Wait` tevékenység) egy számot, egy alapértelmezett névvel rendelkező paraméteres van. De azt a Resource Manager-sablon nem rendelkezik alapértelmezett értékkel. Egy kötelező bemeneti csak a Resource Manager üzembe helyezése során.
* Hasonlóképpen, egy nevű `headers` (például egy `Web` tevékenység) típussal van paraméterezve `object` (JObject). Alapértelmezett érték, amely ugyanazt az értéket, mint a forrás gyári rendelkezik.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Csak a tulajdonságokat, és az összes tulajdonság, az elérési út alatt `typeProperties` vannak paraméteres megfelelő alapértelmezett értékükön. Például az aktuális séma, állapot nincsenek két tulajdonság alatt **IntegrationRuntimes** típusú tulajdonságok: `computeProperties` és `ssisProperties`. Mindkét tulajdonság típusa megfelelő alapértelmezett értékek és típusok (objektum) együtt jönnek létre.

#### <a name="triggers"></a>Eseményindítók

* A `typeProperties`, két tulajdonság paraméteres vannak. Az első egy `maxConcurrency`, amelyhez van megadva, alapértelmezett értékkel rendelkező és a típus lenne `string`. Az alapértelmezett paraméter neve van `<entityName>_properties_typeProperties_maxConcurrency`.
* A `recurrence` tulajdonság is van paraméterezni. Részre ezen a szinten az összes tulajdonság megadott lehet paraméterezni karakterláncként, az alapértelmezett értékeket, és a paraméterek nevei. Kivétel ez alól a `interval` tulajdonság, amely paraméteres szám típusú, és a paraméternév utótaggal `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Ehhez hasonlóan a `freq` tulajdonság egy karakterlánc, és karakterláncként van paraméteres. Azonban a `freq` tulajdonság alapértelmezett érték nélkül van paraméterezni. A név lerövidítettük, suffixed. Például: `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Társított szolgáltatásokat egy egyedülálló megoldás. Társított szolgáltatásokat és adatkészleteket lehet számos különböző, mert típusspecifikus testreszabási biztosíthat. Akkor előfordulhat, hogy tegyük fel például, hogy az összes társított szolgáltatás típusa `AzureDataLakeStore`, egy adott sablon lesz alkalmazott, és minden egyéb esetében (keresztül \*) egy másik sablon lépnek érvénybe.
* Az előző példában a `connectionString` tulajdonság lesz lehet paraméterezni, mint egy `securestring` érték, nem rendelkezik alapértelmezett értékkel, és a egy rövidített paraméternév van utótaggal fog rendelkezni `connectionString`.
* A tulajdonság `secretAccessKey`, azonban nem kell történik egy `AzureKeyVaultSecret` (például egy `AmazonS3` társított szolgáltatás). Így paraméterekkel, automatikusan a az Azure Key Vault titkos kulcsából, rendelkeznek, és azt a mavenből lekért a kulcstartóhoz, amely a forrás-előállító lesz konfigurálva. Akkor is is parametrizálja a key vault magát.

#### <a name="datasets"></a>Adathalmazok

* Annak ellenére, hogy a típus-specifikus testreszabási adatkészletek esetében érhető el, anélkül, hogy explicit módon kellene konfigurációs adható meg egy \*-szolgáltatásiszint-konfiguráció. Az előző példában az összes adatkészlet tulajdonságai alapján `typeProperties` paraméteres vannak.

Az alapértelmezett paraméterezés sablon módosíthatja, de ez az aktuális sablon. Ez akkor lehet hasznos, ha csupán hozzáadása egy további tulajdonságot paraméterként, de még ha nem szeretné a meglévő parameterizations elvesznek, és újból létre kell hozni.


```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

**Példa**: Adjon hozzá egy interaktív Databricks Fürtazonosító (a Databricks társított szolgáltatás), a paramétereket tartalmazó fájlt:

```
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```


## <a name="linked-resource-manager-templates"></a>A csatolt Resource Manager-sablonok

Ha beállította a folyamatos integráció és készregyártás (CI/CD) esetében az adat-előállítók, azt megfigyelheti, hogy nagyobb méretű az előállító növekedésével tapasztal a Resource Manager sablon korlátok, például a maximális számát erőforrásokhoz vagy az erőforrás a maximálisan megengedett adattartalom Manager-sablon. Például ezek együtt egy Factory teljes Resource Manager-sablon létrehozása a Data Factory is hoz létre a társított Resource Manager-sablonok. Ennek eredményeképpen rendelkezik az egész gyár tartalom oszthatók több fájlt, hogy azokat az említett korlátozások nem futtatható.

Ha van konfigurálva, a Git, a hivatkozott sablonok jönnek létre és menti a teljes Resource Manager-sablonok mellett a `adf_publish` ág nevű új mappa alatt `linkedTemplates`.

![Resource Manager-sablonok csatolt mappa](media/continuous-integration-deployment/linked-resource-manager-templates.png)

A csatolt Resource Manager-sablonok általában rendelkeznek egy fő sablont és a fő kapcsolódó gyermek sablonok számos. A fölérendelt sablon neve `ArmTemplate_master.json`, és a gyermek sablonok vannak elnevezve a minta `ArmTemplate_0.json`, `ArmTemplate_1.json`, és így tovább. A teljes, a kapcsolt sablonok használata a Resource Manager-sablon használatával történő átvitele a CI/CD feladat átirányítása frissítése `ArmTemplate_master.json` helyett mutató `ArmTemplateForFactory.json` (azaz a teljes Resource Manager-sablon). Resource Manager megköveteli, hogy a hivatkozott sablonok feltöltése a storage-fiókra, hogy azok elérhetők az Azure-ban üzembe helyezés során. További információ: [telepítése társított ARM-sablonok VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Ne felejtse el hozzáadni a Data Factory-parancsfájlok a CI/CD-folyamat, előtt és után a központi telepítési feladatot.

Ha nincs konfigurálva a Git, a hivatkozott sablonok keresztül érhető el a **exportálása ARM-sablon** kézmozdulat.

## <a name="best-practices-for-cicd"></a>Ajánlott eljárások a CI/CD

Ha a Git-integrációval együtt az adat-előállító használ, és a egy CI/CD folyamatot, amely a helyezi át a módosításokat a fejlesztési, tesztelési és éles rendelkezik, a következő gyakorlati tanácsok javasoljuk:

-   **Git-integrációval**. Csak a fejlesztése a data factory konfigurálása a Git-integrációval rendelkező szükségesek. Tesztelési és éles módosításai telepítve vannak a CI/CD-n keresztül, és nincs szükségük van a Git-integráció.

-   **Data Factory CI/CD parancsfájl**. A CI/CD a Resource Manager üzembe helyezési lépés előtt meg kell átadniuk, többek között az eseményindítók és a különböző típusú gyári karbantartása leállítása. Azt javasoljuk, [Ez a szkript](#sample-script-to-stop-and-restart-triggers-and-clean-up) , akkor ezeknek a dolgoknak gondoskodik. Futtassa a szkriptet, miután az üzembe helyezés előtt és után egyszer, használja a megfelelő jelzőkkel.

-   **Integrációs modulok és a megosztási**. Integrációs modulok tartoznak az adat-előállítóban az infrastrukturális összetevőket, amelyek kevésbé gyakran változásokon és hasonlók a CI/CD összes szakaszai között. Ennek eredményeképpen a Data Factory vár, hogy ugyanazt a nevet és ugyanolyan típusú integrációs modulok között a CI/CD valamennyi fázisában. Ha arra kíváncsi, integrációs modulok megosztása minden szakaszában – például a helyi integrációs modulok – megosztani egy úgy, hogy a helyi integrációs modul egy Ternáris előállítóban, és a megosztott integrációs modulok tartalmazó az üzemeltetés. Ezután használhatja azokat a fejlesztési/tesztelési vagy éles társított integrációs modul típusként.

-   **Key Vault**. Az ajánlott használatakor az Azure Key Vault-alapú társított szolgáltatásokat, a fejlesztési/tesztelési vagy éles külön kulcstartók potenciálisan tartja az előnyök egy szinttel további is igénybe vehet. Beállíthatja a külön jogosultsági szintek mindegyikük számára. Nem érdemes a termelési titkos kódokhoz való engedélyekkel kell rendelkeznie a munkacsoportja többi tagjával. Azt javasoljuk, hogy az azonos titkos kód nevét keresztül valamennyi tartsa. Ha ugyanazokat a neveket, nem kell módosul a Resource Manager-sablonok a CI/CD, mivel az, hogy módosítani kell a kulcstartó nevét, amely az egyik a Resource Manager-sablon paraméterei.

## <a name="unsupported-features"></a>Nem támogatott szolgáltatások

-   Az egyes erőforrások nem tehető közzé, mert a data factory-entitások egymástól függenek. Eseményindítók folyamatok függnek, a folyamatok függnek az adatkészletek és más adatcsatornákat stb. Változó függőségek követése nem könnyű feladat. Ha sikerült válassza ki az erőforrásokat manuálisan közzétenni, válassza ki a teljes készletet módosításait, amelyeket közzététel után nem várt viselkedést dolgot vezetne csak egy részhalmazát is lenne.

-   Nem tehet közzé a saját ágak.

-   A bitbucket-alapú projektek nem tárolható.
