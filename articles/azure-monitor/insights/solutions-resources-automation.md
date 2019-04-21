---
title: Azure Automation-erőforrások felügyeleti megoldások |} A Microsoft Docs
description: Felügyeleti megoldások általában az Azure Automationben, például a figyelési adatok feldolgozása és gyűjtéséről folyamatok automatizálása runbookok tartalmazza.  Ez a cikk ismerteti, hogyan foglalhat bele a runbookokat és az azokhoz kapcsolódó erőforrásokat a megoldás.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1c9b13f44dae068597cb82a0aa803283ad5e67bc
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57763606"
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>Azure Automation-erőforrások hozzáadása a felügyeleti megoldásra (előzetes verzió)
> [!NOTE]
> Ez az előzetes dokumentum, jelenleg előzetes verzióban elérhető kezelési megoldások létrehozásához. Semmilyen sémát, az alábbiakban a változhat.   


[Felügyeleti megoldások]( solutions.md) rendszerint tartalmazza a forgatókönyvek az Azure Automationben a automatizálhatja a folyamatokat, például a figyelési adatok feldolgozása és gyűjtéséről.  Runbookok, mellett az Automation-fiókok adategységeket, mint a változók és a megoldásban használt runbookok támogató ütemezések magában foglalja.  Ez a cikk ismerteti, hogyan foglalhat bele a runbookokat és az azokhoz kapcsolódó erőforrásokat a megoldás.

> [!NOTE]
> Ebben a cikkben a minták használata, paraméterek és változók, kötelező vagy közös felügyeleti megoldások és az itt ismertetett [tervezés és felépítés felügyeleti megoldás az Azure-ban]( solutions-creating.md) 


## <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy Ön már ismeri a következő információkat.

- Hogyan [felügyeleti megoldás létrehozása]( solutions-creating.md).
- Szerkezete egy [megoldásfájlt]( solutions-solution-file.md).
- Hogyan [Resource Manager-sablonok készítése](../../azure-resource-manager/resource-group-authoring-templates.md)

## <a name="automation-account"></a>Automation-fiók
Található összes erőforrást az Azure Automation- [Automation-fiók](../../automation/automation-security-overview.md#automation-account-overview).  Leírtak szerint [Log Analytics-munkaterületet és Automation-fiók]( solutions.md#log-analytics-workspace-and-automation-account) az Automation-fiók nem található meg a felügyeleti megoldás, de a megoldás telepítése előtt léteznie kell.  Ha nem érhető el, majd a megoldás telepítése sikertelen lesz.

Minden Automation-erőforrás neve tartalmazza az Automation-fiók nevét.  Ezt a megoldást a **accountName** paraméter egy adott runbook-erőforrás a következő példához hasonlóan.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbookok
A megoldásfájl lévő megoldás által használt, hogy a megoldás telepítése során jönnek létre runbookokat kell tartalmaznia.  A runbook a sablon törzsét nem tartalmazza, ezért a runbook tegyen közzé egy nyilvános helyre, ahol is hozzáférhet a megoldás telepítése felhasználója sem.

[Az Azure Automation-runbook](../../automation/automation-runbook-types.md) erőforrás rendelkezik egy típusú **Microsoft.Automation/automationAccounts/runbooks** és az alábbi struktúrával rendelkeznek. Ez magában foglalja közös változók és paraméterek, így másolhatja, és ez a kódrészlet illessze be a megoldásfájlt, és módosítsa a paraméterek nevei. 

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


Runbookok tulajdonságai a következő táblázat ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| runbookType |Adja meg a runbook típusú. <br><br> -Szkript – PowerShell-parancsfájl <br>PowerShell – PowerShell-munkafolyamat <br> GraphPowerShell - grafikus PowerShell-parancsprogram-forgatókönyv <br> GraphPowerShellWorkflow - grafikus PowerShell-munkafolyamati forgatókönyv |
| logProgress |Megadja, hogy [rekordok halad](../../automation/automation-runbook-output-and-messages.md) elő kell állítani a runbook. |
| logVerbose |Megadja, hogy [részletes rekordok](../../automation/automation-runbook-output-and-messages.md) elő kell állítani a forgatókönyvet. |
| description |A runbook kívánja – leírását. |
| publishContentLink |Adja meg a runbook tartalmát. <br><br>URI - URI-t a runbook tartalmát.  Ez lesz egy .ps1 fájlt a PowerShell és a parancsfájl runbookok és a egy Graph runbook exportált grafikus runbook fájl.  <br> verzió - verzió a runbook a saját nyomon követésére. |


## <a name="automation-jobs"></a>Automation-feladatokkal
Az Azure Automationben elindít egy runbookot, amikor létrehoz egy automation-feladat.  A megoldás automatikusan elindíthat egy runbookot, ha a felügyeleti megoldás telepítve van egy automation-feladat erőforrás adhat hozzá.  Ez a módszer általában segítségével indítsa el a runbookokat, amelyek a megoldás kezdeti konfigurációjához.  Elindít egy runbookot, rendszeres időközönként, hozzon létre egy [ütemezés](#schedules) és a egy [feladat ütemezése](#job-schedules)

Feladat-erőforrások rendelkezik olyan típusú **Microsoft.Automation/automationAccounts/jobs** és az alábbi struktúrával rendelkeznek.  Ez magában foglalja közös változók és paraméterek, így másolhatja, és ez a kódrészlet illessze be a megoldásfájlt, és módosítsa a paraméterek nevei. 

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

Automatizálási feladatok tulajdonságainak a következő táblázat ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| runbook |A runbook elindításához nevű entitás egyetlen nevet. |
| paraméterek |Az entitás minden egyes paraméterérték a runbook által igényelt. |

A feladat a runbook nevét és a runbook küldendő paraméterértékeket tartalmaz.  A feladat kell [függenek]( solutions-solution-file.md#resources) , amely azt indítása óta a runbookot a runbook a feladat előtt kell létrehozni.  Ha több runbook, el kell meghatározhatja a sorrendjük létesíteni egy feladat, bármilyen más feladatokat, amelyeket futtatni első függenek.

Egy feladat erőforrás nevének tartalmaznia kell egy GUID Azonosítót, amely általában egy paraméter által hozzárendelt.  Tudjon meg többet a GUID-paraméterekkel kapcsolatos [felügyeleti megoldás fájl létrehozása az Azure-ban]( solutions-solution-file.md#parameters).  


## <a name="certificates"></a>Tanúsítványok
[Az Azure Automation-tanúsítványok](../../automation/automation-certificates.md) típusú **Microsoft.Automation/automationAccounts/certificates** és az alábbi struktúrával rendelkeznek. Ez magában foglalja közös változók és paraméterek, így másolhatja, és ez a kódrészlet illessze be a megoldásfájlt, és módosítsa a paraméterek nevei. 

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



A tanúsítványok erőforrás tulajdonságait az alábbi táblázatban ismertetett.

| Tulajdonság | Leírás |
|:--- |:--- |
| base64Value |A tanúsítvány Base 64 értéke. |
| thumbprint |A tanúsítvány ujjlenyomatát. |



## <a name="credentials"></a>Hitelesítő adatok
[Az Azure Automation hitelesítő adatok](../../automation/automation-credentials.md) típusú **Microsoft.Automation/automationAccounts/credentials** és az alábbi struktúrával rendelkeznek.  Ez magában foglalja közös változók és paraméterek, így másolhatja, és ez a kódrészlet illessze be a megoldásfájlt, és módosítsa a paraméterek nevei. 


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

Az alábbi táblázatban ismertetett hitelesítőadat-erőforrások tulajdonságai.

| Tulajdonság | Leírás |
|:--- |:--- |
| userName |A hitelesítő felhasználó neve. |
| password |A hitelesítő adatainak jelszava. |


## <a name="schedules"></a>Ütemezések
[Az Azure automatizálási ütemezések](../../automation/automation-schedules.md) típusú **Microsoft.Automation/automationAccounts/schedules** és az alábbi struktúrával rendelkeznek. Ez magában foglalja közös változók és paraméterek, így másolhatja, és ez a kódrészlet illessze be a megoldásfájlt, és módosítsa a paraméterek nevei. 

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

Az alábbi táblázatban ismertetett ütemezés erőforrások tulajdonságait.

| Tulajdonság | Leírás |
|:--- |:--- |
| description |Az ütemezés kívánja – leírását. |
| startTime |Egy DateTime típusú objektumot egy ütemezés kezdési idejét határozza meg. Ha egy érvényes DateTime átalakítható karakterlánc adható meg. |
| isEnabled |Itt adhatja meg, hogy engedélyezve van-e az ütemezés. |
| interval |Az ütemezés intervallumát típusa.<br><br>nap<br>óra |
| frequency |Az ütemezés kell aktiválódik a napok vagy órák során gyakoriságát. |

Ütemezés kezdési idő nagyobb, mint a jelenlegi idő értékkel kell rendelkeznie.  Ezt az értéket nem biztosítja egy változó számára, mert nem tudja, hogy mikor fog telepíteni kell.

Használja a következő két stratégiák egyikét, ütemezheti az erőforrásokat a megoldás használatakor.

- Egy paraméter használható az ütemezés kezdési idejét.  Ekkor megjelenik a felhasználó számára, hogy a megoldás telepítésekor, adjon meg egy értéket.  Ha több ütemezést, használhat egy egyetlen paraméter értéke több, mint egy.
- Hozzon létre az ütemezések használatával egy runbookot, amely akkor kezdődik, amikor a megoldás telepítve van.  Ez a követelmény időpontnak a megadása, hogy a felhasználó eltávolítja, de az ütemezés nem tartalmazza a megoldásában, ezért el lesz távolítva, amikor a megoldás a rendszer eltávolítja.


### <a name="job-schedules"></a>Feladatütemezések
Feladat ütemezése erőforrások csatolása egy runbook-ütemezés szerint.  Vannak olyan típusú **Microsoft.Automation/automationAccounts/jobSchedules** és az alábbi struktúrával rendelkeznek.  Ez magában foglalja közös változók és paraméterek, így másolhatja, és ez a kódrészlet illessze be a megoldásfájlt, és módosítsa a paraméterek nevei. 

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


Az alábbi táblázatban ismertetett feladatok ütemezésének tulajdonságai.

| Tulajdonság | Leírás |
|:--- |:--- |
| schedule name |Egyetlen **neve** entitás az ütemezés nevét. |
| runbook name |Egyetlen **neve** a runbook nevére entitáshoz.  |



## <a name="variables"></a>Változók
[Az Azure Automation-változók](../../automation/automation-variables.md) típusú **Microsoft.Automation/automationAccounts/variables** és az alábbi struktúrával rendelkeznek.  Ez magában foglalja közös változók és paraméterek, így másolhatja, és ez a kódrészlet illessze be a megoldásfájlt, és módosítsa a paraméterek nevei.

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

Változó erőforrások tulajdonságait a következő táblázat ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| description | A változó kívánja – leírását. |
| isEncrypted | Határozza meg, hogy a változó rendszer titkosítsa. |
| type | Ez a tulajdonság jelenleg nem befolyásolja.  A változó adattípusa fog határozza meg a kezdeti érték. |
| value | A változó értékét. |

> [!NOTE]
> A **típus** tulajdonság jelenleg nem érinti a változó létrehozása folyamatban.  Az adattípus a változó értéke határozza meg.  

Ha a változó kezdeti értéke, hogy kell konfigurálni a megfelelő adattípust.  A következő táblázat megengedett különböző adattípusok és a szintaxis.  Vegye figyelembe, hogy a JSON-értékek várhatóan mindig idézőjelek idézőjelek különleges karaktereket az.  Például egy karakterláncértéket ezt kell megadni a karakterlánc idézőjeleket által (használja az escape-karakter (\\)) közben egy numerikus értéket ezt kell megadni, az ajánlatok egy készletét.

| Adattípus | Leírás | Példa | Oldja fel |
|:--|:--|:--|:--|
| string   | Tegye idézőjelek értéket.  | "\"Helló, világ\"" | "Hello world" |
| numeric  | A numerikus értékek a szimpla idézőjelek között.| "64" | 64 |
| boolean  | **Igaz** vagy **hamis** idézőjelben.  Vegye figyelembe, hogy ez az érték csak kisbetűket tartalmazhatnak. | "true" | true |
| datetime | A szerializált dátumérték.<br>A PowerShellben a ConvertTo-Json-parancsmag segítségével hozza létre ezt az értéket egy adott dátumot.<br>Példa: get-date "5/24/2017 13:14:57" \| ConvertTo-Json | "\\/Date(1495656897378)\\/" | 2017-05-24 13:14:57 |

## <a name="modules"></a>Modulok
A felügyeleti megoldás nem kell meghatároznia [globális modulok](../../automation/automation-integration-modules.md) a runbookok által használt, mert ezek mindig elérhető lesz az Automation-fiók.  Között szerepelnek olyan erőforrások esetében a runbookok által használt bármely egyéb modult kell.

[Integrációs modulok](../../automation/automation-integration-modules.md) típusú **Microsoft.Automation/automationAccounts/modules** és az alábbi struktúrával rendelkeznek.  Ez magában foglalja közös változók és paraméterek, így másolhatja, és ez a kódrészlet illessze be a megoldásfájlt, és módosítsa a paraméterek nevei.

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


A modul erőforrás tulajdonságait az alábbi táblázatban ismertetett.

| Tulajdonság | Leírás |
|:--- |:--- |
| contentLink |A modul tartalmának megadása <br><br>URI - a modul tartalmának URI-t.  Ez lesz egy .ps1 fájlt a PowerShell és a parancsfájl runbookok és a egy Graph runbook exportált grafikus runbook fájl.  <br> verzió - modul verziószámát az saját nyomon követésére. |

A runbook függ attól, hogy a runbook előtt létre modul erőforráson.

### <a name="updating-modules"></a>Modulok frissítése
Ha egy felügyeleti megoldás, amely tartalmazza az ütemezés használó runbookot frissíti, és az új verziót, hogy a megoldás, hogy a runbook által használt új modul, a runbook a régi verziót, a modul felhasználhatja.  Érdemes közé tartozik az alábbi runbookok a megoldásban, és hozzon létre egy feladatot, mielőtt más runbookokat futtatásához.  Ez biztosítja, hogy a szükséges modulok frissüljenek mielőtt a rendszer betölti a runbookok szükséges.

* [Frissítés-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/) biztosítja, hogy a megoldás a runbookok által használt összes-e a legújabb verzióra.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/) újraregisztrálása ütemezés erőforrásokat annak érdekében, hogy a runbookok hozzájuk kapcsolódó használnak a legújabb modulok.




## <a name="sample"></a>Sample
Következő látható egy minta olyan megoldás, amely tartalmazza, amely a következő forrásokat tartalmazza:

- A forgatókönyv.  Ez a minta runbook egy nyilvános GitHub-adattár tárolja.
- Automation-feladat, amely elindítja a runbookot, amikor a megoldás telepítve van.
- Ütemezés és az ütemezett feladat, rendszeres időközönként a runbook elindításához.
- a tanúsítvány.
- Hitelesítő adatok.
- A változó.
- A modul.  Ez a [OMSIngestionAPI modul](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) írja az adatokat a Log Analytics számára. 

A példa [standard megoldás paraméterek]( solutions-solution-file.md#parameters) változókat gyakran használni kívánt figyelésekor az erőforrás-definíciókban hardcoding értékek-megoldásban.


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




## <a name="next-steps"></a>További lépések
* [Nézet hozzáadása a megoldás]( solutions-resources-views.md) összegyűjtött adatok vizualizálásához.
