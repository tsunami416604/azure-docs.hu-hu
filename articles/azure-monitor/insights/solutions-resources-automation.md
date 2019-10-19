---
title: Erőforrások Azure Automation a felügyeleti megoldásokban | Microsoft Docs
description: A felügyeleti megoldások általában Azure Automation runbookok tartalmaznak a folyamatok automatizálásához, például a figyelési adatok gyűjtéséhez és feldolgozásához.  Ez a cikk azt ismerteti, hogyan lehet a runbookok és a hozzájuk kapcsolódó erőforrásokat felvenni egy megoldásba.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/24/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 63e09bacd1ce70f05f04798f092d3eb4b3e36ab5
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555246"
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>Azure Automation erőforrások hozzáadása felügyeleti megoldáshoz (előzetes verzió)
> [!NOTE]
> Ez a jelenleg előzetes verzióban elérhető felügyeleti megoldások létrehozásának előzetes dokumentációja. Az alább ismertetett sémák változhatnak.   


A [felügyeleti megoldások]( solutions.md) általában Azure Automation runbookok tartalmaznak a folyamatok automatizálásához, például a figyelési adatok gyűjtéséhez és feldolgozásához.  A runbookok mellett az Automation-fiókok olyan eszközöket is tartalmaznak, mint például a változók és az ütemtervek, amelyek támogatják a megoldásban használt runbookok.  Ez a cikk azt ismerteti, hogyan lehet a runbookok és a hozzájuk kapcsolódó erőforrásokat felvenni egy megoldásba.

> [!NOTE]
> A cikkben szereplő minták olyan paramétereket és változókat használnak, amelyek szükségesek vagy közösek a felügyeleti megoldásokhoz, és az Azure-beli [felügyeleti megoldás kialakítása és]( solutions-creating.md) létrehozása című témakörben olvashatók. 


## <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy már ismeri a következő információkat.

- [Felügyeleti megoldás létrehozása]( solutions-creating.md).
- Egy [megoldás fájljának]( solutions-solution-file.md)szerkezete.
- [Resource Manager-sablonok](../../azure-resource-manager/resource-group-authoring-templates.md) készítése

## <a name="automation-account"></a>Automation-fiók
Azure Automation összes erőforrása egy [Automation-fiókban](../../automation/automation-security-overview.md#automation-account-overview)található.  Az [log Analytics munkaterület és az Automation-fiók]( solutions.md#log-analytics-workspace-and-automation-account) című témakörben leírtak szerint az Automation-fiók nem szerepel a felügyeleti megoldásban, de a megoldás telepítése előtt léteznie kell.  Ha nem érhető el, a megoldás telepítése sikertelen lesz.

Az egyes Automation-erőforrások neve tartalmazza az Automation-fiók nevét.  Ezt a megoldásban a **accountName** paraméterrel végezheti el, mint a runbook-erőforrás következő példájában.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbookok
A megoldás által használt összes runbookok tartalmaznia kell a megoldás fájljában, hogy azok a megoldás telepítésekor jöjjenek létre.  A sablonban nem szerepelhet a runbook törzse, ezért a runbook közzé kell tenni egy nyilvános helyre, ahol a megoldást telepítő bármely felhasználó hozzáférhet.

[Azure Automation runbook](../../automation/automation-runbook-types.md) -erőforrások típusa **Microsoft. Automation/automationAccounts/runbookok** , és a következő szerkezettel rendelkezik. Ilyenek például a gyakori változók és paraméterek, hogy a kódrészletet másolja és illessze be a megoldás fájljába, és módosítsa a paraméterek nevét. 

    {
        "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
        "type": "Microsoft.Automation/automationAccounts/runbooks",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "location": "[parameters('regionId')]",
        "tags": { },
        "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
                "uri": "[variables('Runbook').Uri]",
                "version": [variables('Runbook').Version]"
            }
        }
    }


A runbookok tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| Kötelező runbooktype értéke |Megadja a runbook típusát. <br><br> Parancsfájl – PowerShell-parancsfájl <br>PowerShell – PowerShell-munkafolyamat <br> GraphPowerShell – grafikus PowerShell-parancsfájl runbook <br> GraphPowerShellWorkflow – grafikus PowerShell-munkafolyamat runbook |
| logProgress |Meghatározza, hogy a runbook kell-e létrehozni az [előrehaladási rekordokat](../../automation/automation-runbook-output-and-messages.md) . |
| logVerbose |Meghatározza, hogy a runbook kell-e létrehozni a [részletes rekordokat](../../automation/automation-runbook-output-and-messages.md) . |
| leírás |A runbook leírását nem kötelező megadni. |
| publishContentLink |Megadja a runbook tartalmát. <br><br>URI – URI a runbook tartalmához.  Ez egy. ps1-fájl a PowerShell és a parancsfájl runbookok, valamint egy gráf runbook exportált grafikus runbook-fájlja.  <br> a runbook verziója a saját nyomon követéséhez. |


## <a name="automation-jobs"></a>Automation-feladatok
Amikor runbook indít a Azure Automationban, egy Automation-feladatot hoz létre.  Az Automation-feladatok erőforrásait hozzáadhatja a megoldáshoz, hogy automatikusan elindítson egy runbook a felügyeleti megoldás telepítésekor.  Ez a metódus általában a megoldás kezdeti konfigurálásához használt runbookok elindítására szolgál.  A runbook rendszeres időközönkénti elindításához hozzon létre egy [ütemtervet](#schedules) és egy [feladatütemezés](#job-schedules)

A feladatok erőforrásai a **Microsoft. Automation/automationAccounts/Jobs** típussal rendelkeznek, és a következő szerkezettel rendelkeznek.  Ilyenek például a gyakori változók és paraméterek, hogy a kódrészletet másolja és illessze be a megoldás fájljába, és módosítsa a paraméterek nevét. 

    {
      "name": "[concat(parameters('accountName'), '/', parameters('Runbook').JobGuid)]",
      "type": "Microsoft.Automation/automationAccounts/jobs",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
      ],
      "tags": { },
      "properties": {
        "runbook": {
          "name": "[variables('Runbook').Name]"
        },
        "parameters": {
          "Parameter1": "[[variables('Runbook').Parameter1]",
          "Parameter2": "[[variables('Runbook').Parameter2]"
        }
      }
    }

Az Automation-feladatok tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| runbook |Egyetlen Name entitás, amely az runbook nevét adja meg. |
| paraméterek |A runbook által igényelt paraméterek értékének entitása. |

A feladattípus tartalmazza a runbook nevét és a runbook küldendő paraméterek értékét.  A feladattípusnak [attól]( solutions-solution-file.md#resources) a runbook kell tartoznia, amelyről indul, mert a runbook a feladatokhoz kell létrehozni.  Ha több runbookok is meg kell kezdenie, akkor megadhatja a sorrendet, ha a feladat attól függ, hogy milyen feladatokat kell futtatnia.

A feladathoz tartozó erőforrás nevének olyan GUID-azonosítót kell tartalmaznia, amelyet általában egy paraméter rendel hozzá.  A GUID paraméterekkel kapcsolatos további információkért tekintse meg a [felügyeleti megoldás fájljának létrehozása az Azure-ban]( solutions-solution-file.md#parameters)című részt.  


## <a name="certificates"></a>Tanúsítványok
[Azure Automation a tanúsítványok](../../automation/automation-certificates.md) típusa **Microsoft. Automation/automationAccounts/Certificates** , és a következő szerkezettel rendelkezik. Ilyenek például a gyakori változók és paraméterek, hogy a kódrészletet másolja és illessze be a megoldás fájljába, és módosítsa a paraméterek nevét. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
      "type": "Microsoft.Automation/automationAccounts/certificates",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "base64Value": "[variables('Certificate').Base64Value]",
        "thumbprint": "[variables('Certificate').Thumbprint]"
      }
    }



A tanúsítványok erőforrásainak tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| Base64value tulajdonsága |A tanúsítvány alap 64-értéke. |
| ujjlenyomat |A Tanúsítvány ujjlenyomata. |



## <a name="credentials"></a>Hitelesítő adatok
[Azure Automation a hitelesítő adatok](../../automation/automation-credentials.md) **Microsoft. Automation/automationAccounts/hitelesítő adatokkal** rendelkeznek, és a következő struktúrával rendelkeznek.  Ilyenek például a gyakori változók és paraméterek, hogy a kódrészletet másolja és illessze be a megoldás fájljába, és módosítsa a paraméterek nevét. 


    {
      "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
      "type": "Microsoft.Automation/automationAccounts/credentials",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "userName": "[parameters('credentialUsername')]",
        "password": "[parameters('credentialPassword')]"
      }
    }

A hitelesítőadat-erőforrások tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| userName |A hitelesítő adat felhasználóneve. |
| jelszó |A hitelesítő adat jelszava. |


## <a name="schedules"></a>Ütemezések
[Azure Automation az ütemtervek](../../automation/automation-schedules.md) a **Microsoft. Automation/automationAccounts/Schedules** típussal rendelkeznek, és az alábbi struktúrával rendelkeznek. Ilyenek például a gyakori változók és paraméterek, hogy a kódrészletet másolja és illessze be a megoldás fájljába, és módosítsa a paraméterek nevét. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
      "type": "microsoft.automation/automationAccounts/schedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Schedule').Description]",
        "startTime": "[parameters('scheduleStartTime')]",
        "timeZone": "[parameters('scheduleTimeZone')]",
        "isEnabled": "[variables('Schedule').IsEnabled]",
        "interval": "[variables('Schedule').Interval]",
        "frequency": "[variables('Schedule').Frequency]"
      }
    }

Az ütemezett erőforrások tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| leírás |Az ütemterv leírását nem kötelező megadni. |
| startTime |Meghatározza az ütemterv kezdő időpontját DateTime objektumként. Karakterláncot adhat meg, ha egy érvényes DateTime értékre konvertálható. |
| isEnabled |Meghatározza, hogy az ütemterv engedélyezve van-e. |
| interval |Az ütemterv intervallumának típusa<br><br>nap<br>óra |
| frequency |A gyakoriság, amelyet az ütemtervnek a napok vagy órák számának megfelelően kell megjelennie. |

Az ütemezett kezdési időpontnak az aktuális időpontnál nagyobb értékkel kell rendelkeznie.  Ez az érték nem adható meg változóként, mert nem tudná tudni, hogy mikor lesz telepítve.

Használja az alábbi két stratégia egyikét, ha egy megoldásban ütemezett erőforrásokat használ.

- Az ütemezett kezdési időponthoz használjon paramétert.  Ekkor a rendszer megkéri a felhasználót, hogy adjon meg egy értéket a megoldás telepítésekor.  Ha több ütemterv is van, akkor egyetlen paraméter értékét is használhatja többek között.
- Hozza létre az ütemterveket egy olyan runbook használatával, amely a megoldás telepítésekor indul el.  Ezzel a beállítással a felhasználó megadhatja az időpontot, de nem tartalmazhatja a megoldás ütemezését, így a rendszer eltávolítja a megoldás eltávolításakor.


### <a name="job-schedules"></a>Feladatütemezések
A feladatütemezés erőforrásai egy runbook és egy ütemezett műveletet kapcsolnak össze.  A **Microsoft. Automation/automationAccounts/jobSchedules** típussal rendelkeznek, és az alábbi struktúrával rendelkeznek.  Ilyenek például a gyakori változók és paraméterek, hogy a kódrészletet másolja és illessze be a megoldás fájljába, és módosítsa a paraméterek nevét. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
      "type": "microsoft.automation/automationAccounts/jobSchedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
        "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
      ],
      "tags": {
      },
      "properties": {
        "schedule": {
          "name": "[variables('Schedule').Name]"
        },
        "runbook": {
          "name": "[variables('Runbook').Name]"
        }
      }
    }


A feladatütemezés tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| Ütemterv neve |Egyetlen **Name** entitás az ütemterv nevével. |
| runbook neve  |Egyetlen **Name** entitás a runbook nevével.  |



## <a name="variables"></a>Változók
[Azure Automation változók](../../automation/automation-variables.md) típusa **Microsoft. Automation/automationAccounts/változó** , és a következő szerkezettel rendelkezik.  Ilyenek például a gyakori változók és paraméterek, hogy a kódrészletet másolja és illessze be a megoldás fájljába, és módosítsa a paraméterek nevét.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
      "type": "microsoft.automation/automationAccounts/variables",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Variable').Description]",
        "isEncrypted": "[variables('Variable').Encrypted]",
        "type": "[variables('Variable').Type]",
        "value": "[variables('Variable').Value]"
      }
    }

A változó erőforrások tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| leírás | A változó leírását nem kötelező megadni. |
| isEncrypted | Megadja, hogy a változó titkosítva legyen-e. |
| type | Ennek a tulajdonságnak jelenleg nincs hatása.  A változó adattípusa a kezdeti érték alapján lesz meghatározva. |
| érték | A változó értéke. |

> [!NOTE]
> A **Type** tulajdonság jelenleg nem befolyásolja a létrehozandó változót.  A változó adattípusa az érték alapján lesz meghatározva.  

Ha a változóhoz a kezdeti értéket állítja be, akkor azt megfelelő adattípusként kell konfigurálni.  Az alábbi táblázat a különböző adattípusokat és azok szintaxisát tartalmazza.  Vegye figyelembe, hogy a JSON-értékeket mindig idézőjelek közé kell tenni az idézőjelek között lévő speciális karakterekkel.  Például egy karakterlánc értékét a karakterlánc körüli idézőjelek határozzák meg (az Escape-karakterrel (\\)), míg egy numerikus értéket egy idézőjelek között kell megadni.

| Data type | Leírás | Példa | Feloldás |
|:--|:--|:--|:--|
| sztring   | Érték befoglalása idézőjelek közé.  | "\"Hello World \"" | "Helló világ" |
| numerikus  | Numerikus érték szimpla idézőjelekkel.| "64" | 64 |
| logikai  | **igaz** vagy **hamis** idézőjelek között.  Vegye figyelembe, hogy ennek az értéknek kisbetűsnek kell lennie. | igaz | igaz |
| dátum/idő | Szerializált dátumérték.<br>Az ConvertTo-JSON parancsmagot a PowerShellben használhatja egy adott dátumhoz tartozó érték létrehozásához.<br>Példa: Get-date "5/24/2017 13:14:57" \| ConvertTo-JSON | "\\/Date (1495656897378) \\/" | 2017-05-24 13:14:57 |

## <a name="modules"></a>Modulok
A felügyeleti megoldásnak nem kell megadnia a runbookok által használt [globális modulokat](../../automation/automation-integration-modules.md) , mivel azok mindig elérhetők lesznek az Automation-fiókban.  Meg kell adnia egy erőforrást a runbookok által használt bármely más modulhoz.

Az [integrációs modulok](../../automation/automation-integration-modules.md) rendelkeznek a **Microsoft. Automation/automationAccounts/modulok** típusával, és a következő struktúrával rendelkeznek.  Ilyenek például a gyakori változók és paraméterek, hogy a kódrészletet másolja és illessze be a megoldás fájljába, és módosítsa a paraméterek nevét.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
      "type": "Microsoft.Automation/automationAccounts/modules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "dependsOn": [
      ],
      "properties": {
        "contentLink": {
          "uri": "[variables('Module').Uri]"
        }
      }
    }


A modul erőforrásainak tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| contentLink |Megadja a modul tartalmát. <br><br>URI – URI a modul tartalmához.  Ez egy. ps1-fájl a PowerShell és a parancsfájl runbookok, valamint egy gráf runbook exportált grafikus runbook-fájlja.  <br> a modul verziója a saját nyomon követéséhez. |

A runbook a modul erőforrásának kell lennie, hogy az a runbook előtt legyen létrehozva.

### <a name="updating-modules"></a>Modulok frissítése
Ha olyan felügyeleti megoldást frissít, amely egy ütemezett runbook tartalmaz, és a megoldás új verziója új modult használ a runbook, akkor a runbook a modul régi verzióját is használhatja.  Vegye fel a következő runbookok a megoldásba, és hozzon létre egy feladatot, amely minden más runbookok előtt fut.  Ez biztosítja, hogy a modulok a runbookok betöltése előtt a szükséges módon frissüljenek.

* Az [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/) biztosítja, hogy a runbookok által a megoldásban használt összes modul a legújabb verziót használja.  
* A [ReRegisterAutomationSchedule-MS-mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/) újra regisztrálja az összes ütemezett erőforrást, hogy a runbookok a legújabb modulok használatával legyenek társítva.




## <a name="sample"></a>Minta
A következő példa egy olyan megoldás mintáját tartalmazza, amely magában foglalja a következő erőforrásokat:

- Runbook.  Ez egy nyilvános GitHub-tárházban tárolt runbook.
- Az Automation-feladatot, amely elindítja a runbook a megoldás telepítésekor.
- Ütemezett és feladatütemezés, hogy rendszeres időközönként elindítsa a runbook.
- Tanúsítvány.
- Hitelesítőadat.
- Változó.
- Modul.  Ez a [OMSIngestionAPI modul](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) az adatLog Analyticsba való íráshoz. 

A minta a [standard megoldás paramétereinek]( solutions-solution-file.md#parameters) változóit használja, amelyeket általában egy megoldásban használtak, szemben az rögzítjük lévő értékekkel.


    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Log Analytics workspace."
          }
        },
        "accountName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Automation account."
          }
        },
        "workspaceregionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Log Analytics workspace."
          }
        },
        "regionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Automation account."
          }
        },
        "pricingTier": {
          "type": "string",
          "metadata": {
            "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account."
          }
        },
        "certificateBase64Value": {
          "type": "string",
          "metadata": {
            "Description": "Base 64 value for certificate."
          }
        },
        "certificateThumbprint": {
          "type": "securestring",
          "metadata": {
            "Description": "Thumbprint for certificate."
          }
        },
        "credentialUsername": {
          "type": "string",
          "metadata": {
            "Description": "Username for credential."
          }
        },
        "credentialPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Password for credential."
          }
        },
        "scheduleStartTime": {
          "type": "string",
          "metadata": {
            "Description": "Start time for schedule."
          }
        },
        "scheduleTimeZone": {
          "type": "string",
          "metadata": {
            "Description": "Time zone for schedule."
          }
        },
        "scheduleLinkGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the schedule link to runbook.",
            "control": "guid"
          }
        },
        "runbookJobGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the runbook job.",
            "control": "guid"
          }
        }
      },
      "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
    
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31",
    
        "Runbook": {
          "Name": "MyRunbook",
          "Description": "Sample runbook",
          "Type": "PowerShell",
          "Uri": "https://raw.githubusercontent.com/user/myrepo/master/samples/MyRunbook.ps1",
          "JobGuid": "[parameters('runbookJobGuid')]"
        },
    
        "Certificate": {
          "Name": "MyCertificate",
          "Base64Value": "[parameters('certificateBase64Value')]",
          "Thumbprint": "[parameters('certificateThumbprint')]"
        },
    
        "Credential": {
          "Name": "MyCredential",
          "UserName": "[parameters('credentialUsername')]",
          "Password": "[parameters('credentialPassword')]"
        },
    
        "Schedule": {
          "Name": "MySchedule",
          "Description": "Sample schedule",
          "IsEnabled": "true",
          "Interval": "1",
          "Frequency": "hour",
          "StartTime": "[parameters('scheduleStartTime')]",
          "TimeZone": "[parameters('scheduleTimeZone')]",
          "LinkGuid": "[parameters('scheduleLinkGuid')]"
        },
    
        "Variable": {
          "Name": "MyVariable",
          "Description": "Sample variable",
          "Encrypted": 0,
          "Type": "string",
          "Value": "'This is my string value.'"
        },
    
        "Module": {
          "Name": "OMSIngestionAPI",
          "Uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
        }
      },
      "resources": [
        {
          "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
          "location": "[parameters('workspaceRegionId')]",
          "tags": { },
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "[variables('LogAnalyticsApiVersion')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
            "referencedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
            ],
            "containedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]"
            ]
          },
          "plan": {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
            "Version": "[variables('SolutionVersion')]",
            "product": "[variables('ProductName')]",
            "publisher": "[variables('SolutionPublisher')]",
            "promotionCode": ""
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
              "uri": "[variables('Runbook').Uri]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').JobGuid)]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('Runbook').Name]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
          "type": "Microsoft.Automation/automationAccounts/certificates",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "Base64Value": "[variables('Certificate').Base64Value]",
            "Thumbprint": "[variables('Certificate').Thumbprint]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
          "type": "Microsoft.Automation/automationAccounts/credentials",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "userName": "[variables('Credential').UserName]",
            "password": "[variables('Credential').Password]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
          "type": "microsoft.automation/automationAccounts/schedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Schedule').Description]",
            "startTime": "[variables('Schedule').StartTime]",
            "timeZone": "[variables('Schedule').TimeZone]",
            "isEnabled": "[variables('Schedule').IsEnabled]",
            "interval": "[variables('Schedule').Interval]",
            "frequency": "[variables('Schedule').Frequency]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
          "type": "microsoft.automation/automationAccounts/jobSchedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
          ],
          "tags": {
          },
          "properties": {
            "schedule": {
              "name": "[variables('Schedule').Name]"
            },
            "runbook": {
              "name": "[variables('Runbook').Name]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
          "type": "microsoft.automation/automationAccounts/variables",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Variable').Description]",
            "isEncrypted": "[variables('Variable').Encrypted]",
            "type": "[variables('Variable').Type]",
            "value": "[variables('Variable').Value]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
          "type": "Microsoft.Automation/automationAccounts/modules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "properties": {
            "contentLink": {
              "uri": "[variables('Module').Uri]"
            }
          }
        }
    
      ],
      "outputs": { }
    }




## <a name="next-steps"></a>Következő lépések
* [Vegyen fel egy nézetet a megoldásba]( solutions-resources-views.md) az összegyűjtött adatok megjelenítéséhez.
