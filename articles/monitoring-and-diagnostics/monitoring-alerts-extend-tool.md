---
title: A napló Analytcs riasztások kiterjesztése az Azure-bA
description: Ez a cikk ismerteti az eszközök és az API, amellyel bővítheti riasztások a Log Analytics az Azure Alerts szolgáltatáshoz.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: d70eecb6a5d6bafbfa6507dbe8b1bcb1cad67191
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990246"
---
# <a name="extend-alerts-from-log-analytics-into-azure-alerts"></a>Riasztások kiterjesztése a Log Analytics az Azure-riasztások
A riasztások szolgáltatása az Azure Log Analyticsben váltja fel az Azure-riasztások. Az átállás részeként a riasztásokat, amelyek eredetileg konfigurálták a Log Analytics kiterjesztik az Azure-bA. Ha nem szeretné automatikusan Azure-ba való áthelyezésének várja, is kezdeményezhet a folyamat:

- Az Operations Management Suite Portalról manuálisan. 
- Programozott módon az API-val AlertsVersion.  

> [!NOTE]
> A Microsoft automatikusan kiterjeszti a nyilvános felhő példányát az Azure Alerts szolgáltatáshoz, a Log Analytics létrehozott riasztásokat egy ismétlődő sorozat, amíg befejeződik a 2018. május 14., kezdve. Ha problémába ütközik létrehozása [Műveletcsoportok](monitoring-action-groups.md), használjon [javítási lépések](monitoring-alerts-extend-tool.md#troubleshooting) beolvasni a Műveletcsoportok jönnek létre automatikusan. 2018. július 5-ig is használhatja ezeket a lépéseket. *Nem alkalmazható Azure kormány Esettanulmányának és a Log Analytics Soveriegn felhőbeli felhasználók*. 

## <a name="option-1-initiate-from-the-operations-management-suite-portal"></a>1. lehetőség: Az Operations Management Suite portálján az kezdeményezése
Az alábbi lépéseket a munkaterületre vonatkozó riasztások kiterjesztése az Operations Management Suite portálján a ismertetik.  

1. Az Azure Portalon válassza ki a **minden szolgáltatás**. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
2. A Log Analytics-előfizetések ablaktábláján válasszon ki egy munkaterületet, és válassza a **OMS-portálon** csempére.
![Képernyőkép a Log Analytics előfizetés ablaktáblán kiemelt OMS-portálon csempével](./media/monitor-alerts-extend/azure-portal-01.png) 
3. Után a rendszer átirányítja az Operations Management Suite portálra, válassza ki a **beállítások** ikonra.
![Képernyőkép az Operations Management Suite portálján, a beállítások ikonja kiemelve](./media/monitor-alerts-extend/oms-portal-settings-option.png) 
4. Az a **beállítások** lapon jelölje be **riasztások**.  
5. Válassza ki **kiterjesztése az Azure-bA**.
![Képernyőkép az Operations Management Suite portál riasztási beállítások oldala, amelyen a kiemelt Azure-ba való bővítése](./media/monitor-alerts-extend/ExtendInto.png)
6. Megjelenik egy három lépéses varázsló, a **riasztások** ablaktáblán. Olvassa el az áttekintést, és válassza ki **tovább**.
![1. lépés a varázsló képernyőképe](./media/monitor-alerts-extend/ExtendStep1.png)  
7. A második lépésben látja összegzését, javasolt változtatás ajánlati megfelelő [Műveletcsoportok](monitoring-action-groups.md) a riasztások. Hasonló műveletek közötti egynél több riasztás is látható, ha a varázsló azokat egyetlen művelettel csoport hozzárendelése javasolja.  Az elnevezési szabályt követik a következőképpen történik: *WorkspaceName_AG_ #Number*. A továbblépéshez válasszon **tovább**.
![2. lépés a varázsló képernyőképe](./media/monitor-alerts-extend/ExtendStep2.png)  
8. A varázsló utolsó lépése, jelölje ki **Befejezés**, és győződjön meg arról, amikor a rendszer kéri a folyamat elindításához. Igény szerint megadhat egy e-mail-címet, így értesítést kap a folyamat befejeződik, és az összes riasztás sikeresen helyezte az Azure Alerts szolgáltatáshoz.
![3. lépés a varázsló képernyőképe](./media/monitor-alerts-extend/ExtendStep3.png)

Ha a varázsló nem végzett, a **riasztási beállítások** lap, riasztások kiterjesztése az Azure-bA lehetősége el lett távolítva. A háttérben a riasztásokat az Azure-ba kerülnek, és ez eltarthat egy ideig. A művelet során nem végezhet módosításokat a riasztásokat az Operations Management Suite-portálon. A portál tetején a szalagcímen az aktuális állapota látható. Ha korábban megadott e-mail-címmel, egy e-mailt kap, amikor a folyamat sikeresen befejeződött.  


Riasztások továbbra is megjelennek az Operations Management Suite-portálon, az Azure-bA sikeresen áthelyezett után is.
![Képernyőkép az Operations Management Suite portál riasztási beállítások lap](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2-use-the-alertsversion-api"></a>2. lehetőség: A AlertsVersion API használata
A Log Analytics AlertsVersion API segítségével riasztások kiterjesztése a Log Analytics az Azure Alerts minden ügyfélről, amely segítségével meghívhatja a REST API-t. Hozzáférhet az API-t a PowerShell használatával [ARMClient](https://github.com/projectkudu/ARMClient), egy nyílt forráskódú parancssori eszközt. Az eredményeket JSON-fájlban.  

Az API használatához, először hozzon létre egy GET kéréssel. Kiértékeli, és ez a javasolt változtatások összegző eredménnyel tér vissza, mielőtt ténylegesen kiterjesztése az Azure-bA egy POST-kérés használatával. Az eredmények sorolják fel a riasztásokat és a egy javasolt listáját [Műveletcsoportok](monitoring-action-groups.md), JSON formátumban. Hasonló műveletek közötti egynél több riasztás is látható, ha a szolgáltatás javaslatot tesz egy egyetlen művelettel csoporthoz társítandó azokat. Az elnevezési szabályt követik a következőképpen történik: *WorkspaceName_AG_ #Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Ha a GET-kérés sikeres, 200-as HTTP-állapotkód ad vissza, és a riasztások listája, és javasolt műveletcsoportok a JSON-adatokat. A következő egy példa választ:

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
Ha a megadott munkaterület nem rendelkezik a riasztási szabályok meghatározva, a JSON-adatokat a következő adja vissza:

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Ha a megadott munkaterület összes riasztási szabályt már ki van bővítve az Azure-ba, a GET kérelemre adott válasz a következő:

```json
{
    "version": 2
}
```

A riasztások az Azure-ba való migrálás elindításához egy POST-választ kezdeményezni. A bejegyzés választ megerősíti, hogy a leképezés, valamint elfogadó, szeretné, hogy a riasztások a Log Analytics az Azure Alerts kiterjesztett. A tevékenységek ütemezve van, és a riasztások feldolgozása történik jelöli, ha a GET-válasz korábban végrehajtott eredményei alapján. Szükség esetén is adja meg, amelyhez a Log Analytics jelentést küld, amikor ütemezett háttér folyamata az áttelepítéshez a riasztás sikeresen lefutott e-mail-címek listáját. Ez a példa kérelem is használhatja:

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Eredményét áttelepítése riasztások az Azure Alerts GET válaszban által átadott alapján változhat. Ha ütemezve, riasztások a Log Analyticsben átmenetileg nem érhetők el a módosítás az Operations Management Suite portálján. Új riasztásokat is létrehozhat. 

Ha a POST-kérés sikeres, egy HTTP 200 OK állapota mellett a következő választ adja vissza:

```json
{
    "version": 2
}
```

Ez a válasz azt jelzi, hogy a riasztásokat az Azure Alerts sikeresen bővültek. A verzió tulajdonság értéke csak a rendszer ellenőrzi, hogy a riasztások az Azure-ban fut, és nem állnak kapcsolatban a [Log Analytics Search API](../log-analytics/log-analytics-api-alerts.md). A riasztások ki vannak bővítve az Azure-bA sikeresen, ha bármely e-mail-címek biztosított a POST-kérelmet küldött jelentést. A megadott munkaterület minden riasztás már ütemezett bővíthetők, a POST-kérés adott válasz-e, hogy a kísérlet történt tiltott (a 403-as állapotkód). Hibaüzenetek megtekintéséhez, vagy ha elakadt a folyamat ismertetése, küldhet egy GET kéréssel. Ha hibaüzenet jelenik meg, ezt adja vissza, az összefoglaló információkkal együtt.

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


## <a name="option-3-use-a-custom-powershell-script"></a>3. lehetőség: Egyéni PowerShell parancsfájl használata
 Ha a Microsoft nem sikerült kibővítve a riasztásokat az Operations Management Suite-portálról az Azure-ba, akkor manuálisan tehetik 2018. július 5-ig. A két lehetőség manuális bővítmény terjed ki az előző két szakaszokban.

2018. július 5., után minden riasztás az Operations Management Suite portálon kiterjeszthetők az Azure. Felhasználók, akik nem igénybe a [javasolt szükséges javítási lépések](#troubleshooting) a riasztások kiváltó műveletek vagy hiánya miatt az értesítéseket, anélkül fog kapcsolódó [Műveletcsoportok](monitoring-action-groups.md). 

Hozhat létre [Műveletcsoportok](monitoring-action-groups.md) riasztások manuálisan a Log Analyticsben, használja a következő minta parancsfájlt:
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
Az alábbiakban látható a szkripttel kapcsolatos fontos információkat:
- Előfeltétel telepítése [ARMclient](https://github.com/projectkudu/ARMClient), egy nyílt forráskódú parancssori eszköz, amely leegyszerűsíti az Azure Resource Manager API meghívása.
- A szkript futtatásához egy közreműködői vagy tulajdonosi szerepkörrel kell rendelkeznie az Azure-előfizetésében.
- Az alábbi paramétereket kell megadnia:
    - $subscriptionId: az Azure-előfizetési Azonosítót az Operations Management Suite Log Analytics-munkaterülethez társított.
    - $resourceGroup: az Operations Management Suite Log Analytics-munkaterülethez tartozó Azure-erőforráscsoport.
    - $workspaceName: az Operations Management Suite Log Analytics-munkaterület nevét.

### <a name="output-of-the-custom-powershell-script"></a>Egyéni PowerShell-parancsprogram kimenetéből
A parancsfájl részletes, és kiírja a lépéseket, annak futtatása során: 
- Megjeleníti az összefoglaló, amely a munkaterületen a meglévő Operations Management Suite Log Analytics-riasztások információkat tartalmaz. Az összegzés a hozzájuk rendelt műveleteket kell létrehozni az Azure Műveletcsoportok vonatkozó információkat is tartalmaz. 
- Lépjen tovább a kiterjesztésű, vagy lépjen ki az összegzés megtekintése után kéri.
- Lépjen tovább az bővítménnyel, ha új Azure Műveletcsoportok jönnek létre, és minden meglévő riasztás rendelt őket. 
- A parancsfájl kilép a "Teljes bővítmény!" üzenet megjelenítésével Minden olyan köztes hibák esetén a szkript további hibákat jeleníti meg.

## <a name="troubleshooting"></a>Hibaelhárítás 
Riasztások kiterjesztése az a folyamat során problémák megakadályozhatja a rendszer az ehhez szükséges [Műveletcsoportok](monitoring-action-groups.md). Ezekben az esetekben a fejléc a hibaüzenetet látja a **riasztási** szakasz az Operations Management Suite portálján, vagy a GET hívja az API-nak kész.

> [!IMPORTANT]
> Ha Azure nyilvános felhő alapú Log Analytics-felhasználók 2018. július 5. előtt nem kell a következő javítási lépéseket, a riasztások az Azure-ban futnak, de nem indulnak el, bármilyen művelet vagy az értesítés. Értesítés küldése riasztások, kell manuálisan szerkessze, és adja hozzá [Műveletcsoportok](monitoring-action-groups.md), vagy használja az előző [egyéni PowerShell-parancsprogram](#option-3---using-custom-powershell-script).

Minden egyes hibához szervizelés lépései a következők:
- **Chyba: Hatókör zár megtalálható előfizetés/erőforráscsoport szintjén az írási műveletek**: ![lapjának képernyőképe, az Operations Management Suite portál riasztási beállítások, kiemelve hatókör zárolási hibaüzenet](./media/monitor-alerts-extend/ErrorScopeLock.png)

    Hatókör zár engedélyezve van, amikor a szolgáltatás korlátozza (Operations Management Suite) Log Analytics-munkaterületnek tartalmazó előfizetés vagy az erőforrás-csoport minden olyan új változásáról. A rendszer nem tudja riasztások kiterjesztése az Azure-ba, és a szükséges Műveletcsoportok létrehozása.
    
    Megoldásához, törölje a *ReadOnly* a a munkaterületet tartalmazó előfizetés vagy az erőforrás csoport zárolása. Ehhez az Azure portal, PowerShell, Azure CLI-vel vagy az API használatával. További tudnivalókért lásd: [erőforrás zárolása használati](../azure-resource-manager/resource-group-lock-resources.md). 
    
    A cikkben bemutatott lépések segítségével hárítsa el a hibát, ha az Operations Management Suite az Azure-bA a riasztások kiterjeszti a következő napra ütemezett futtatását. Nem kell további műveleteket, vagy kezdeményezzen semmit.

- **Hiba: A házirend jelen előfizetés/erőforráscsoport szintjén**: ![lapjának képernyőképe, az Operations Management Suite portál riasztási beállítások, kiemelve a házirend hibaüzenet](./media/monitor-alerts-extend/ErrorPolicy.png)

    Amikor [Azure Policy](../azure-policy/azure-policy-introduction.md) van érvényben, korlátozza a Log Analytics (az Operations Management Suite) munkaterületet tartalmazó előfizetés vagy az erőforrás csoportban bármilyen új erőforrást. A rendszer nem tudja riasztások kiterjesztése az Azure-ba, és a szükséges Műveletcsoportok létrehozása.
    
    Megoldásához módosítsa a házirendet, amely miatt a *[RequestDisallowedByPolicy](../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* hiba, amely megakadályozza az új erőforrások a vagy erőforráscsoportonként csoport, amely tartalmazza a munkaterület létrehozását. Ehhez az Azure portal, PowerShell, Azure CLI-vel vagy az API használatával. Keresse meg a megfelelő szabályzatot hibát okozó műveletek naplózhatók. További tudnivalókért lásd: [végzett műveletek naplózásához tevékenységi naplóinak megtekintése](../azure-resource-manager/resource-group-audit.md). 
    
    A cikkben bemutatott lépések segítségével hárítsa el a hibát, ha az Operations Management Suite az Azure-bA a riasztások kiterjeszti a következő napra ütemezett futtatását. Nem kell további műveleteket, vagy kezdeményezzen semmit.


## <a name="next-steps"></a>További lépések

* További információ az új [Azure Alerts élmény](monitoring-overview-unified-alerts.md).
* Ismerje meg [naplóriasztások az Azure Alerts](monitor-alerts-unified-log.md).
