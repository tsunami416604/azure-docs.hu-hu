---
title: Azure Automation-erőforrások-kezelési megoldásokban |} Microsoft Docs
description: Megoldások rendszerint tartalmazza a runbookok az Azure Automationben folyamatoknak, mint például a összegyűjtése és figyelési adatokat feldolgozó automatizálására.  Ez a cikk ismerteti a runbookok és a kapcsolódó erőforrások közé tartoznak a megoldásban.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5750cd1147ec861ea38ff2ebc9ce481d256c1959
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>Azure Automation-erőforrások hozzáadása a felügyeleti megoldás (előzetes verzió)
> [!NOTE]
> Ez az előzetes dokumentációjában létrehozása kezelési megoldást, amely jelenleg előzetes verziójúak. Az alábbiakban a séma van változhat.   


[Megoldások](operations-management-suite-solutions.md) runbookok rendszerint tartalmazza az Azure Automationben folyamatoknak, mint például a összegyűjtése és figyelési adatokat feldolgozó automatizálására.  Mellett runbookok Automation-fiók erőforrásokat, például a változók és a megoldásban használt runbookok támogató ütemezéseket tartalmaz.  Ez a cikk ismerteti a runbookok és a kapcsolódó erőforrások közé tartoznak a megoldásban.

> [!NOTE]
> Ebben a cikkben a minták használható paramétereket és változókat, amelyek a szükséges vagy közös felügyeleti megoldás és a [tervezési és -buildek olyan felügyeleti megoldást az Azure-ban ](operations-management-suite-solutions-creating.md) 


## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy már ismeri a következő információkat.

- Hogyan [felügyeleti megoldás létrehozása](operations-management-suite-solutions-creating.md).
- Szerkezete egy [megoldásfájlt](operations-management-suite-solutions-solution-file.md).
- Hogyan [Resource Manager-sablonok készítésének](../azure-resource-manager/resource-group-authoring-templates.md)

## <a name="automation-account"></a>Automation-fiók
Az Azure Automationben összes erőforrás található egy [Automation-fiók](../automation/automation-security-overview.md#automation-account-overview).  A [og Naplóelemzési munkaterület és Automation-fiók](operations-management-suite-solutions.md#log-analytics-workspace-and-automation-account) az Automation-fiók nem található meg a felügyeleti megoldás, de már léteznie kell a megoldás telepítve van.  Ha nem érhető el, akkor a megoldás telepítése sikertelen lesz.

Minden egyes Automation-erőforrás nevét tartalmazza az Automation-fiók nevét.  Ezt a megoldást a **accountName** paraméter egy runbook-erőforrást az alábbi példában látható módon.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbookok
A megoldás a megoldásfájl használja, hogy a megoldás telepítésekor jönnek létre runbookokat kell tartalmaznia.  A runbook a sablon törzsét nem tartalmazhat azonban, így kell közzétenni a runbookot, egy nyilvános helyre, ahol az elérhető bármely felhasználó, a megoldás telepítése.

[Azure Automation-runbook](../automation/automation-runbook-types.md) erőforrások típusa lehet **Microsoft.Automation/automationAccounts/runbooks** és az alábbi szerkezettel rendelkezik. Ez magában foglalja közös változók és paramétereket, így másolhatja és illessze be a következő kódrészletet a megoldásfájlt a, és módosítsa a paraméterek nevei. 

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


Az alábbi táblázat ismerteti a runbookok tulajdonságai.

| Tulajdonság | Leírás |
|:--- |:--- |
| runbookType |Adja meg a runbook típusú. <br><br> Parancsfájl - PowerShell-parancsfájl <br>PowerShell - PowerShell munkafolyamat <br> GraphPowerShell - grafikus PowerShell-parancsfájl forgatókönyv <br> GraphPowerShellWorkflow - grafikus PowerShell-munkafolyamati forgatókönyv |
| logProgress |Megadja, hogy [rekordok előrehaladás](../automation/automation-runbook-output-and-messages.md) elő kell állítani a runbookot. |
| logVerbose |Megadja, hogy [részletes rekordok](../automation/automation-runbook-output-and-messages.md) elő kell állítani a runbookot. |
| leírás |Megadhat egy leírást a runbook. |
| publishContentLink |Adja meg a runbook tartalmának. <br><br>URI - Uri-t a runbook tartalmának.  Ez lesz a PowerShell és a parancsfájl runbookok .ps1 fájl, és az exportált grafikus forgatókönyvnek fájl, a Graph-forgatókönyvek esetében.  <br> verzió - verzió a runbook a saját nyomon követésére. |


## <a name="automation-jobs"></a>Automatizálási feladatok
Amikor elindít egy forgatókönyvet az Azure Automationben, létrehoz egy automation-feladat.  A megoldás automatikusan induljon egy runbookot a kezelési megoldással telepítve van az automation-feladat erőforrás adhat hozzá.  Ez a módszer általában segítségével indítsa el a kezdeti konfigurálása a megoldásban használt runbookok.  Runbook indítása rendszeres időközönként, hozzon létre egy [ütemezés](#schedules) és egy [feladat ütemezése](#job-schedules)

Feladat erőforrások típusa lehet **Microsoft.Automation/automationAccounts/jobs** és az alábbi szerkezettel rendelkezik.  Ez magában foglalja közös változók és paramétereket, így másolhatja és illessze be a következő kódrészletet a megoldásfájlt a, és módosítsa a paraméterek nevei. 

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

Az automatizálási feladatok tulajdonságok az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| a runbook |Egyetlen entitás nevű, a runbook elindul. |
| paraméterek |Az entitás minden egyes a runbook által igényelt paraméterérték. |

A feladat a runbook neve és a runbook küldendő paraméterértékeket tartalmaz.  A feladat-érdemes [függő](operations-management-suite-solutions-solution-file.md#resources) a runbook, mert a runbook indítása a feldolgozás előtt létre kell hozni.  Ha több runbook el kell indítani, megadhatja a sorrendjük azzal, hogy egy feladat minden más, fusson első feladat függ.

Egy feladat erőforrás nevének tartalmaznia kell egy GUID, amely általában egy paraméter által hozzárendelt.  További a GUID-paraméterekkel kapcsolatos [felügyeleti megoldás fájl létrehozása az Azure-ban](operations-management-suite-solutions-solution-file.md#parameters).  


## <a name="certificates"></a>Tanúsítványok
[Azure Automation-tanúsítványok](../automation/automation-certificates.md) típusú **Microsoft.Automation/automationAccounts/certificates** és az alábbi szerkezettel rendelkezik. Ez magában foglalja közös változók és paramétereket, így másolhatja és illessze be a következő kódrészletet a megoldásfájlt a, és módosítsa a paraméterek nevei. 

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



A tanúsítványok erőforrás tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| base64Value |A tanúsítvány Base 64 értéke. |
| ujjlenyomat |A tanúsítvány ujjlenyomata. |



## <a name="credentials"></a>Hitelesítő adatok
[Azure Automation hitelesítő adataival](../automation/automation-credentials.md) típusú **Microsoft.Automation/automationAccounts/credentials** és az alábbi szerkezettel rendelkezik.  Ez magában foglalja közös változók és paramétereket, így másolhatja és illessze be a következő kódrészletet a megoldásfájlt a, és módosítsa a paraméterek nevei. 


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

A hitelesítő adatok erőforrás tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| Felhasználónév |A hitelesítő adatokat felhasználónév. |
| jelszó |A hitelesítő adatok jelszavát. |


## <a name="schedules"></a>Ütemezések
[Azure Automation-ütemezések](../automation/automation-schedules.md) típusú **Microsoft.Automation/automationAccounts/schedules** , és az az alábbi szerkezettel. Ez magában foglalja közös változók és paramétereket, így másolhatja és illessze be a következő kódrészletet a megoldásfájlt a, és módosítsa a paraméterek nevei. 

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

Az ütemezés erőforrás tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| leírás |Az ütemezés nem kötelező leírása. |
| startTime |Adja meg az ütemezés kezdő időpontjának DateTime objektumként. Egy karakterlánc biztosítható, hogy ha konvertálható érvényes DateTime értékként. |
| IsEnabled |Meghatározza, hogy engedélyezve van-e az ütemezés. |
| tartam |Az ütemezés intervallumát típusa.<br><br>nap<br>óra |
| frequency |Az ütemezés a napok vagy órák száma kell érvényesítést gyakorisága. |

A kezdési idő nagyobb, mint az aktuális idő értékkel ütemezések.  Nem adja meg ezt az értéket egy változóhoz, mivel semmilyen módon nem tudhatja, hogy mikor fog telepíteni kellene lennie.

Használja a következő két stratégiák egyikét, ütemezés erőforrások a megoldás használata esetén.

- Az ütemezés kezdő időpontjának a paraméter használható.  Ez fogja kérni a felhasználó számára adjon meg egy értéket, a megoldás telepítésekor.  Ha egyszerre több ütemezés, használhat egy egyetlen paraméterérték egynél több.
- Az ütemezések használatával, amely akkor kezdődik, amikor a megoldás telepítve van egy runbook létrehozása.  Ezzel megszünteti a felhasználó követelmény adjon meg egy időpontot, de az ütemezés nem tartalmazhatja a megoldásban, ezért el lesz távolítva, a megoldás eltávolításakor.


### <a name="job-schedules"></a>Feladatütemezések
Feladat ütemezése erőforrások ütemezés runbookokhoz hivatkozásra.  Olyan típusú rendelkeznek **Microsoft.Automation/automationAccounts/jobSchedules** , és az az alábbi szerkezettel.  Ez magában foglalja közös változók és paramétereket, így másolhatja és illessze be a következő kódrészletet a megoldásfájlt a, és módosítsa a paraméterek nevei. 

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


A feladatok ütemezésének tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| az ütemezés nevének |Egyetlen **neve** entitás az ütemezés nevét. |
| Runbook neve  |Egyetlen **neve** entitás a runbook nevével.  |



## <a name="variables"></a>Változók
[Azure Automation-változók](../automation/automation-variables.md) típusú **Microsoft.Automation/automationAccounts/variables** és az alábbi szerkezettel rendelkezik.  Ez magában foglalja közös változók és paramétereket, így másolhatja és illessze be a következő kódrészletet a megoldásfájlt a, és módosítsa a paraméterek nevei.

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

A változó erőforrás tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| leírás | Választható módon leírás megadása a változót. |
| isEncrypted | Adja meg a változó titkosítani kell-e. |
| típus | Ezt a tulajdonságot jelenleg nincs hatása.  A változó adattípusa a kezdeti érték határozza meg. |
| érték | A változó értékét. |

> [!NOTE]
> A **típus** tulajdonság jelenleg nem befolyásolja a változó létrehozása folyamatban.  Az adattípus, a változó értéke határozza meg.  

Ha a változó a kezdeti érték, akkor be kell állítani a megfelelő adattípusú értékként.  A következő táblázat a különböző adattípusú megengedett és szintaxisát.  Vegye figyelembe, hogy a JSON-ban értékek várhatóan mindig idézőjelek idézőjelek belül a különleges karaktereket a.  Például egy olyan karakterláncértéket szeretné megadni idézőjelek közé vannak zárva a karakterlánc (az escape-karakter használatával (\\)) egy numerikus érték lenne megadva, az idézőjelek közé foglalt egy készletével.

| Adattípus | Leírás | Példa | Oldja fel a rendszer |
|:--|:--|:--|:--|
| karakterlánc   | Érték tegye idézőjelek közé foglalt.  | "\"Hello world\"" | "Hello world" |
| Numerikus  | Szimpla idézőjelben numerikus értéket.| "64" | 64 |
| logikai  | **Igaz** vagy **hamis** idézőjelben.  Vegye figyelembe, hogy ez az érték kisbetűnek kell lennie. | "true" | igaz |
| dátum/idő | A szerializált dátumértéket.<br>A PowerShell ConvertTo-Json-parancsmaggal előállításához ezt az értéket egy adott dátumot.<br>Példa: get-date "5/24/2017 13:14:57" \| ConvertTo-Json | "\\/Date(1495656897378)\\/" | 2017-05-24 13:14:57 |

## <a name="modules"></a>Modulok
A felügyeleti megoldás nem kell meghatároznia [globális modulok](../automation/automation-integration-modules.md) a runbookok által használt, mert azok mindig elérhető lesz az Automation-fiók.  Meg kell adnia egy erőforrás bármely más, a runbookok által használt modul.

[Integrációs modulok](../automation/automation-integration-modules.md) típusú **Microsoft.Automation/automationAccounts/modules** és az alábbi szerkezettel rendelkezik.  Ez magában foglalja közös változók és paramétereket, így másolhatja és illessze be a következő kódrészletet a megoldásfájlt a, és módosítsa a paraméterek nevei.

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


A modul erőforrás tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| contentLink |A modul tartalmának megadása <br><br>URI - a modul tartalmának Uri-t.  Ez lesz a PowerShell és a parancsfájl runbookok .ps1 fájl, és az exportált grafikus forgatókönyvnek fájl, a Graph-forgatókönyvek esetében.  <br> verzió - saját nyomon követésére modul verziója. |

A runbook modul erőforrást győződjön meg arról, hogy a runbook előtt létre kell függ.

### <a name="updating-modules"></a>Modulok frissítése
Ha egy felügyeleti megoldás, amely tartalmazza a runbook által használt ütemezés szerint frissíti, és az új verziót a megoldás, hogy a runbook által használt új modul, a runbook modul a régi verziót használhatja.  Kell felvenni a következő forgatókönyvek a megoldás, és más runbookokat előtt futtatandó feladat létrehozása.  Ezzel biztosíthatja, hogy a modul frissüljenek szükséges, mielőtt a runbookok be van töltve.

* [Frissítés-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript) biztosítja, hogy a megoldás a runbookok által használt modulok mindegyike a legújabb verzióra.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript) újraregisztrálása összes annak érdekében, hogy a runbookok hozzájuk kapcsolódó használatával a legújabb modulok ütemezés erőforrást.




## <a name="sample"></a>Minta
Az alábbiakban látható egy minta a megoldás, amely tartalmazza, amely a következőket tartalmazza:

- Runbook.  Ez a példa runbook egy nyilvános GitHub-tárházban tárolt.
- Automation-feladat, amely elindítja a runbookot, ha a megoldás telepítve van.
- Ütemezési és rendszeres időközönként a runbook indítására ütemezett feladat.
- Tanúsítvány.
- Hitelesítő adatok.
- Változó.
- A modul.  Ez a [OMSIngestionAPI modul](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) az írás a Naplóelemzési. 

A mintánkban [szokásos megoldást paraméterek](operations-management-suite-solutions-solution-file.md#parameters) változókat, amelyek gyakran használni a megoldás az erőforrás-definíciókban hardcoding értékek szemben.


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
            "Description": "Start time for shedule."
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
* [Nézet felvétele a megoldás](operations-management-suite-solutions-resources-views.md) összegyűjtött adatok megjelenítéséhez.
