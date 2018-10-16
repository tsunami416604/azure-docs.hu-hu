---
title: Runbook bemeneti paraméterei
description: Runbook bemeneti paramétereinek növelje a rugalmasságot, a runbookok adatok átadására a runbook indításakor, így. Ez a cikk ismerteti a különböző helyzetekhez, ahol használt bemeneti paraméterek runbookok.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 941a7242e1f6fddd1ff91721141be4e1f9816b31
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344863"
---
# <a name="runbook-input-parameters"></a>Runbook bemeneti paraméterei

Runbook bemeneti paramétereinek növelheti a rugalmasságot, a runbookok azáltal, hogy át adatokat, amikor elindul. A paraméterek a runbook-műveletek kapjon az adott forgatókönyveket és a környezetek engedélyezése. Ebben a cikkben vezeti végig a különböző helyzetekhez ahol használt bemeneti paraméterek runbookok.

## <a name="configure-input-parameters"></a>Bemeneti paramétereinek konfigurálása

Bemeneti paramétereket a PowerShell, a PowerShell-munkafolyamat, a Python és a grafikus runbookok konfigurálható. Egy runbook rendelkezhet több paraméterek különböző adattípusokkal, vagy nincsenek paraméterei egyáltalán. Bemeneti paraméterek lehet kötelező vagy nem kötelező, és hozzárendelheti a választható paraméterek alapértelmezett értékét. Runbook bemeneti paramétereinek keresztül a rendelkezésre álló módszerek közül az indításakor rendelhet értékeket. Ezek a metódusok lehetnek egy runbook indítása a portálról vagy a webszolgáltatások. Egy beágyazott egy másik runbook meghívott gyermekrunbookok szerint is elindíthatja.

## <a name="configure-input-parameters-in-powershell-and-powershell-workflow-runbooks"></a>A PowerShell és a PowerShell-munkafolyamati runbookok bemeneti paramétereinek konfigurálása

PowerShell és [PowerShell-munkafolyamati runbookok](automation-first-runbook-textual.md) az Azure Automation segítségével a következő attribútumok meghatározott bemeneti paraméterek támogatja:  

| **Tulajdonság** | **Leírás** |
|:--- |:--- |
| Típus |Kötelező. A paraméter értéke a várt adattípus. Minden olyan .NET-típus érvénytelen. |
| Name (Név) |Kötelező. A paraméter neve. Ez kell lenniük a runbookon belül és csak betűket, számokat, vagy aláhúzás karaktereket. Egy betűvel kell kezdődnie. |
| Kötelező |Választható. Itt adhatja meg, hogy értéket kell adni a paraméter. Ha azonosítással **$true**, majd egy értéket meg kell adni a forgatókönyv elindulásakor. Ha azonosítással **$false**, majd egy értéket nem kötelező. |
| Alapértelmezett érték |Választható. Itt adható meg egy értéket, amely a paraméter akkor használatos, ha egy érték nem átadott, a forgatókönyv elindulásakor. Egy alapértelmezett értéket állíthat be minden paramétert, és is automatikusan végrehajtja a paraméter nem kötelező, függetlenül a kötelező beállítás. |

Windows PowerShell támogatja a további attribútumok bemeneti paramétereket, mint azok szerepel itt, például érvényesítési, aliasok, és paraméter állítja be. Azure Automation jelenleg csak az előző bemeneti paramétereket támogatja.

A PowerShell-munkafolyamati runbookok egy paraméterdefinícióhoz rendelkezik az alábbi általános űrlapot, ahol több paraméterek vesszővel kell elválasztani.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

> [!NOTE]
> Ha most definiálja, paraméterek, ha nem adja meg a **kötelező** attribútumot, akkor alapértelmezés szerint a paraméter megadása nem kötelező. Is, ha PowerShell-munkafolyamati runbookok be egy paraméter alapértelmezett értékét, akkor számít PowerShell egy nem kötelező paraméter, függetlenül attól, hogy a **kötelező** attribútum értéke.
> 
> 

Tegyük fel konfiguráljuk a bemeneti paramétereit egy PowerShell-munkafolyamati forgatókönyv, amely virtuális gépeket, vagy az egyetlen virtuális Gépet, vagy az erőforráscsoporton belül minden virtuális gép adatait. Ez a forgatókönyv két paraméterrel rendelkezik, az alábbi képernyőképen látható módon: neve a virtuális gép és az erőforráscsoport nevét.

![Automation PowerShell-munkafolyamat](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

Ez a paraméter a definíció, a paraméterek a **$VMName** és **$resourceGroupName** karakterlánc típusú egyszerű paraméterek. Azonban, PowerShell és a PowerShell-munkafolyamati runbookok támogatja az egyszerű típusok és a komplex típusok, például **objektum** vagy **PSCredential** -bemeneti paraméterekhez.

Ha a runbook rendelkezik egy objektum típusú bemeneti paraméter, használja a PowerShell kivonattáblaként (név, érték) párok ad meg értéket. Például ha egy runbook a következő paramétert:

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

Ezután adhat át a paramétert a következő értéket:

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```
> [!NOTE]
> Amikor átadhatja nincs érték egy nem kötelező `[String]` írja be a paraméter, amely rendelkezik egy _alapértelmezett érték_ , `$null`, akkor a paraméter értéke lesz egy _üres karakterlánc_, **nem** `$null`.
> 
> 

## <a name="configure-input-parameters-in-graphical-runbooks"></a>Grafikus runbook bemeneti paramétereinek konfigurálása

A [grafikus runbook konfigurálása](automation-first-runbook-graphical.md) bemeneti paraméterek, hozzunk létre, amely virtuális gépek adatait vagy a grafikus runbookok egyetlen virtuális gép vagy a virtuális gépek erőforráscsoporton belül. Egy runbook konfigurálása olyan tevékenységekből állnak, két fő, az alább ismertetett.

[**Runbookok hitelesítése Azure-beli futtató fiók** ](automation-sec-configure-azure-runas-account.md) Azure-hitelesítésre.

[**Get-AzureRmVm** ](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvm) a virtuális gép tulajdonságainak beolvasása.

Használhatja a [ **Write-Output** ](https://technet.microsoft.com/library/hh849921.aspx) tevékenység kimeneti a virtuális gépek nevét. A tevékenység **Get-AzureRmVm** két paramétert fogad a **virtuális gép neve** és a **erőforráscsoport-név**. Ezeket a paramétereket a runbook minden alkalommal, amikor szükségük különböző értékeket, mert a runbook bemeneti paraméterei is hozzáadhat. Bemeneti paraméterek hozzáadása lépései a következők:

1. Válassza ki a grafikus runbookok a **Runbookok** panelen, majd kattintson [ **szerkesztése** ](automation-graphical-authoring-intro.md) azt.
2. A runbook-szerkesztőben kattintson **bemeneti és kimeneti** megnyitásához a **bemeneti és kimeneti** panelen.
   
    ![Automation grafikus forgatókönyv](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)
3. A **bemeneti és kimeneti** panel a runbook definiált bemeneti paraméterek listáját jeleníti meg. Ezen a panelen adjon hozzá egy új bemeneti paramétert, vagy szerkesztheti a meglévő bemeneti paraméter konfigurációját. A runbook paraméter hozzáadásához kattintson **bemenet hozzáadása** megnyitásához a **forgatókönyv bemeneti paramétere** panelen. Itt állíthatja be a következő paraméterekkel:
   
   | **Tulajdonság** | **Leírás** |
   |:--- |:--- |
   | Name (Név) |Kötelező. A paraméter neve. Ez kell lenniük a runbookon belül és csak betűket, számokat, vagy aláhúzás karaktereket. Egy betűvel kell kezdődnie. |
   | Leírás |Választható. Bemeneti paraméter céljáról szóló leírást. |
   | Típus |Választható. Az adattípus, amely a paraméter értéke a várt. Támogatott paramétertípusok a következők **karakterlánc**, **Int32**, **Int64**, **decimális**, **logikai**,  **Dátum és idő**, és **objektum**. Ha olyan adattípust nincs bejelölve, a rendszer alapértelmezés szerint **karakterlánc**. |
   | Kötelező |Választható. Itt adhatja meg, hogy értéket kell adni a paraméter. Ha úgy dönt, **Igen**, majd egy értéket meg kell adni a forgatókönyv elindulásakor. Ha úgy dönt, **nincs**, majd egy érték esetén nem szükséges a runbook elindult, és a egy alapértelmezett érték is beállítható. |
   | Alapértelmezett érték |Választható. Itt adható meg egy értéket, amely a paraméter akkor használatos, ha egy érték nem átadott, a forgatókönyv elindulásakor. Alapértelmezett érték beállítható, amely nem kötelező paraméter. Alapértelmezett érték beállításához válassza **egyéni**. Ezt az értéket használja, ha egy másik érték van megadva, a forgatókönyv elindulásakor. Válasszon **nincs** Ha nem szeretne minden olyan alapértelmezett értéket. |
   
    ![Új bemenet hozzáadása](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Hozzon létre két paramétert a következő tulajdonságokkal, amelyet a **Get-AzureRmVm** tevékenység:
   
   * **Parameter1:**
     
     * Név – VMName
     * Típus - karakterlánc
     * Kötelező – nem
   * **2:**
     
     * Név – resourceGroupName
     * Típus - karakterlánc
     * Kötelező – nem
     * Alapértelmezett érték – egyéni
     * Egyéni alapértelmezett érték - \<a virtuális gépeket tartalmazó erőforráscsoport neve >
5. Miután hozzáadta a paramétereket, kattintson a **OK**. Most már megtekintheti őket a **bemenet és Kimenet panelt**. Kattintson a **OK** újra, és kattintson a **mentése** és **közzététel** a runbookban.

## <a name="configure-input-parameters-in-python-runbooks"></a>Python-runbook bemeneti paramétereinek konfigurálása

Ellentétben a PowerShell, a PowerShell-munkafolyamat és a grafikus runbookok Python-runbookok nem lépnek elnevezett paraméterek.
Az összes bemeneti paraméterek argumentumértékként tömbjeként elemzi.
A tömb importálásával fér hozzá a `sys` modult a Python-szkriptet, és ezután használatával a `sys.argv` tömb.
Fontos megjegyezni, hogy a tömb első eleme `sys.argv[0]`, a parancsfájl neve van, ezért az első tényleges bemeneti paraméter `sys.argv[1]`.

Python-runbook bemeneti paramétereinek használata egy példa: [első Python-runbookom az Azure Automationben](automation-first-runbook-textual-python2.md).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Rendelje hozzá a bemeneti paramétereket a runbookok értékek

Átadhatók értékek bemeneti paramétereket a runbookok a következő esetekben:

### <a name="start-a-runbook-and-assign-parameters"></a>Indítson el egy runbookot, és rendelje hozzá a paraméterek

Egy runbook indítható sokféleképpen: az Azure Portalon, és a egy webhookot, a PowerShell-parancsmagokkal, a REST API-val vagy az SDK-val. Az alábbiakban bemutatjuk, a runbook elindítása és hozzárendelése a paraméterek különböző módszereket.

#### <a name="start-a-published-runbook-by-using-the-azure-portal-and-assign-parameters"></a>Indítsa el a közzétett runbookok az Azure portal használatával, és rendelje hozzá a paraméterek

Ha Ön [a runbook elindításához](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal), a **Runbook indítása** panel nyílik meg, és a paraméterek létrehozott értékeket adhat meg.

![A portál használatának megkezdése](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

A címke a beviteli mező alatt láthatja az attribútumokat, amelyek a paraméter lettek beállítva. Attribútumok például a kötelező vagy választható, típus és az alapértelmezett érték. A Súgó buborékra a paraméter neve mellett láthatja a legfontosabb információkat döntéseket hoz a paraméter bemeneti értékeket kell. Ezen információk közé tartozik-e a paraméter kötelező vagy nem kötelező. A típus és alapértelmezett értéket (ha van ilyen) és más hasznos megjegyzéseket is tartalmaz.

> [!NOTE]
> A karakterlánc típusú paramétereket támogatási **üres** karakterlánc-értékek.  Belépő **[EmptyString]** a bemeneti paraméter be az üres karakterlánc továbbítja a paraméter. Ezenkívül nem támogatják a karakterlánc típusú paramétereket **Null** átadott értékeket. Ha bármely érték a karakterlánc-paraméter nem adhatók át, majd PowerShell értelmezi azt null értékű.
> 
> 

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>Indítsa el a közzétett runbookok PowerShell-parancsmagok segítségével, és rendelje hozzá a paraméterek

* **Az Azure Resource Manager-parancsmagok:** elkezdheti az Automation-runbook használatával egy erőforráscsoportban létrehozott [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook).
  
  **Példa**
  
  ```powershell
  $params = @{“VMName”=”WSVMClassic”;”resourceGroupeName”=”WSVMClassicSG”}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” –ResourceGroupName $resourceGroupName -Parameters $params
  ```
* **Klasszikus Azure üzemi modell parancsmagok:** elkezdheti az automation-runbook használatával létrehozott egy alapértelmezett erőforráscsoportba tartozó [Start-AzureAutomationRunbook](https://docs.microsoft.com/powershell/module/servicemanagement/azure/start-azureautomationrunbook).
  
  **Példa**
  
  ```powershell
  $params = @{“VMName”=”WSVMClassic”; ”ServiceName”=”WSVMClassicSG”}
  
  Start-AzureAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” -Parameters $params
  ```

> [!NOTE]
> Ha a PowerShell-parancsmagok, az alapértelmezett paraméterek, elindít egy runbookot **MicrosoftApplicationManagementStartedBy** jön létre a következő értékkel **PowerShell**. Ezt a paramétert a is megtekintheti a **feladat részletei** panelen.  
> 
> 

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>Runbook indítása egy SDK-val, és rendelje hozzá a paraméterek

* **Az Azure Resource Manager-metódus:** elindíthat egy runbookot, az SDK-t egy programozási nyelv használatával. Alább láthat egy C# kódrészletet a runbook indítása az Automation-fiókban. Megtekintheti, a kód a [GitHub-adattár](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  
  
  ```csharp
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
* **Klasszikus Azure üzemi modell módszer:** elindíthat egy runbookot, az SDK-t egy programozási nyelv használatával. Alább láthat egy C# kódrészletet a runbook indítása az Automation-fiókban. Megtekintheti, a kód a [GitHub-adattár](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).
  
  ```csharp
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
  
  Indítsa el ezt a módszert, hozzon létre egy szótárban tárolja a runbook paramétereinek **VMName** és **resourceGroupName**, és azok értékeit. Indítsa el a runbookot. Az alábbi, a C# Fragment kódu Pro a fent meghatározott metódus hívása.
  
  ```csharp
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook(“Get-AzureVMGraphical”, RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>Runbook indítása a REST API-val, és rendelje hozzá a paraméterek
Runbook-feladatok létrehozhatók és az Azure Automation REST API használatának lépései a **PUT** metódust a következő kérés URI azonosítója:

    https://management.core.windows.net/<subscription-id>/cloudServices/<cloud-service-name>/resources/automation/~/automationAccounts/<automation-account-name>/jobs/<job-id>?api-version=2014-12-08`

A kérelem URI-t cserélje le a következő paraméterekkel:

* **előfizetés-azonosító:** az Azure-előfizetés azonosítóját.  
* **felhőalapú szolgáltatás neve:** nevét a felhőalapú szolgáltatás, amelyre a kérést küldeni kell.  
* **Automation-fiók-neve:** az automation-fiók a megadott felhőszolgáltatásban lévő üzemeltetett nevét.  
* **Feladatazonosító:** a feladathoz tartozó GUID. GUID, a PowerShell használatával lehet létrehozni a **[GUID]::NewGuid(). ToString()** parancsot.

Annak érdekében, hogy a runbook-feladat paraméterek adhatók át, használja a kérelem törzsében. Az alábbi két tulajdonságot a megadott JSON-formátumban szükséges:

* **Runbook neve:** szükséges. A feladat elindítása a runbook neve.  
* **Runbook-paraméterek:** nem kötelező. A paraméter (név, érték) listát tartalmazó ahol neve karakterlánc típusúnak kell lennie, és értéke lehet bármely érvényes JSON-érték formázása.

Ha el szeretné indítani a **Get-AzureVMTextual** a korábban létrehozott runbook **VMName** és **resourceGroupName** meg paraméterként, használja a következő JSON formátummal a a kérelem törzsében.

   ```json
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

Egy HTTP-állapotkód: 201-es ad vissza, ha a feladat sikeresen létrehozva. A válaszfejlécek és a válasz törzse további információkért lásd: a cikk kapcsolatos [runbook-feladat létrehozása a REST API használatával.](https://msdn.microsoft.com/library/azure/mt163849.aspx)

### <a name="test-a-runbook-and-assign-parameters"></a>Egy runbook tesztelése, és rendelje hozzá a paraméterek
Amikor Ön [tesztelése a runbook vázlatverzióját](automation-testing-runbook.md) a tesztelési lehetőség használatával a **tesztelése** lap nyílik meg, és beállíthatja, hogy az Ön által létrehozott paramétereihez tartozó értékek.

![Tesztelje, és rendelje hozzá a paraméterek](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Összekapcsolhat egy ütemezést egy runbookkal, és rendelje hozzá a paraméterek
Is [összekapcsolhat egy ütemezést](automation-schedules.md) a runbooknak úgy, hogy a runbook egy adott időpontban. Ha az ütemezés létrehozása, és a runbook ezeket az értékeket használja, amikor elindul az ütemezés szerint hozzárendelhet bemeneti paraméterek. Az ütemezés nem menthető, amíg az összes kötelező paraméter fel van tüntetve.

![Ütemezés és a hozzárendelés paraméterek](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Hozzon létre egy webhookot a runbookhoz, és rendelje hozzá a paraméterek
Létrehozhat egy [webhook](automation-webhooks.md) a runbookok és runbook bemeneti paramétereinek konfigurálása. A webhook nem lehet menteni, amíg az összes kötelező paraméter fel van tüntetve.

![Webhook létrehozása és hozzárendelése a paraméterek](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Amikor egy runbook végrehajtása egy webhookot, az előre meghatározott bemeneti paraméter használatával **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** , együtt küldött meghatározott bemeneti paraméterek. Kattintva bontsa ki a **WebhookData** paraméter további részletekért.

![WebhookData paraméter](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="next-steps"></a>További lépések
* A forgatókönyv-bemenet és kimenet további információkért lásd: [Azure Automation: runbook bemeneti, kimeneti és egymásba ágyazott runbookok](https://azure.microsoft.com/blog/azure-automation-runbook-input-output-and-nested-runbooks/).
* Módjai, indítson el egy runbookot, lásd: [runbook elindítása](automation-starting-a-runbook.md).
* Szöveges runbookok szerkesztése, tekintse meg [szöveges runbookok szerkesztése](automation-edit-textual-runbook.md).
* A grafikus runbookok szerkesztése, tekintse meg [grafikus létrehozás az Azure Automationben](automation-graphical-authoring-intro.md).

