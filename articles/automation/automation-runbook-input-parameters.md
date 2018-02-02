---
title: "Runbook-bemeneti paraméterekhez |} Microsoft Docs"
description: "Forgatókönyv bemeneti paraméterei a rugalmasabb, a runbookok a azáltal, hogy mechanizmusok adatok átadására a runbook, amikor elindul. Ez a cikk ismerteti a különböző alkalmazási helyzetek, amely bemeneti paraméterek runbookok használják."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 4d3dff2c-1f55-498d-9a0e-eee497e5bedb
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: sngun
ms.openlocfilehash: 2934257e6feb6836492a4957e976abd02df12cfd
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="runbook-input-parameters"></a>Runbook bemeneti paraméterei

Forgatókönyv bemeneti paraméterei a rugalmasabb, a runbookok a azáltal, hogy mechanizmusok adatok átadására rá, amikor elindul. A paraméterek lehetővé teszik, hogy a runbook műveleteket, meghatározott forgatókönyvek és környezetekhez felvenne. Ebben a cikkben, bízná különböző alkalmazási helyzetek runbookok helyének bemeneti paraméterek.

## <a name="configure-input-parameters"></a>A bemeneti paraméterek konfigurálása

A bemeneti paraméterek PowerShell, a PowerShell munkafolyamat, a Python és a grafikus forgatókönyvek konfigurálható. Egy runbook is rendelkezik különböző adattípusokkal több paramétert, vagy nincsenek paraméterei egyáltalán. Kötelező vagy választható lehet bemeneti paramétereket, és hozzá lehet rendelni egy választható paramétereik alapértelmezett értéke. A rendelkezésre álló módszerek egyikével indításakor, értékek rendelhet egy runbookhoz tartozó bemeneti paraméterek. Ezek a metódusok lehetnek egy runbook indítása a portál vagy webszolgáltatás futhat. Akkor is elindíthatja, mint amit a gyermekrunbook beágyazottan is nevezett egy másik runbook.

## <a name="configure-input-parameters-in-powershell-and-powershell-workflow-runbooks"></a>Konfigurálhatja a bemeneti paramétereket a PowerShell és a PowerShell-munkafolyamati forgatókönyvek

PowerShell és [PowerShell munkafolyamat-forgatókönyvekről](automation-first-runbook-textual.md) az Azure Automationben támogatja a következő attribútumok keresztül meghatározott bemeneti paraméterek:  

| **Tulajdonság** | **Leírás** |
|:--- |:--- |
| Típus |Kötelező. A paraméter értéke a várt adattípus. A .NET-típus érvénytelen. |
| Name (Név) |Kötelező. A paraméter neve. Ez a runbookon belül egyedi és is csak betűket, számokat, vagy aláhúzás karaktereket tartalmazhat. Betűvel kell kezdődnie. |
| Kötelező |Választható. Meghatározza, hogy értéket kell megadni a paraméterben. Ha ez **$true**, akkor meg kell adni egy értéket, a runbook indításakor. Ha ez **$false**, majd egy értéket nem kötelező megadni. |
| Alapértelmezett érték |Választható. Adja meg egy értéket, amely a paraméter akkor használatos, ha egy érték nem kapott, a runbook indításakor. Alapértelmezett érték bármelyik paraméternél állítható be, és automatikusan teszik a paraméter nem kötelező, függetlenül a kötelező beállítás. |

Windows PowerShell támogatja a bemeneti paraméterek további attribútumok azokat az itt felsorolt, érvényesítése, például aliasokat, és a paraméter állandóként állítja be. Azure Automation jelenleg csak az előző bemeneti paramétereket támogatja.

A paraméterek definícióját a PowerShell-munkafolyamati forgatókönyvek formátuma a következő általános, ahol több paraméter vesszővel kell elválasztani.

   ```powershell
     Param
     (
         [Parameter (Mandatory= $true/$false)]
         [Type] Name1 = <Default value>,

         [Parameter (Mandatory= $true/$false)]
         [Type] Name2 = <Default value>
     )
   ```

> [!NOTE]
> Ha amely akkor paraméterek, ha nem adja meg a **kötelező** attribútum, akkor alapértelmezés szerint a paraméter nem kötelező. Is, ha a paraméter alapértelmezett értékét a PowerShell munkafolyamat-forgatókönyvekről, akkor a rendszer PowerShell egy nem kötelező paraméter, függetlenül attól, hogy a **kötelező** attribútum értéke.
> 
> 

Tegyük fel pedig konfiguráljuk egy PowerShell-munkafolyamati forgatókönyv, amely virtuális gépeket, vagy egy virtuális vagy az erőforráscsoporton belül minden virtuális gép adatait a bemeneti paraméterek. Ez a forgatókönyv két paraméterrel rendelkezik, az alábbi képernyőfelvételen látható módon: a nevét, a virtuális gép és az erőforráscsoport nevét.

![Automatizálás PowerShell-munkafolyamat](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

Ez a paraméter-definícióban a paraméterek **$VMName** és **$resourceGroupName** egyszerű paraméterek karakterlánc típusú. Azonban PowerShell és a PowerShell-munkafolyamati forgatókönyvek támogatja az egyszerű típusok és a komplex típusok, például a **objektum** vagy **PSCredential** -bemeneti paraméterekhez.

Ha a runbookban egy objektum típusú bemeneti paraméter, majd használja a PowerShell kivonattáblaként (név, érték) párral értékét. Például ha egy runbook a következő paramétert:

     [Parameter (Mandatory = $true)]
     [object] $FullName

A következő érték akkor a paraméter is továbbítja:

    @{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}


## <a name="configure-input-parameters-in-graphical-runbooks"></a>Grafikus forgatókönyvek bemeneti paramétereinek a konfigurálása

A [konfigurálása egy grafikus forgatókönyvnek](automation-first-runbook-graphical.md) bemeneti paraméterek, hozzon létre egy grafikus forgatókönyv, amely virtuális gépek adatait vagy egy virtuális vagy virtuális gépeinek erőforráscsoporton belül. Egy runbook konfigurálása áll két fő tevékenység az alább ismertetett.

[**Azure-beli futtató fiókot a Runbookok hitelesítést** ](automation-sec-configure-azure-runas-account.md) Azure szolgáltatással való hitelesítésre.

[**Get-AzureRmVm** ](https://msdn.microsoft.com/library/mt603718.aspx) a virtuális gépek tulajdonságait.

Használhatja a [ **Write-Output** ](https://technet.microsoft.com/library/hh849921.aspx) tevékenység kimeneti virtuális gépek nevét. A tevékenység **Get-AzureRmVm** két paramétert fogad a **virtuálisgép-nevet** és a **erőforráscsoport-név**. Mivel ezek a paraméterek a runbook minden egyes indításakor igényelhet eltérő értékek tartoznak, a runbookban is hozzáadhat bemeneti paraméterek. A bemeneti paraméterek hozzáadása lépései a következők:

1. Válassza ki a grafikus forgatókönyvnek a **Runbookok** panel megnyitásához, és kattintson [ **szerkesztése** ](automation-graphical-authoring-intro.md) azt.
2. A forgatókönyv-szerkesztőben kattintson **bemeneti és kimeneti** megnyitásához a **bemeneti és kimeneti** panelen.
   
    ![Grafikus Automation-forgatókönyv](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)
3. A **bemeneti és kimeneti** panel a runbook meghatározott bemeneti paraméterek listáját jeleníti meg. A panel új bemeneti paraméter hozzáadása vagy szerkesztése meglévő bemeneti paraméter konfigurációját. A runbook egy új paraméter hozzáadásához kattintson **bemenet hozzáadása** megnyitásához a **Runbook bemeneti paraméter** panelen. Konfigurálhatja, a következő paraméterekkel:
   
   | **Tulajdonság** | **Leírás** |
   |:--- |:--- |
   | Name (Név) |Kötelező. A paraméter neve. Ez a runbookon belül egyedi és is csak betűket, számokat, vagy aláhúzás karaktereket tartalmazhat. Betűvel kell kezdődnie. |
   | Leírás |Választható. Leírás a bemeneti paraméter céljáról. |
   | Típus |Választható. Az adattípus, amely a paraméterérték várt. Támogatott paramétertípusa **karakterlánc**, **Int32**, **Int64**, **decimális**, **logikai**,  **Dátum és idő**, és **objektum**. Ha adattípus nincs megadva, alapértelmezés szerint a **karakterlánc**. |
   | Kötelező |Választható. Meghatározza, hogy értéket kell megadni a paraméterben. Ha úgy dönt, **Igen**, akkor meg kell adni egy értéket, a runbook indításakor. Ha úgy dönt, **nem**, majd kitöltése nem kötelező a forgatókönyv indításakor, és egy alapértelmezett érték is beállítható. |
   | Alapértelmezett érték |Választható. Adja meg egy értéket, amely a paraméter akkor használatos, ha egy érték nem kapott, a runbook indításakor. Alapértelmezett érték a nem kötelező paraméter állítható be. Alapértelmezett érték beállításához válassza **egyéni**. Ezt az értéket használja, ha egy másik érték lett megadva, a runbook indításakor. Válasszon **nincs** Ha nem kívánja minden alapértelmezett értéket. |
   
    ![Új bemenet hozzáadása](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Hozzon létre két paramétert a következő tulajdonságokkal, melynek használatával a **Get-AzureRmVm** tevékenység:
   
   * **Parameter1:**
     
     * Név – VMName
     * -Karakterlánc típusú
     * Kötelező – nem
   * **2:**
     
     * Név - erőforráscsoport-név
     * -Karakterlánc típusú
     * Kötelező – nem
     * Alapértelmezett érték – egyéni
     * Egyéni alapértelmezett érték – \<a virtuális gépeket tartalmazó erőforráscsoport neve >
5. Miután hozzáadta a paramétereket, kattintson a **OK**. Most már megtekintheti azokat a **bemeneti és kimeneti panel**. Kattintson a **OK** újra, és kattintson a **mentése** és **közzététel** a runbookban.

## <a name="configure-input-parameters-in-python-runbooks"></a>Python runbookok bemeneti paramétereinek a konfigurálása

Ellentétben a PowerShell, a PowerShell munkafolyamatok és a grafikus forgatókönyvek Python runbookok elnevezett paraméterek nem hajtja végre.
Az összes bemeneti paraméterek argumentum értékeket egy tömbként elemzésének.
A tömb elérheti importálásával a `sys` modult a Python-parancsfájl, és ezután használatával a `sys.argv` tömb.
Fontos megjegyezni, hogy a tömb első eleme `sys.argv[0]`, a parancsfájl neve van, így az első tényleges bemeneti paraméter `sys.argv[1]`.

Például egy bemeneti paraméterek használata a Python runbookokban, lásd: [az első Python-runbook az Azure Automationben](automation-first-runbook-textual-python2.md).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>A bemeneti paraméterek runbookok megadása

Átadhatók értékek a bemeneti paraméterek runbookok a következő esetekben:

### <a name="start-a-runbook-and-assign-parameters"></a>Elindítja a runbookot, és rendelje hozzá a paraméterek

Egy runbook indítható számos módja: az Azure-portálon, és olyan webhook, a PowerShell-parancsmagokkal, a REST API-t vagy az SDK-val. Az alábbiakban arról lesz szó különböző módszereket a runbook indítása, és rendelje hozzá a paramétereket.

#### <a name="start-a-published-runbook-by-using-the-azure-portal-and-assign-parameters"></a>Az Azure-portál használatával indítsa el a közzétett runbookok, és rendelje hozzá a paraméterek

Ha Ön [elindítja a runbookot](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal), a **runbook meghívása** panel nyílik meg, és megadhatja az értékeket a paraméterek létrehozott.

![A portál használatának megkezdése](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

A címke alatt a beviteli mezőbe lásd: a paraméter beállított attribútumait. Attribútumok kötelező vagy választható, típusa és alapértelmezett értékét tartalmazza. A paraméter neve melletti súgó buborék megtekintheti a kulcs adatainak meg kell győződnie döntéseket hozzanak arról, hogy a paraméter bemeneti értékeket. Ezen információk közé tartozik, hogy a paraméter kötelező vagy választható. A típus és alapértelmezett értéket (ha van ilyen) és más hasznos megjegyzéseket is tartalmaz.

> [!NOTE]
> Karakterlánc típusú paramétereket támogatási **üres** karakterlánc-értékek.  Belépés **[EmptyString]** mezőben a bemeneti paraméter üres karakterlánc megfelel a paraméter. Ezenkívül nem támogatják a karakterlánc típusú paraméterrel **Null** átadta-e értékeket. Ha bármely érték nem adhatók át a karakterlánc-paramétert, majd PowerShell értelmezi null értékű.
> 
> 

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>Indítsa el a közzétett runbookok PowerShell-parancsmagok használatával, és rendelje hozzá a paraméterek

* **Az Azure Resource Manager parancsmagjainak:** elindíthatja egy hozott létre egy erőforráscsoportot az Automation-runbook [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx).
  
  **Példa**
  
  ```
  $params = @{“VMName”=”WSVMClassic”;”resourceGroupeName”=”WSVMClassicSG”}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” –ResourceGroupName $resourceGroupName -Parameters $params
  ```
* **Az Azure klasszikus telepítési modell parancsmagok:** elindíthatja az automation-forgatókönyv használatával létrehozott egy alapértelmezett erőforráscsoportban [Start-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690259.aspx).
  
  **Példa**
  
  ```
  $params = @{“VMName”=”WSVMClassic”; ”ServiceName”=”WSVMClassicSG”}
  
  Start-AzureAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” -Parameters $params
  ```

> [!NOTE]
> Amikor elindít egy runbookot PowerShell-parancsmagok, egy alapértelmezett paraméter használatával **MicrosoftApplicationManagementStartedBy** jön létre a értékű **PowerShell**. Megtekintheti a paraméter a **feladat részletei** panelen.  
> 
> 

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>Az SDK használatával indítsa el a runbookot, és rendelje hozzá a paraméterek

* **Az Azure Resource Manager-metódus:** runbook elindíthatja az SDK-t egy programozási nyelv használatával. Alatt van egy C# kódrészletet a runbook indítása az Automation-fiókban. Megtekintheti, a kódot a [GitHub-tárházban](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  
  
  ```
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
  ```
* **Az Azure klasszikus telepítési modell metódus:** runbook elindíthatja az SDK-t egy programozási nyelv használatával. Alatt van egy C# kódrészletet a runbook indítása az Automation-fiókban. Megtekintheti, a kódot a [GitHub-tárházban](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).
  
  ```      
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
  ```
  
  Ez a metódus indításához a runbook paramétereinek tárolására szótár **VMName** és **resourceGroupName**, és azok értékeit. Ezután elindítja a runbookot. A C# kódrészletet a fent megadott metódust az alábbiakban található.
  
  ```
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook(“Get-AzureVMGraphical”, RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>Runbook indítása a REST API használatával, és rendelje hozzá a paraméterek
A runbook-feladatok létrehozhatók és lépések az Azure Automation REST API használatával a **PUT** metódus a következő kérelem URI-azonosítója:

    https://management.core.windows.net/<subscription-id>/cloudServices/<cloud-service-name>/resources/automation/~/automationAccounts/<automation-account-name>/jobs/<job-id>?api-version=2014-12-08`

A kérelem URI-azonosítója cserélje le a következő paraméterekkel:

* **előfizetés-azonosító:** az Azure-előfizetés-azonosítójával.  
* **felhő szolgáltatásnév:** a nevét, a felhőalapú szolgáltatás, amelyre a kérelmet küldeni kell.  
* **Automation-fiók-name:** , amely a megadott felhő-szolgáltatáson belül az automation-fiók nevét.  
* **azonosítójú feladat:** a feladathoz tartozó GUID. GUID azonosítók a PowerShell használatával hozhatók létre a **[GUID]::NewGuid(). ToString()** parancsot.

Ahhoz, hogy a paraméterek átadása a runbook-feladat, használja a kérés törzsében. A következő két tulajdonságait JSON formátumban megadott tart:

* **Runbook neve:** szükséges. A feladat elindítása a runbook neve.  
* **Runbook-paraméterek:** nem kötelező. A paraméterlista (név, érték) álló dictionary formátumban, ahol neve karakterlánc típusúnak kell lennie, és értéke lehet bármely érvényes JSON-érték.

Ha el szeretné indítani a **Get-AzureVMTextual** hoztak létre korábbi runbook **VMName** és **resourceGroupName** paraméterek, használja a következő JSON formátummal a a kérés törzsében.

   ```
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WSVMClassic",
         "resourceGroupName":”WSCS1”}
        }
    }
   ```

201-es HTTP-állapotkódot eredményül, ha a feladat sikeresen létrehozva. A válaszfejlécek és az adott válasz törzsének további információkért lásd: a cikk kapcsolatos [egy runbook-feladat létrehozása a REST API használatával.](https://msdn.microsoft.com/library/azure/mt163849.aspx)

### <a name="test-a-runbook-and-assign-parameters"></a>Egy runbook tesztelése, és rendelje hozzá a paraméterek
Ha Ön [tesztelése a runbook vázlatverzióját](automation-testing-runbook.md) a vizsgálat lehetőséget a **tesztelése** lap nyílik meg, és beállíthatja, hogy a létrehozott paraméter értékét.

![Tesztelje, és rendelje hozzá a paraméterek](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Ütemezés összekapcsolása runbookkal, és rendelje hozzá a paraméterek
Is [összekapcsolhat egy ütemezést](automation-schedules.md) a forgatókönyvhöz való úgy, hogy a runbook egy adott időpontban. Ha az ütemezés létrehozása, és a runbook ezeket az értékeket használja, amikor elindul az ütemezés szerint rendelhet bemeneti paraméterek. Az ütemezés nem menthető, amíg az összes kötelező paraméter az értékek.

![Az ütemezés és a hozzárendelés paraméterek](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>A webhook egy runbook létrehozása és hozzárendelése paraméterek
Létrehozhat egy [webhook](automation-webhooks.md) a runbook és konfigurálhatja a runbook bemeneti paramétereket. A webhook nem menthetők, amíg az összes kötelező paraméter az értékek.

![Webhook létrehozása és hozzárendelése paraméterek](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Ha olyan webhook, az előre meghatározott bemeneti paraméter használatával runbookot végrehajtani  **[Webhookdata](automation-webhooks.md#details-of-a-webhook)**  , együtt küldött, amelyet a bemeneti paraméterek. Kattintva bontsa ki a **WebhookData** paraméter további részleteket.

![WebhookData parameter](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="next-steps"></a>További lépések
* A forgatókönyv bemeneti és kimeneti további információkért lásd: [Azure Automation: runbook bemeneti, kimeneti és egymásba ágyazott runbookok](https://azure.microsoft.com/blog/azure-automation-runbook-input-output-and-nested-runbooks/).
* Runbook-indítási módok részletekért lásd: [runbook indítása](automation-starting-a-runbook.md).
* Szöveges forgatókönyvként szerkesztéséhez hivatkoznak [szöveges runbookok szerkesztéséhez](automation-edit-textual-runbook.md).
* Grafikus runbook szerkesztése, tekintse meg [grafikus készítése az Azure Automationben](automation-graphical-authoring-intro.md).

