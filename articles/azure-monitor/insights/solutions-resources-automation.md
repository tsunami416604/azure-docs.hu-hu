---
title: Azure Automation-erőforrások a felügyeleti megoldásokban | Microsoft dokumentumok
description: A felügyeleti megoldások általában runbookokat tartalmaznak az Azure Automationben a folyamatok automatizálásához, például a figyelési adatok gyűjtéséhez és feldolgozásához.  Ez a cikk bemutatja, hogyan veheti fel a runbookokat és a kapcsolódó erőforrásokat egy megoldásba.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/24/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8ef9f27546e9db95d5a41769e1b5bc7bc0c2f851
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663062"
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>Azure Automation-erőforrások hozzáadása felügyeleti megoldáshoz (előzetes verzió)
> [!NOTE]
> Ez az előzetes dokumentáció felügyeleti megoldások létrehozásához, amelyek jelenleg előzetes verzióban. Az alábbiakban ismertetett sémák változhatnak.   


[A felügyeleti megoldások]( solutions.md) általában runbookokat tartalmaznak az Azure Automationben a folyamatok automatizálásához, például a figyelési adatok gyűjtéséhez és feldolgozásához.  A runbookok mellett az Automation-fiókok olyan eszközöket is tartalmaznak, mint például a változók és az ütemezések, amelyek támogatják a megoldásban használt runbookokat.  Ez a cikk bemutatja, hogyan veheti fel a runbookokat és a kapcsolódó erőforrásokat egy megoldásba.

> [!NOTE]
> A jelen cikkben szereplő minták olyan paramétereket és változókat használnak, amelyek a felügyeleti megoldásokhoz szükségesek vagy közösek, és amelyeket a Tervezés és [felügyeleti megoldás létrehozása az Azure-ban ismertetett.]( solutions-creating.md) 


## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy már ismeri az alábbi információkat.

- Hogyan [hozzunk létre egy kezelési megoldás]( solutions-creating.md).
- A [megoldásfájl]( solutions-solution-file.md)szerkezete .
- Az [Erőforrás-kezelő sablonjainak készítése](../../azure-resource-manager/templates/template-syntax.md)

## <a name="automation-account"></a>Automation-fiók
Az Azure Automation összes erőforrása egy [Automation-fiókban](../../automation/automation-security-overview.md#automation-account-overview)található.  A [Log Analytics-munkaterület és automation-fiók]( solutions.md#log-analytics-workspace-and-automation-account) ban leírtak szerint az Automation-fiók nem szerepel a felügyeleti megoldásban, de a megoldás telepítése előtt léteznie kell.  Ha nem érhető el, akkor a megoldás telepítése sikertelen lesz.

Az egyes Automation-erőforrások neve tartalmazza az Automation-fiók nevét.  Ez a megoldásban a **accountName** paraméterrel történik, mint a következő példában egy runbook-erőforrás.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbookok
A megoldás által használt runbookokat a megoldásfájlba kell foglalnia, hogy a megoldás telepítésekor létrejönjenek.  A runbook törzsét azonban nem tartalmazhatja a sablonban, ezért közzé kell tennie a runbookot egy nyilvános helyen, ahol a megoldást telepítő bármely felhasználó hozzáférhet.

[Az Azure Automation runbook-erőforrásai](../../automation/automation-runbook-types.md) **microsoft.automation/automationAccounts/runbookok** típusával rendelkeznek, és a következő struktúrával rendelkeznek. Ez magában foglalja a gyakori változókat és paramétereket, így ezt a kódrészletet átmásolhatja és beillesztheti a megoldásfájlba, és módosíthatja a paraméterneveket. 

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
| runbookType |A runbook típusait adja meg. <br><br> Parancsfájl – PowerShell-parancsfájl <br>PowerShell – PowerShell-munkafolyamat <br> GraphPowerShell - Grafikus PowerShell-parancsfájl-runbook <br> GraphPowerShellWorkflow – Grafikus PowerShell-munkafolyamat-runbook |
| logProgress |Itt adható meg, hogy létre kell-e hozni a runbook hozlétre [folyamatrekordokat.](../../automation/automation-runbook-output-and-messages.md) |
| logVerbose |Itt adható meg, hogy részletes [rekordokat](../../automation/automation-runbook-output-and-messages.md) kell-e létrehozni a runbookhoz. |
| leírás |A runbook nem kötelező leírása. |
| publishContentLink |A runbook tartalmát adja meg. <br><br>uri - Uri a runbook tartalmához.  Ez egy .ps1 fájl lesz a PowerShell és a Script runbookok számára, és egy exportált grafikus runbook fájl egy Graph runbookhoz.  <br> verzió - Változata a runbook a saját követés. |


## <a name="automation-jobs"></a>Automatizálási feladatok
Amikor elindítja a runbook az Azure Automationben, létrehoz egy automatizálási feladat.  Hozzáadhat egy automatizálási feladat-erőforrást a megoldáshoz, hogy automatikusan elindítson egy runbookot, amikor a felügyeleti megoldás telepítve van.  Ez a módszer általában a megoldás kezdeti konfigurálásához használt runbookok indítására szolgál.  Runbook rendszeres időközönkénti indításához hozzon létre [ütemezést](#schedules) és [munkaütemezést](#job-schedules)

A feladattípusú **Microsoft.Automation/automationAccounts/jobs** típusú, és a következő struktúrával rendelkezik.  Ez magában foglalja a gyakori változókat és paramétereket, így ezt a kódrészletet átmásolhatja és beillesztheti a megoldásfájlba, és módosíthatja a paraméterneveket. 

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

Az automatizálási feladatok tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| runbook |Egyetlen név entitás a runbook nevét kezdeni. |
| paraméterek |Entitás a runbook által igényelt minden paraméterértékhez. |

A feladat tartalmazza a runbook nevét és a runbooknak küldendő paraméterértékeket.  A feladat [nak attól kell függenie,]( solutions-solution-file.md#resources) hogy a runbook, hogy az indítása óta a runbook kell létrehozni a feladat előtt.  Ha több runbookok, amelyeket el kell indítani, megadhatja a sorrendben, ha egy feladat függ bármely más feladatok, amelyeket először futtatni kell.

A projekterőforrás nevének tartalmaznia kell egy GUID azonosítót, amelyet általában egy paraméter rendel hozzá.  A GUID-paraméterekről a Felügyeleti megoldás fájl létrehozása az [Azure-ban]( solutions-solution-file.md#parameters)területen olvashat bővebben.  


## <a name="certificates"></a>Tanúsítványok
[Az Azure Automation-tanúsítványok](../../automation/automation-certificates.md) **microsoft.automation/automationAccounts/certificates** típusúak, és a következő struktúrával rendelkeznek. Ez magában foglalja a gyakori változókat és paramétereket, így ezt a kódrészletet átmásolhatja és beillesztheti a megoldásfájlba, és módosíthatja a paraméterneveket. 

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



A Tanúsítványok erőforrások tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| base64Érték |A tanúsítvány 64-es alapértéke. |
| Ujjlenyomat |Ujjlenyomat a tanúsítványhoz. |



## <a name="credentials"></a>Hitelesítő adatok
[Az Azure Automation hitelesítő adatai](../../automation/automation-credentials.md) **microsoft.automation/automationAccounts/credentials** típussal rendelkeznek, és a következő struktúrával rendelkeznek.  Ez magában foglalja a gyakori változókat és paramétereket, így ezt a kódrészletet átmásolhatja és beillesztheti a megoldásfájlba, és módosíthatja a paraméterneveket. 


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

A hitelesítő adatok tulajdonságai az alábbi táblázatban találhatók.

| Tulajdonság | Leírás |
|:--- |:--- |
| userName (Felhasználónév) |A hitelesítő adat felhasználóneve. |
| jelszó |A hitelesítő adatok jelszava. |


## <a name="schedules"></a>Ütemezések
[Az Azure Automation-ütemezések](../../automation/automation-schedules.md) **microsoft.automation/automationAccounts/schedules** típussal rendelkeznek, és a következő struktúrával rendelkeznek. Ez magában foglalja a gyakori változókat és paramétereket, így ezt a kódrészletet átmásolhatja és beillesztheti a megoldásfájlba, és módosíthatja a paraméterneveket. 

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

Az ütemezési erőforrások tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| leírás |Az ütemezés nem kötelező leírása. |
| startTime |Az ütemezés datetime objektumként való kezdési időpontját adja meg. A karakterlánc akkor adható meg, ha érvényes DateTime-ra konvertálható. |
| engedélyezve van |Itt adható meg, hogy az ütemezés engedélyezve van-e. |
| interval |Az ütemezés intervallumának típusa.<br><br>nap<br>hour |
| frequency |Az a gyakoriság, hogy az ütemtervnek napok vagy órák száma szerint kell elsütnie. |

Az ütemezések kezdési időpontjának az aktuális időnél nagyobb értékkel kell rendelkeznie.  Ezt az értéket nem adhatja meg változóval, mivel nem tudhatja, hogy mikor lesz telepítve.

Használja az alábbi két stratégia egyikét, ha ütemezési erőforrásokat használ egy megoldásban.

- Használjon paramétert az ütemezés kezdési időpontjához.  Ez arra kéri a felhasználót, hogy adjon meg egy értéket a megoldás telepítésekor.  Ha több ütemezése van, egynél több paraméterértéket is használhat.
- Hozza létre az ütemezéseket egy runbook használatával, amely a megoldás telepítésekor kezdődik.  Ez megszünteti a felhasználó idő megadásának követelményét, de nem tartalmazhatja az ütemezést a megoldásban, így a megoldás eltávolításakor el lesz távolítva.


### <a name="job-schedules"></a>Feladatütemezések
A feladatütemezési erőforrások egy runbookot egy ütemezéssel kapcsolnak össze.  A **Microsoft.Automation/automationAccounts/jobSchedules** típussal rendelkeznek, és a következő struktúrával rendelkeznek.  Ez magában foglalja a gyakori változókat és paramétereket, így ezt a kódrészletet átmásolhatja és beillesztheti a megoldásfájlba, és módosíthatja a paraméterneveket. 

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


A munkaütemezések tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| ütemezés neve |Egyetlen **nevű** entitás az ütemezés nevével. |
| runbook neve  |Egyetlen **nevű** entitás a runbook nevével.  |



## <a name="variables"></a>Változók
[Az Azure Automation-változók](../../automation/automation-variables.md) **microsoft.automation/automationAccounts/variables** típussal rendelkeznek, és a következő struktúrával rendelkeznek.  Ez magában foglalja a gyakori változókat és paramétereket, így ezt a kódrészletet átmásolhatja és beillesztheti a megoldásfájlba, és módosíthatja a paraméterneveket.

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
| leírás | A változó nem kötelező leírása. |
| isEncrypted | Itt adható meg, hogy a változót titkosítsa-e. |
| type | Ennek a tulajdonságnak jelenleg nincs hatása.  A változó adattípusát a kezdeti érték határozza meg. |
| érték | A változó értéke. |

> [!NOTE]
> A **típustulajdonság** jelenleg nincs hatással a létrehozás alatt álló változóra.  A változó adattípusát az érték határozza meg.  

Ha a változó kezdeti értékét állítja be, azt a megfelelő adattípusként kell konfigurálni.  Az alábbi táblázat a különböző engedélyezett adattípusokat és azok szintaxisát tartalmazza.  Ne feledje, hogy a JSON értékei mindig idézőjelek közé kerülnek, az idézőjelekben lévő speciális karakterekkel.  Például egy karakterlánc-értéket idézőjelek határoznak meg a karakterlánc\\körül (az escape karaktert ( )), míg egy numerikus értéket egy idézőjel-készlettel kell megadni.

| Adattípus | Leírás | Példa | Feloldódik |
|:--|:--|:--|:--|
| sztring   | Az értéket idézőjelek közé kell tetszetős zárdabe.  | "\"Helló\"világ " | "Helló világ" |
| numerikus  | Számérték egyszeres idézőjelekkel.| "64" | 64 |
| logikai  | **igaz** vagy **hamis** idézőjelben.  Ne feledje, hogy ennek az értéknek kisbetűsnek kell lennie. | "igaz" | igaz |
| dátum/idő | Szerializált dátumérték.<br>A PowerShell ConvertTo-Json parancsmagjával létrehozhatja ezt az értéket egy adott dátumhoz.<br>Példa: get-date "5/24/2017 13:14:57" \| ConvertTo-Json | "\\/Dátum(1495656897378)\\/" | 2017-05-24 13:14:57 |

## <a name="modules"></a>Modulok
A felügyeleti megoldás nak nem kell meghatároznia a runbookok által használt [globális modulokat,](../../automation/automation-integration-modules.md) mert azok mindig elérhetők lesznek az Automation-fiókban.  A runbookok által használt bármely más modulhoz erőforrást kell megadnia.

[Az integrációs modulok](../../automation/automation-integration-modules.md) **microsoft.automation/automationaccounts/modules típusúak,** és a következő struktúrával rendelkeznek.  Ez magában foglalja a gyakori változókat és paramétereket, így ezt a kódrészletet átmásolhatja és beillesztheti a megoldásfájlba, és módosíthatja a paraméterneveket.

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


A modulerőforrások tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| contentLink |A modul tartalmát adja meg. <br><br>uri - Uri a modul tartalmához.  Ez egy .ps1 fájl lesz a PowerShell és a Script runbookok számára, és egy exportált grafikus runbook fájl egy Graph runbookhoz.  <br> verzió - Változata a modul a saját követés. |

A runbook függ a modul erőforrás annak érdekében, hogy a runbook előtt jön létre.

### <a name="updating-modules"></a>Modulok frissítése
Ha olyan felügyeleti megoldást frissít, amely ütemezést használó runbookot tartalmaz, és a megoldás új verziója rendelkezik a runbook által használt új modullal, akkor a runbook a modul régi verzióját használhatja.  A következő runbookok a megoldásban, és hozzon létre egy feladatot futtatni őket, mielőtt bármely más runbookok.  Ez biztosítja, hogy a modulok szükség szerint frissüljenek a runbookok betöltése előtt.

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/) biztosítja, hogy a megoldásban a runbookok által használt összes modul a legújabb verzió.  
* [A ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/) újraregisztrálja az összes ütemezési erőforrást, hogy a hozzájuk kapcsolódó runbookok a legújabb modulokat használják.




## <a name="sample"></a>Sample
Az alábbiakban egy megoldásminta látható, amely a következő erőforrásokat tartalmazza:

- Forgatókönyv.  Ez egy nyilvános GitHub-tárházban tárolt minta runbook.
- Automation-feladat, amely elindítja a runbook, amikor a megoldás telepítve van.
- Ütemezés és feladatütemezés a runbook rendszeres időközönkénti elindításához.
- Tanúsítvány.
- Megbízólevél.
- Változó.
- Modul.  Ez az [OMSIngestionAPI modul](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) adatok írásához a Log Analytics. 

A minta [szabványos megoldás paraméterváltozókat]( solutions-solution-file.md#parameters) használ, amelyeket általában egy megoldásban használnak, szemben az erőforrás-definíciók ban szereplő hardcoding értékekkel.


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
* Az összegyűjtött adatok megjelenítéséhez [adjon hozzá egy nézetet a megoldáshoz.]( solutions-resources-views.md)
