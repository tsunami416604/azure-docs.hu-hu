---
title: Terjessze ki a riasztások a napló Analytcs Azure
description: Ez a cikk ismerteti az eszközök és API, amellyel kiterjesztheti riasztások a Log Analyticshez Azure riasztásokat.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 21ba95a7b3efff177afe63d22da3f6ba9848ded2
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35301031"
---
# <a name="extend-alerts-from-log-analytics-into-azure-alerts"></a>A Naplóelemzési riasztások kiterjeszti Azure riasztások
Azure Naplóelemzés riasztások funkcióját a felváltja a Azure riasztásokat. Ez a változás részeként az eredetileg szolgáltatáshoz konfigurált riasztások az Azure kell terjeszteni. Ha nem szeretné Várjon, amíg az Azure automatikusan áthelyezni, a folyamat is kezdeményezhető:

- Az Operations Management Suite portálról manuálisan. 
- Programozott módon a AlertsVersion API használatával.  

> [!NOTE]
> Microsoft automatikusan kiterjed Naplóelemzési Azure riasztás létre riasztásokat, kezdve a 2018. május 14., ismétlődő több befejeződéséig. A riasztások áttelepítése az Azure-ba, és ez a változás során Microsoft ütemezéseket, riasztások kezelheti mind az Operations Management Suite portálját, és az Azure-portálon. Ez a folyamat nincs ilyen beállítások mellett pusztító vagy interruptive.  

## <a name="option-1-initiate-from-the-operations-management-suite-portal"></a>1. lehetőség: Indítása az Operations Management Suite portálról
A következő lépések írják le a munkaterület riasztásokat az Operations Management Suite portálról bővítése.  

1. Válassza ki az Azure-portálon **minden szolgáltatás**. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
2. A Naplóelemzési előfizetések ablaktáblán válassza ki a munkaterületen, és válassza a **OMS-portálon** csempére.
![Naplóelemzési képernyőkép előfizetés ablaktábla a kijelölt OMS-portálon csempe](./media/monitor-alerts-extend/azure-portal-01.png) 
3. Után a rendszer átirányítja az Operations Management Suite portálját, válassza ki a **beállítások** ikonra.
![Képernyőkép az Operations Management Suite portálját, a beállítások ikonja kiemelve](./media/monitor-alerts-extend/oms-portal-settings-option.png) 
4. Az a **beállítások** lapon jelölje be **riasztások**.  
5. Válassza ki **Azure kiterjeszti**.
![Képernyőkép az Operations Management Suite portál riasztási beállítások lapon, az a kijelölt Azure bővítése](./media/monitor-alerts-extend/ExtendInto.png)
6. Megjelenik egy három lépéses varázsló, a **riasztások** ablaktáblán. Olvassa el a áttekintése, és válassza ki **következő**.
![1. lépés a varázsló képernyőképe](./media/monitor-alerts-extend/ExtendStep1.png)  
7. A második lépésben látja javasolt változások összefoglalása listázása megfelelő [művelet csoportok](monitoring-action-groups.md) a riasztásokhoz. Ha hasonló műveletek közötti egynél több riasztást, a varázsló egy egyetlen művelettel csoporthoz hozzárendelni az összes javasol.  Az elnevezési a következőképpen történik: *WorkspaceName_AG_ #Number*. A folytatáshoz válasszon **következő**.
![Képernyőkép a 2. lépés a varázsló](./media/monitor-alerts-extend/ExtendStep2.png)  
8. A varázsló utolsó lépése, jelölje ki **Befejezés**, és erősítse meg, amikor a rendszer kéri a megkezdéséhez. Egy e-mail címet, opcionálisan megadhatja, hogy értesítést kap a folyamat befejeződik, és az összes riasztás sikeresen helyezte Azure riasztások.
![Képernyőkép a 3. lépés a varázsló](./media/monitor-alerts-extend/ExtendStep3.png)

Amikor a varázsló nem végzett, a **riasztási beállítások** lap, terjessze ki a riasztások Azure is törlődik. A háttérben a riasztások Azure kerülnek, és ez eltarthat egy ideig. A művelet során nem módosíthatja a riasztásokat az Operations Management Suite-portálról. Az aktuális állapotát a portál felső fejléc tekintheti meg. Ha korábban megadott e-mail cím, kap egy e-mailt során a folyamat sikeresen befejeződött.  


Riasztások továbbra is jelenik meg az Operations Management Suite portálját, az Azure sikeresen áthelyezett után is.
![Képernyőkép az Operations Management Suite portál riasztási beállítások lap](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2-use-the-alertsversion-api"></a>2. lehetőség: A AlertsVersion API használata
A napló Analytics AlertsVersion API segítségével terjessze ki a riasztások a Naplóelemzési be Azure riasztások minden ügyfélről, amely a REST API meghívása. Elérhető az API-t PowerShell használatával [ARMClient](https://github.com/projectkudu/ARMClient), egy nyílt forráskódú parancssori eszközt. Az eredményeket a JSON-ban.  

Az API használatához először létrehozhat egy GET kérelmet. Ez kiértékeli, és a javasolt változtatásokat összegzését adja vissza, ténylegesen kiterjesztése az Azure használatával egy POST kérést megkísérlése előtt. Az eredmények listát a riasztásokat és a javasolt listája [művelet csoportok](monitoring-action-groups.md), JSON formátumban. Ha hasonló műveletek közötti egynél több riasztást, a szolgáltatás az összes olyan egyetlen művelettel csoporthoz társítandó javasolja. Az elnevezési a következőképpen történik: *WorkspaceName_AG_ #Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Ha a GET kérés sikeres, 200-as HTTP-állapotkódot adott vissza, a riasztások listája mellett, és javasolt művelet csoportok a JSON-adatokat. A következő egy példa egy válasz:

```json
{
    "version": 1,
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "http://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}

```
Ha a megadott munkaterület nem rendelkezik a riasztási szabály lett meghatározva, a JSON-adatokat a következő adja vissza:

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Ha a megadott munkaterület minden riasztási szabályok már fut az Azure-ba, a GET kérelemre adott válasz van:

```json
{
    "version": 2
}
```

Kezdeményezheti a riasztások áttelepítése az Azure-ba, indítsa el a POST válasz. A POST válasz megerősíti, hogy a leképezés, valamint elfogadási, Log Analyticshez terjeszteni Azure riasztások riasztások rendelkeznie. A tevékenység van ütemezve, és a riasztások jelöli, az eredmények alapján, ha korábban hajtotta végre a GET-válasz feldolgozása. Igény szerint is adja meg e-mail címét, amelyhez a Naplóelemzési jelentést küld, amikor ütemezett folyamata az áttelepítéshez a riasztások sikeresen befejeződik. Ez a példa kérelem használhatja:

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Eredményét áttelepítése értesítések az Azure-riasztások a GET-válasz által átadott függően változhatnak. Ütemezése, a Naplóelemzési riasztások az éppen átmenetileg nem érhető el az Operations Management Suite portálját módosításra. Azonban létrehozhat új riasztásokat. 

Ha a POST kérelem sikeres, a következő választ egy HTTP 200 OK állapotára adja vissza:

```json
{
    "version": 2
}
```

Ez a válasz azt jelzi, hogy a riasztásokat az Azure-riasztások sikeresen bővültek. A version tulajdonságát a rendszer csak ellenőrizni, hogy a riasztások kiterjesztettük, az Azure-ba, és nincsenek kapcsolatban a [napló Analytics Search API](../log-analytics/log-analytics-api-alerts.md). A riasztások bővítve lettek az Azure-bA sikeresen, ha bármelyik e-mail címek megadott a POST kérelem vannak a rendszer küld jelentést. A megadott munkaterület minden riasztás már ütemezve kell terjeszteni, a POST-kérelmet válasz esetén, hogy a kísérlet (a 403-as állapotkód) történt tiltott. Hibaüzenetek megtekintéséhez, vagy ha akadt-e a folyamat megértése, elküldheti a GET kérés. Ha hibaüzenet jelenik meg, akkor lett visszaadva, akkor az összefoglaló információk mellett.

```json
{
    "version": 1,
    "message": "OMS was unable to extend your alerts into Azure, Error: The subscription is not registered to use the namespace 'microsoft.insights'. OMS will schedule extending your alerts, once remediation steps illustrated in the troubleshooting guide are done.",
    "recipients": [
       "john.doe@email.com",
       "jane.doe@email.com"
     ],
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "http://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}              

```


## <a name="option-3-use-a-custom-powershell-script"></a>3. lehetőség: Egy egyéni PowerShell-parancsfájl használata
 Ha a Microsoft nem sikeresen kibővítve a riasztásokat az Operations Management Suite-portálon az Azure-ba, megteheti ezt manuálisan amíg 2018 július 5. A két lehetőség manuális bővítmény az előző két szakaszok ismertetnek.

2018. július 5., miután minden riasztásait az Operations Management Suite portálját kiterjeszthetőek Azure. Felhasználók, akik nem igénybe vehet a [javasolt szükséges javítási lépéseket](#troubleshooting) műveletek vagy értesítések, mert nem tartozik kiváltó nélkül futtatja a figyelmeztetéseket fog tartozó [művelet csoportok](monitoring-action-groups.md). 

Létrehozásához [művelet csoportok](monitoring-action-groups.md) a Naplóelemzési manuálisan riasztásokhoz, használja az alábbi mintaparancsfájl:
```PowerShell
########## Input Parameters Begin ###########


$subscriptionId = ""
$resourceGroup = ""
$workspaceName = "" 


########## Input Parameters End ###########

armclient login

try
{
    $workspace = armclient get /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/"$workspaceName"?api-version=2015-03-20 | ConvertFrom-Json
    $workspaceId = $workspace.properties.customerId
    $resourceLocation = $workspace.location
}
catch
{
    "Please enter valid input parameters i.e. Subscription Id, Resource Group and Workspace Name !!"
    exit
}

# Get Extend Summary of the Alerts
"`nGetting Extend Summary of Alerts for the workspace...`n"
try
{

    $value = armclient get /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/alertsversion?api-version=2017-04-26-preview

    "Extend preview summary"
    "=========================`n"

    $value

    $result = $value | ConvertFrom-Json
}
catch
{

    $ErrorMessage = $_.Exception.Message
    "Error occured while fetching/parsing Extend summary: $ErrorMessage"
    exit 
}

if ($result.version -eq 2)
{
    "`nThe alerts in this workspace have already been extended to Azure."
    exit
}

$in = Read-Host -Prompt "`nDo you want to continue extending the alerts to Azure? (Y/N)"

if ($in.ToLower() -ne "y")
{
    exit
} 


# Check for resource provider registration
try
{
    $val = armclient get subscriptions/$subscriptionId/providers/microsoft.insights/?api-version=2017-05-10 | ConvertFrom-Json
    if ($val.registrationState -eq "NotRegistered")
    {
        $val = armclient post subscriptions/$subscriptionId/providers/microsoft.insights/register/?api-version=2017-05-10
    }
}
catch
{
    "`nThe user does not have required access to register the resource provider. Please try with user having Contributor/Owner role in the subscription"
    exit
}

$actionGroupsMap = @{}
try
{
    "`nCreating new action groups for alerts extension...`n"
    foreach ($actionGroup in $result.migrationSummary.actionGroups)
    {
        $actionGroupName = $actionGroup.actionGroupName
        $actions = $actionGroup.actions
        if ($actionGroupsMap.ContainsKey($actionGroupName))
        {
            continue
        } 
        
        # Create action group payload
        $shortName = $actionGroupName.Substring($actionGroupName.LastIndexOf("AG_"))
        $properties = @{"groupShortName"= $shortName; "enabled" = $true}
        $emailReceivers = New-Object Object[] $actions.emailIds.Count
        $webhookReceivers = New-Object Object[] $actions.webhookActions.Count
        
        $count = 0
        foreach ($email in $actions.emailIds)
        {
            $emailReceivers[$count] = @{"name" = "Email$($count+1)"; "emailAddress" = "$email"}
            $count++
        }

        $count = 0
        foreach ($webhook in $actions.webhookActions)
        {
            $webhookReceivers[$count] = @{"name" = "$($webhook.name)"; "serviceUri" = "$($webhook.serviceUri)"}
            $count++
        }

        $itsmAction = $actions.itsmAction
        if ($itsmAction.connectionId -ne $null)
        {
            $val = @{
            "name" = "ITSM"
            "workspaceId" = "$subscriptionId|$workspaceId"
            "connectionId" = "$($itsmAction.connectionId)"
            "ticketConfiguration" = $itsmAction.templateInfo
            "region" = "$resourceLocation"
            }
            $properties["itsmReceivers"] = @($val)  
        }

        $properties["emailReceivers"] = @($emailReceivers)
        $properties["webhookReceivers"] = @($webhookReceivers)
        $armPayload = @{"properties" = $properties; "location" = "Global"} | ConvertTo-Json -Compress -Depth 4

    
        # Azure Resource Manager call to create action group
        $response = $armPayload | armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroupName/?api-version=2017-04-01

        "Created Action Group with name $actionGroupName" 
        $actionGroupsMap[$actionGroupName] = $actionGroup.actionGroupResourceId.ToLower()
        $index++
    }

    "`nSuccessfully created all action groups!!"
}
catch
{
    $ErrorMessage = $_.Exception.Message

    #Delete all action groups in case of failure
    "`nDeleting newly created action groups if any as some error happened..."
    
    foreach ($actionGroup in $actionGroupsMap.Keys)
    {
        $response = armclient delete /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroup/?api-version=2017-04-01      
    }

    "`nError: $ErrorMessage"
    "`nExiting..."
    exit
}

# Update all alerts configuration to the new version
"`nExtending OMS alerts to Azure...`n"

try
{
    $index = 1
    foreach ($alert in $result.migrationSummary.alerts)
    {
        $uri = $alert.alertId + "?api-version=2015-03-20"
        $config = armclient get $uri | ConvertFrom-Json
        $aznsNotification = @{
            "GroupIds" = @($actionGroupsMap[$alert.actionGroupName])
        }
        if ($alert.customWebhookPayload)
        {
            $aznsNotification.Add("CustomWebhookPayload", $alert.customWebhookPayload)
        }
        if ($alert.customEmailSubject)
        {
            $aznsNotification.Add("CustomEmailSubject", $alert.customEmailSubject)
        }      

        # Update alert version
        $config.properties.Version = 2

        $config.properties | Add-Member -MemberType NoteProperty -Name "AzNsNotification" -Value $aznsNotification
        $payload = $config | ConvertTo-Json -Depth 4
        $response = $payload | armclient put $uri
    
        "Extended alert with name $($alert.alertName)"
        $index++
    }
}
catch
{
    $ErrorMessage = $_.Exception.Message   
    if ($index -eq 1)
    {
        "`nDeleting all newly created action groups as no alerts got extended..."
        foreach ($actionGroup in $actionGroupsMap.Keys)
        {
            $response = armclient delete /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroup/?api-version=2017-04-01      
        }
        "`nDeleted all action groups."  
    }
    
    "`nError: $ErrorMessage"
    "`nPlease resolve the issue and try extending again!!"
    "`nExiting..."
    exit
}

"`nSuccessfully extended all OMS alerts to Azure!!" 

# Update version of workspace to indicate extension
"`nUpdating alert version information in OMS workspace..." 

$response = armclient post "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/alertsversion?api-version=2017-04-26-preview&iversion=2"

"`nExtension complete!!"
```


### <a name="about-the-custom-powershell-script"></a>Tudnivalók az egyéni PowerShell-parancsfájl 
A következő egy a parancsfájl használatával kapcsolatos fontos információkat:
- Egy előfeltétel, telepítésének [ARMclient](https://github.com/projectkudu/ARMClient), egy nyílt forráskódú parancssori eszköz, amely leegyszerűsíti az Azure Resource Manager API meghívása.
- A parancsfájl futtatásához egy közreműködő vagy a tulajdonos szerepkörrel kell rendelkeznie az Azure-előfizetésben.
- A következő paramétereket kell megadnia:
    - $subscriptionId: az Azure-előfizetési Azonosítót az Operations Management Suite Naplóelemzési munkaterület társított.
    - $resourceGroup: Operations Management Suite Naplóelemzési munkaterület az Azure-erőforráscsoportot.
    - $workspaceName: az Operations Management Suite Naplóelemzési munkaterület nevét.

### <a name="output-of-the-custom-powershell-script"></a>Az egyéni PowerShell-parancsfájl
A parancsfájl részletes, és kiírja a lépéseket, hogy: 
- Megjeleníti a összegzésbe, amelyet a meglévő Operations Management Suite Naplóelemzési riasztások munkaterületén adatait tartalmazza. Az összegzés a hozzájuk társított műveletek létrehozandó Azure művelet csoportok adatait is tartalmazza. 
- A bővítmény használatától, vagy lépjen ki az összegzés megtekintése után kéri.
- Ha előre kiterjesztésű, új Azure művelet csoportok jönnek létre, és minden meglévő riasztás társított őket. 
- A parancsfájl lép ki megjeleníti az üzenet "Bővítmény teljes!" Összes köztes hiba esetén a parancsfájl további hibákat jeleníti meg.

## <a name="troubleshooting"></a>Hibaelhárítás 
Riasztások kiterjesztésének során problémák így előfordulhat, hogy a rendszer az ehhez szükséges [művelet csoportok](monitoring-action-groups.md). Ebben az esetben, a fejléc a hibaüzenetet látja a **riasztási** szakasz az Operations Management Suite portálját, vagy a GET hívja az API kész.

> [!IMPORTANT]
> Ha nem a következő javítási lépéseket 2018. július 5., mielőtt riasztások az Azure-ban működik, de nem lép működésbe, semmilyen művelet vagy az értesítés. Ahhoz, hogy a riasztások értesítései, manuálisan módosítsa, majd adja hozzá [művelet csoportok](monitoring-action-groups.md), vagy használja az előző [egyéni PowerShell-parancsfájl](#option-3---using-custom-powershell-script).

Minden egyes hibához szervizelési lépései a következők:
- **Hiba: Hatókör zárolási szerepel előfizetés-erőforráscsoport szintjén az írási műveletek**: ![oldalát bemutató Képernyőkép az Operations Management Suite portál riasztási beállításokat, a kijelölt hatókör zárolási hibaüzenet](./media/monitor-alerts-extend/ErrorScopeLock.png)

    Ha hatókör zár engedélyezve van, a szolgáltatás korlátozza az előfizetés vagy az erőforrás csoport, amely tartalmazza a (Operations Management Suite) Naplóelemzési munkaterület új változást. A rendszer nem tud riasztások kiterjeszti az Azure és a szükséges intézkedéseket csoportok létrehozása.
    
    Megoldásához törölje a *ReadOnly* zárolni az előfizetés vagy az erőforrás csoport, amely tartalmazza a munkaterületen. Ehhez az Azure-portálon, PowerShell, az Azure parancssori felület vagy a API használatával. További tudnivalókért lásd: [erőforrás zárolási kihasználtsága](../azure-resource-manager/resource-group-lock-resources.md). 
    
    A cikkben ismertetett lépések segítségével hárítsa el a hibát, ha az Operations Management Suite az Azure a riasztások bővíti a következő napra ütemezett futtatását. Nem kell további műveleteket, vagy bármi indítása.

- **Hiba: A előfizetés-erőforráscsoport szintjén szerepel házirend**: ![oldalát bemutató Képernyőkép az Operations Management Suite portál riasztási beállításokat, a kijelölt házirend hibaüzenet](./media/monitor-alerts-extend/ErrorPolicy.png)

    Ha [Azure házirend](../azure-policy/azure-policy-introduction.md) van érvényben, korlátozza a (Operations Management Suite) Naplóelemzési munkaterület tartalmazó előfizetés vagy az erőforrás csoport bármely új erőforrás. A rendszer nem tud riasztások kiterjeszti az Azure és a szükséges intézkedéseket csoportok létrehozása.
    
    Megoldásához szerkeszti a házirendet, amely miatt a *[RequestDisallowedByPolicy](../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* hiba, amely megakadályozza az új erőforrások az előfizetés vagy az erőforrás csoport, amely tartalmazza a munkaterület létrehozását. Ehhez az Azure-portálon, PowerShell, az Azure parancssori felület vagy a API használatával. A megfelelő házirend hibát okozó található műveletek naplózhatók. További tudnivalókért lásd: [műveletek naplózása tevékenység naplók megtekintése](../azure-resource-manager/resource-group-audit.md). 
    
    A cikkben ismertetett lépések segítségével hárítsa el a hibát, ha az Operations Management Suite az Azure a riasztások bővíti a következő napra ütemezett futtatását. Nem kell további műveleteket, vagy bármi indítása.


## <a name="next-steps"></a>További lépések

* További információ az új [Azure riasztások élmény](monitoring-overview-unified-alerts.md).
* További tudnivalók [riasztások jelentkezzen be Azure riasztások](monitor-alerts-unified-log.md).
