---
title: Hogyan terjessze ki a riasztások a napló Analytcs Azure |} Microsoft Docs
description: Ez a cikk ismerteti az eszközök és API, amellyel kiterjesztheti riasztások a Log Analyticshez Azure riasztásokat.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2018
ms.author: vinagara
ms.openlocfilehash: 0dce6e6772b4efea90df2e095ac0041641d99061
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763527"
---
# <a name="how-to-extend-alerts-from-log-analytics-into-azure-alerts"></a>Hogyan Naplóelemzési riasztások kiterjeszti Azure riasztások
Naplóelemzési riasztások lecserélni Azure riasztásaival, és ez a változás részeként, a szolgáltatáshoz konfigurált riasztások fogja az Azure.  Ha nem szeretné Várjon, amíg az Azure automatikusan áthelyezni, is kezdeményezhető a folyamat a következő beállítások egyikét:

1. Manuálisan a az OMS-portálon 
2. Programozott módon, a AlertsVersion API  

> [!NOTE]
> A Microsoft automatikusan kiterjed Azure riasztás indítása a Naplóelemzési létrehozott riasztások **2018. május 14.** szakaszos megközelítéssel befejeződéséig. Az ebben az nap ütemezése, a riasztások áttelepítése az Azure-bA Microsoft megkezdődik, és során ez a változás az OMS-portálon és az Azure-portálon kezelhetők a riasztásokat. Ez a folyamat visszafordítható és nem interruptive.  

## <a name="option-1---initiate-from-the-oms-portal"></a>1 - beállítást az OMS-portálon kezdeményezése
A következő lépések írják le a munkaterület riasztások bővítése az OMS-portálon.  

1. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
2. A Naplóelemzési előfizetések ablaktáblán válassza ki a munkaterület, és válassza ki a **OMS-portálon** csempére.<br><br> ![Naplóbeli keresés gomb](./media/monitor-alerts-extend/azure-portal-01.png)<br><br> 
3. Után a rendszer átirányítja az OMS-portálon, kattintson a lap jobb felső oldalán beállítások csempére.<br><br> ![Beállítás OMS-portálon](./media/monitor-alerts-extend/oms-portal-settings-option.png)<br><br> 
4. Az a **beállítások** lapon jelölje be **riasztások**.  
5. Kattintson a gombra **bővítése az Azure**.<br><br> ![A riasztási beállítások portállapon OMS bővítése beállítással](./media/monitor-alerts-extend/ExtendInto.png)
6. A varázsló a panelen számára jelenik meg az első és áttekintést nyújt azokról a folyamat három lépésből áll.  Kattintson a **tovább** a folytatáshoz.<br><br> ![Terjessze ki a riasztások a Log Analyticshez Azure - 1. lépés](./media/monitor-alerts-extend/ExtendStep1.png)  
7. A második lépésben javasolt változások összefoglalása számára jelenik meg a megfelelő listázása [művelet csoportok](monitoring-action-groups.md) a riasztásokhoz. Ha hasonló műveletek közötti egynél több riasztást, a szolgáltatás javasolni fogja hozzárendelni az összes csoport egyetlen műveleti.  A javasolt művelet csoport hajtsa végre az elnevezési: *WorkspaceName_AG_ #Number*. A folytatáshoz kattintson **következő**.<br><br> ![terjessze ki a riasztások a Log Analyticshez Azure - 2. lépés](./media/monitor-alerts-extend/ExtendStep2.png)  
8. A varázsló utolsó lépésként kattintson **Befejezés** , majd erősítse meg, amikor a rendszer kéri a megkezdéséhez.  Opcionálisan megadhatja egy e-mail címet, hogy értesítést kap a folyamat befejeződik, és az összes riasztás sikeresen helyezte Azure riasztásokat.<br><br> ![Terjessze ki a riasztások a Log Analyticshez Azure - 3. lépés](./media/monitor-alerts-extend/ExtendStep3.png)

A varázsló befejezése után nézze meg a **riasztási beállítások** oldal, amely kiterjesztése lehetőséget az Azure-bA riasztást küld a rendszer eltávolítja.  A háttérben a riasztások Azure kerülnek, és ez eltarthat egy ideig.  A művelet során csak akkor tud majd módosításokat végezni a riasztásokat az OMS-portálon.  A jelenlegi állapot jelenik meg a portál felső fejléc, és ha egy e-mail címet a korábban megadott kap egy e-mailt, ha a folyamat sikeresen befejeződött.  


Riasztások továbbra is az Azure sikeresen áthelyezett után is az OMS-portálon jelenik.<br><br> ![Helyezze át a riasztások a Naplóelemzési az Azure-bA](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2---using-the-alertsversion-api"></a>2 – a AlertsVersion API használatával lehetőséget
A napló Analytics AlertsVersion API segítségével terjessze ki a riasztások a Naplóelemzési be Azure riasztások minden ügyfélről, amely a REST API meghívása. A PowerShell használatával végezheti el [ARMClient](https://github.com/projectkudu/ARMClient), egy nyílt forráskódú parancssori eszköz, amely leegyszerűsíti az Azure Resource Manager API meghívása. A ARMClient és a PowerShell használata közül sok az API eléréséhez.  Az API-val lesz az eredményeket a JSON-ban.  

Az API használatához először létrehozhat egy GET kérelmet, amely értékelje ki és összegzést biztosít azokról a változtatási ténylegesen kiterjeszti az Azure-ban egy POST kérést megkísérlése előtt. Az eredmények listát a riasztások és a javasolt listája [művelet csoportok](monitoring-action-groups.md) JSON formátumban.  Ha hasonló műveletek közötti egynél több riasztást, a szolgáltatás javasolni fogja az összes társít egy egyetlen művelettel csoport.  Javasolt művelet csoportok kövesse az elnevezési: *WorkspaceName_AG_ #Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Ha a GET kérés sikeres, 200-as HTTP-állapotkódot együtt a riasztások és a JSON-adatokat a javasolt művelet csoportok listáját adja vissza. A következő egy példa egy válasz:

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
Ha a megadott munkaterület nem rendelkezik a riasztási szabály lett meghatározva, HTTP 200 OK állapotával együtt a GET művelethez a JSON-adatok kódot adja vissza:

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Ha a megadott munkaterület minden riasztási szabályok már fut az Azure - GET kérelemre adott válasz a következő:

```json
{
    "version": 2
}
```

Kezdeményezheti a riasztások áttelepítése az Azure-ba, indítsa el a POST válasz. A POST válasz megerősíti, hogy a leképezés, valamint elfogadási Naplóelemzési terjeszteni Auzre riasztások riasztások rendelkeznie.  A tevékenység van ütemezve, és a riasztások dolgoznak fel az eredmények alapján jelöli, a GET-válasz korábbi végrehajtása közben.  Igény szerint is adja meg e-mail címét, amelyhez Naplóelemzési lesz az e-mail jelentés ütemezett folyamata az áttelepítéshez a riasztások sikeres befejezéséről.  Ez a következő kérés példa segítségével hajtható végre:

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Eredményt az Azure-riasztások áttelepítése riasztások GET válaszban által átadott függően eltérhetnek.  Ütemezett, Log Analyticshez riasztások állnak átmenetileg nem érhető el, a Szerkesztés vagy módosítása az OMS-portálon.  Azonban új riasztások hozhatók létre. 

Ha a POST kérelem sikeres, együtt a következő választ egy HTTP 200 OK állapotot adja vissza:

```json
{
    "version": 2
}
```

Ez a válasz azt jelzi, hogy a riasztásokat az Azure-riasztások sikeresen bővültek. A version tulajdonságát a rendszer csak ellenőrizni, hogy a riasztások kiterjesztettük, az Azure-ba, és nincsenek kapcsolatban a [napló Analytics Search API](../log-analytics/log-analytics-api-alerts.md). A riasztások bővítve lettek az Azure-bA sikeresen, miután minden e-mail-címet megadott a POST kérelem vannak a rendszer küld egy jelentés ezzel a változtatások végrehajtani részleteit.  A riasztásokat a megadott munkaterület mindegyikét már ütemezve kell terjeszteni, a POST-kérelmet válasz esetén a 403-as állapot kód jelentés visszautasította a kísérletet. Hibaüzenetek megtekintéséhez vagy megérteni, ha a folyamat Beragadt, elküldheti a GET kérés és egy hibaüzenet, ha bármely, az eredmény az összefoglaló információk mellett.

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


## <a name="option-3---using-custom-powershell-script"></a>A beállítás 3 - egyéni PowerShell-parancsfájl használatával
 Miután május 14, 2018 - Ha a Microsoft nem sikeresen kibővítve a riasztásokat az OMS-portálon az Azure-bA; majd amíg **2018 július 5** -felhasználó is képes manuálisan ugyanerre keresztül [1. lehetőség – keresztül grafikus](#option-1---initiate-from-the-oms-portal) vagy [2. lehetőség – keresztül API](#option-2---using-the-alertsversion-api).

Után **2018 július 5** -minden riasztás az OMS-portálon az Azure lesz terjesztve. Felhasználók, akik nem igénybe vehet a [javasolt szükséges javítási lépéseket](#troubleshooting), a riasztások kiváltó műveletek vagy hiánya miatt értesítések nélkül futó lesz társítva [művelet csoportok](monitoring-action-groups.md). 

Kézi létrehozása [művelet csoportok](monitoring-action-groups.md) a Naplóelemzési riasztásokhoz, a felhasználók használhatják az alábbi.
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

    
        # ARM call to create action group
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


**Az egyéni PowerShell-parancsfájl használatával** 
- Előfeltétel telepítése [ARMclient](https://github.com/projectkudu/ARMClient), egy nyílt forráskódú parancssori eszköz, amely leegyszerűsíti az Azure Resource Manager API meghívása
- Az említett parancsfájlt futtató felhasználó közreműködő vagy a tulajdonos szerepkörrel kell rendelkeznie az Azure-előfizetés
- A parancsfájl meg kell adni a paraméterek a következők:
    - $subscriptionId: az Azure-előfizetési Azonosítót társított az OMS/LA munkaterület
    - $resourceGroup: az Azure erőforráscsoport hol helyezkedik el a OMS/LA munkaterület
    - $workspaceName: az OMS/LA munkaterület nevét

**Az egyéni PowerShell-parancsfájl** a parancsfájl részletes és fog kimeneti a lépéseket, akkor hajt végre. 
- Megjeleníti azt a összegzésbe, amelyet a meglévő OMS/LA riasztásokat a munkaterületet, és a hozzájuk társított műveletekhez létrehozandó Azure művelet csoportok adatait tartalmazza. 
- A bővítmény használatától és a kilépési az összegzés megtekintése után felhasználó kéri.
- Ha a felhasználó kéri annak használatától kiterjesztésű, új Azure művelet csoportok jön létre, és fel őket hozzárendelni a meglévő riasztást. 
- A végén, a parancsfájl kilép, az üzenet megjelenítésével "végezze el a kiterjesztés!." Összes köztes hiba esetén további hibákat jelenik meg.

## <a name="troubleshooting"></a>Hibaelhárítás 
A során, amely a riasztásokat az OMS Szolgáltatáshoz az Azure lehet alkalmi hibát, amely megakadályozza, hogy a rendszer létrehozásához szükséges [művelet csoportok](monitoring-action-groups.md). Ilyen esetekben egy hibaüzenet jelenik meg az OMS-portálon keresztül szalagcím figyelmeztetési és a GET hívást API történik.

> [!WARNING]
> Ha a felhasználó nem veszi a precribed javítási lépéseket alább, mielőtt **2018 július 5** - majd riasztásokat fog futni az Azure-ban, de kiváltó bármely művelet, vagy az értesítés nélkül. Ahhoz, hogy a riasztások értesítései, felhasználók manuálisan módosítsa, majd adja hozzá [művelet csoportok](monitoring-action-groups.md) , vagy használja a [egyéni PowerShell-parancsfájl](#option-3---using-custom-powershell-script) fenti.

Az alábbiakban a javítási lépéseket minden egyes hibához:
1. **Hiba: Hatókör zárolási szerepel előfizetés-erőforráscsoport szintjén az írási műveletek**: ![OMS portál riasztási beállítások lapján ScopeLock hibaüzenet](./media/monitor-alerts-extend/ErrorScopeLock.png)

    a. Ha hatókör zárolása engedélyezett, előfizetéshez vagy erőforráscsoporthoz Naplóelemzés (OMS) munkaterületet; tartalmazó új változást korlátozása a rendszer nem tud (Másolás) riasztások kiterjeszti az Azure és a szükséges intézkedéseket csoportok létrehozása.
    
    b. Megoldásához törölje a *ReadOnly* az előfizetés vagy az erőforrás-részleg a munkaterületet tartalmazó; az Azure-portálon, Powershell, az Azure parancssori felület vagy API zárolását. További tudnivalókért tekintse meg a cikk a [erőforrás zárolási kihasználtsága](../azure-resource-manager/resource-group-lock-resources.md). 
    
    c. Miután feloldotta a cikkben ismertetett lépések szerint, OMS kiterjed a riasztásokat az Azure belül a következő napra ütemezett futtatását; nincs szükség semmilyen művelet vagy kezdeményezés.

2. **Hiba: A előfizetés-erőforráscsoport szintjén szerepel házirend**: ![OMS portál riasztási beállítások lapján házirend hibaüzenet](./media/monitor-alerts-extend/ErrorPolicy.png)

    a. Ha [Azure házirend](../azure-policy/azure-policy-introduction.md) van érvényben, bármely új erőforrás előfizetéshez vagy erőforráscsoporthoz Naplóelemzés (OMS) munkaterületet; tartalmazó korlátozása a rendszer nem tudja (Másolás) riasztások kiterjeszti az Azure és a szükséges intézkedéseket csoportok létrehozása.
    
    b. Megoldásához módosítsa a házirendet, amely *[RequestDisallowedByPolicy](../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* hiba, amely megakadályozza az új erőforrások az előfizetés vagy az erőforrás a munkaterületet tartalmazó csoport létrehozását. Az Azure-portált használja, Powershell, az Azure parancssori felület vagy API; Hiba, amely a megfelelő házirend kereséséhez műveletek naplózhatók. További tudnivalókért tekintse meg a cikk a [műveletek naplózása tevékenység naplók megtekintése](../azure-resource-manager/resource-group-audit.md). 
    
    c. Miután feloldotta a cikkben ismertetett lépések szerint, OMS kiterjed a riasztásokat az Azure belül a következő napra ütemezett futtatását; nincs szükség semmilyen művelet vagy kezdeményezés.


## <a name="next-steps"></a>További lépések

* További információ az új [Azure riasztások élmény](monitoring-overview-unified-alerts.md).
* További tudnivalók [riasztások jelentkezzen be Azure riasztások](monitor-alerts-unified-log.md).
