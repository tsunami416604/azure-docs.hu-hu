---
title: Runbook bemeneti paraméterei
description: Runbook bemeneti paramétereinek növelje a rugalmasságot, a runbookok adatok átadására a runbook indításakor, így. Ez a cikk ismerteti a különböző helyzetekhez, ahol használt bemeneti paraméterek runbookok.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5f190d60a059108b9763f35e2ee8cf99ae77b694
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578152"
---
# <a name="runbook-input-parameters"></a>Runbook bemeneti paraméterei

Runbook bemeneti paramétereinek növelheti a rugalmasságot, a runbookok azáltal, hogy át adatokat, amikor elindul. A paraméterek a runbook-műveletek kapjon az adott forgatókönyveket és a környezetek engedélyezése. Ebben a cikkben vezeti végig a különböző helyzetekhez ahol használt bemeneti paraméterek runbookok.

## <a name="configure-input-parameters"></a>Bemeneti paramétereinek konfigurálása

Bemeneti paramétereket a PowerShell, a PowerShell-munkafolyamat, a Python és a grafikus runbookok konfigurálható. Egy runbook rendelkezhet több paraméterek különböző adattípusokkal, vagy nincsenek paraméterei egyáltalán. Bemeneti paraméterek lehet kötelező vagy nem kötelező, és nem kötelező paraméter alapértelmezett értékének rendelkezhet. Rendeljen hozzá értékek runbook bemeneti paramétereinek keresztül a rendelkezésre álló módszerek közül az indításakor. Ezek a metódusok lehetnek egy runbook indítása az Azure Portalon, a webszolgáltatás vagy a PowerShell. Egy beágyazott egy másik runbook meghívott gyermekrunbookok szerint is elindíthatja.

## <a name="configure-input-parameters-in-powershell-runbooks"></a>Bemeneti paramétereinek konfigurálása a PowerShell-forgatókönyvek

Az Azure Automation PowerShell és a PowerShell-munkafolyamati runbookok használatával a következő attribútumok meghatározott bemeneti paramétereket támogatja:  

| **Tulajdonság** | **Leírás** |
|:--- |:--- |
| `Type` |Kötelező. A paraméter értéke a várt adattípus. Minden olyan .NET-típus érvénytelen. |
| `Name` |Kötelező. A paraméter neve. Ez kell lenniük a runbookon belül és csak betűket, számokat, vagy aláhúzás karaktereket. Egy betűvel kell kezdődnie. |
| `Mandatory` |Választható. Itt adhatja meg, hogy értéket kell adni a paraméter. Ha azonosítással  **\$igaz**, majd egy értéket meg kell adni a forgatókönyv elindulásakor. Ha azonosítással  **\$hamis**, majd egy értéket nem kötelező. |
| `Default value` |Választható. Itt adható meg egy értéket, amely a paraméter akkor használatos, ha egy érték nem átadott, a forgatókönyv elindulásakor. Egy alapértelmezett értéket állíthat be minden paramétert, és is automatikusan végrehajtja a paraméter nem kötelező, függetlenül a kötelező beállítás. |

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

Tegyük fel konfiguráljuk a bemeneti paramétereit egy PowerShell-munkafolyamati forgatókönyv, amely virtuális gépeket, vagy az egyetlen virtuális Gépet, vagy az erőforráscsoporton belül minden virtuális gép adatait. Ez a forgatókönyv két paraméterrel rendelkezik, az alábbi képernyőképen látható módon: neve a virtuális gép és az erőforráscsoport nevét.

![Automation PowerShell-munkafolyamat](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

Ez a paraméter a definíció, a paraméterek a  **\$VMName** és  **\$resourceGroupName** karakterlánc típusú egyszerű paraméterek. Azonban, PowerShell és a PowerShell-munkafolyamati runbookok támogatja az egyszerű típusok és a komplex típusok, például **objektum** vagy **PSCredential** -bemeneti paraméterekhez.

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
> Amikor átadhatja nincs érték egy nem kötelező `[String]` írja be a paraméter, amely rendelkezik egy _alapértelmezett érték_ , `\$null`, akkor a paraméter értéke lesz egy _üres karakterlánc_, **nem** `\$null`.

## <a name="configure-input-parameters-in-graphical-runbooks"></a>Grafikus runbook bemeneti paramétereinek konfigurálása

A [grafikus runbook konfigurálása](automation-first-runbook-graphical.md) bemeneti paraméterek, hozzunk létre, amely virtuális gépek adatait vagy a grafikus runbookok egyetlen virtuális gép vagy a virtuális gépek erőforráscsoporton belül. Egy runbook konfigurálása olyan tevékenységekből állnak, két fő, az alább ismertetett.

[**Runbookok hitelesítése Azure-beli futtató fiók** ](automation-sec-configure-azure-runas-account.md) Azure-hitelesítésre.

[**Get-AzureRmVm** ](/powershell/module/azurerm.compute/get-azurermvm) a virtuális gép tulajdonságainak beolvasása.

Használhatja a [ **Write-Output** ](/powershell/module/microsoft.powershell.utility/write-output) tevékenység kimeneti a virtuális gépek nevét. A tevékenység **Get-AzureRmVm** két paramétert fogad a **virtuális gép neve** és a **erőforráscsoport-név**. Ezeket a paramétereket a runbook minden alkalommal, amikor szükségük különböző értékeket, mert a runbook bemeneti paraméterei is hozzáadhat. Bemeneti paraméterek hozzáadása lépései a következők:

1. Válassza ki a grafikus runbookok a **Runbookok** panelen, majd kattintson [ **szerkesztése** ](automation-graphical-authoring-intro.md) azt.
2. A runbook-szerkesztőben kattintson **bemeneti és kimeneti** megnyitásához a **bemeneti és kimeneti** panelen.

   ![Automation grafikus forgatókönyv](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. A **bemeneti és kimeneti** panel a runbook definiált bemeneti paraméterek listáját jeleníti meg. Ezen a panelen adjon hozzá egy új bemeneti paramétert, vagy szerkesztheti a meglévő bemeneti paraméter konfigurációját. A runbook paraméter hozzáadásához kattintson **bemenet hozzáadása** megnyitásához a **forgatókönyv bemeneti paramétere** panelen. Itt állíthatja be a következő paraméterekkel:

   | **Tulajdonság** | **Leírás** |
   |:--- |:--- |
   | `Name` |Kötelező. A paraméter neve. Ez kell lenniük a runbookon belül és csak betűket, számokat, vagy aláhúzás karaktereket. Egy betűvel kell kezdődnie. |
   | `Description` |Választható. Bemeneti paraméter céljáról szóló leírást. |
   | `Type` |Választható. Az adattípus, amely a paraméter értéke a várt. Támogatott paramétertípusok a következők **karakterlánc**, **Int32**, **Int64**, **decimális**, **logikai**,  **Dátum és idő**, és **objektum**. Ha olyan adattípust nincs bejelölve, a rendszer alapértelmezés szerint **karakterlánc**. |
   | `Mandatory` |Választható. Itt adhatja meg, hogy értéket kell adni a paraméter. Ha úgy dönt, **Igen**, majd egy értéket meg kell adni a forgatókönyv elindulásakor. Ha úgy dönt, **nincs**, majd egy érték esetén nem szükséges a runbook elindult, és a egy alapértelmezett érték is beállítható. |
   | `Default Value` |Választható. Itt adható meg egy értéket, amely a paraméter akkor használatos, ha egy érték nem átadott, a forgatókönyv elindulásakor. Alapértelmezett érték beállítható, amely nem kötelező paraméter. Alapértelmezett érték beállításához válassza **egyéni**. Ezt az értéket használja, ha egy másik érték van megadva, a forgatókönyv elindulásakor. Válasszon **nincs** Ha nem szeretne minden olyan alapértelmezett értéket. |

    ![Új bemenet hozzáadása](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Hozzon létre két paramétert a következő tulajdonságokkal által használt a **Get-AzureRmVm** tevékenység:

   * **Parameter1:**
     * Név – VMName
     * Típus - karakterlánc
     * Kötelező – nem
   * **2:**
     * Name - resourceGroupName
     * Típus - karakterlánc
     * Kötelező – nem
     * Alapértelmezett érték – egyéni
     * Egyéni alapértelmezett érték - \<a virtuális gépeket tartalmazó erőforráscsoport neve\>

5. Miután hozzáadta a paramétereket, kattintson a **OK**. Most már megtekintheti őket a **bemeneti és kimeneti oldal**. Kattintson a **OK** újra, és kattintson a **mentése** és **közzététel** a runbookban.

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

Ha Ön [a runbook elindításához](start-runbooks.md#start-a-runbook-with-the-azure-portal), a **Runbook indítása** panel nyílik meg, és a paraméterek létrehozott értékeket adhat meg.

![A portál használatának megkezdése](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

A címke a beviteli mező alatt láthatja az attribútumokat, amelyek a paraméter lettek beállítva. Attribútumok például a kötelező vagy választható, típus és az alapértelmezett érték. A Súgó buborékra a paraméter neve mellett láthatja a legfontosabb információkat döntéseket hoz a paraméter bemeneti értékeket kell. Ezen információk közé tartozik-e a paraméter kötelező vagy nem kötelező. A típus és alapértelmezett értéket (ha van ilyen) és más hasznos megjegyzéseket is tartalmaz.

> [!NOTE]
> A karakterlánc típusú paramétereket támogatási **üres** karakterlánc-értékek.  Belépő **[EmptyString]** a bemeneti paraméter be az üres karakterlánc továbbítja a paraméter. Ezenkívül nem támogatják a karakterlánc típusú paramétereket **Null** átadott értékeket. Ha bármely érték a karakterlánc-paraméter nem adhatók át, majd PowerShell értelmezi azt null értékű.

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>Indítsa el a közzétett runbookok PowerShell-parancsmagok segítségével, és rendelje hozzá a paraméterek

* **Az Azure Resource Manager-parancsmagok:** Automation-runbook használatával egy erőforráscsoportban létrehozott megkezdése [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook).
  
  **Példa**

  ```powershell
  $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
  ```

* **Klasszikus Azure üzemi modell parancsmagok:** Automation-runbook használatával létrehozott egy alapértelmezett erőforráscsoportba tartozó megkezdése [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook).
  
  **Példa**

  ```powershell
  $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
  Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
  ```

> [!NOTE]
> Ha a PowerShell-parancsmagok, az alapértelmezett paraméterek, elindít egy runbookot **MicrosoftApplicationManagementStartedBy** jön létre a következő értékkel **PowerShell**. Ezt a paramétert a is megtekintheti a **feladat részletei** lap.  

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>Runbook indítása egy SDK-val, és rendelje hozzá a paraméterek

* **Az Azure Resource Manager-metódus:** Az SDK-t egy programozási nyelv használatával is elindít egy runbookot. Alább láthat egy C# kódrészletet a runbook indítása az Automation-fiókban. Megtekintheti, a kód a [GitHub-adattár](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  

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

* **Klasszikus Azure üzemi modell módszer:** Az SDK-t egy programozási nyelv használatával is elindít egy runbookot. Alább láthat egy C# kódrészletet a runbook indítása az Automation-fiókban. Megtekintheti, a kód a [GitHub-adattár](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).

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
  StartRunbook("Get-AzureVMGraphical", RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>Runbook indítása a REST API-val, és rendelje hozzá a paraméterek

Runbook-feladatok létrehozhatók és az Azure Automation REST API használatának lépései a **PUT** metódust a következő kérés URI azonosítója: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`


A kérelem URI-t cserélje le a következő paraméterekkel:

* **előfizetés-azonosító:** Az Azure-előfizetés azonosítóját.  
* **resourceGroupName:** Az Automation-fiókot az erőforráscsoport neve.
* **automationAccountName:** Az automation-fiók a megadott felhőszolgáltatásban lévő üzemeltetett neve.  
* **jobName:** A feladathoz tartozó GUID azonosítója. GUID, a PowerShell használatával lehet létrehozni a **[GUID]::NewGuid(). ToString()** parancsot.

Annak érdekében, hogy a runbook-feladat paraméterek adhatók át, használja a kérelem törzsében. Az alábbi két tulajdonságot a megadott JSON-formátumban szükséges:

* **Runbook neve:** Kötelező. A feladat elindítása a runbook neve.  
* **Runbook paraméterek:** Választható. A paraméter (név, érték) listát tartalmazó ahol neve karakterlánc típusúnak kell lennie, és értéke lehet bármely érvényes JSON-érték formázása.

Ha el szeretné indítani a **Get-AzureVMTextual** a korábban létrehozott runbook **VMName** és **resourceGroupName** meg paraméterként, használja a következő JSON formátummal a a kérelem törzsében.

   ```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WindowsVM",
         "resourceGroupName":"ContosoSales"}
        }
    }
   ```

Egy HTTP-állapotkód: 201-es ad vissza, ha a feladat sikeresen létrehozva. A válaszfejlécek és a válasz törzse további információkért lásd: a cikk kapcsolatos [runbook-feladat létrehozása a REST API használatával.](/rest/api/automation/job/create)

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

## <a name="pass-a-json-object-to-a-runbook"></a>JSON-objektum továbbítása egy runbookhoz

Egy runbook egy JSON-fájlt az átadni kívánt adatokat tárolhat hasznos lehet.
Például előfordulhat, hogy hozzon létre egy JSON-fájlt, amely tartalmazza az összes runbook az átadni kívánt paramétert. Ehhez meg kell konvertálja a JSON-karakterlánc, és majd konvertálja a karakterláncot egy PowerShell-objektumot, mielőtt átadva a runbooknak.

Ebben a példában egy PowerShell-parancsprogram, amely meghívja ezt rendelkezik [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) egy PowerShell-forgatókönyvet, a tartalmát a JSON-fájllal való átadásához a runbook elindításához.
A PowerShell-runbook elindítja egy Azure virtuális Gépen, a paramétereket JSON-t lett átadva a virtuális gép lekérése.

### <a name="create-the-json-file"></a>A JSON-fájl létrehozása

Írja be a következő vizsgálat egy szövegfájlba, és mentse a fájt `test.json` valahol a helyi számítógépen.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>A runbook létrehozása

Hozzon létre egy új PowerShell-runbook nevű, "Test-Json" az Azure Automationben.
Megtudhatja, hogyan hozhat létre egy új PowerShell-forgatókönyvet, tekintse meg a [az első PowerShell-forgatókönyvem](automation-first-runbook-textual-powershell.md).

Fogadja el a JSON-adatokat, a runbook kell tenniük az objektum bemeneti paraméterként.

A runbook indítására használhatja a JSON-megadott tulajdonságok.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Mentse, és tegye közzé a runbook az Automation-fiókban.

### <a name="call-the-runbook-from-powershell"></a>A runbookot hívja meg a PowerShellben

Most már Ön is a runbookot hívja meg a helyi gépen Azure PowerShell-lel.
Futtassa a következő PowerShell-parancsokat:

1. Jelentkezzen be az Azure-bA:

   ```powershell
   Connect-AzureRmAccount
   ```

   Adja meg Azure hitelesítő adatait kéri.

   > [!IMPORTANT]
   > **Add-AzureRmAccount** alias már **Connect-AzureRMAccount**. Ha a Keresés a szalagtár elemmel, ha nem látja, akkor **Connect-AzureRMAccount**, használhat **Add-AzureRmAccount**, vagy frissítheti az a modulokat az Automation-fiókban.

1. A JSON-fájl tartalmát, és alakíthatja át egy karakterlánc:

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

   `JsonPath` az elérési utat, ahová mentette a JSON-fájl van.

1. Karakterlánc tartalmát `$json` PowerShell-objektummá:

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Hozzon létre egy kivonattáblát a paraméterek a `Start-AzureRmAutomationRunbook`:

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Figyelje meg, hogy értékét beállítjuk `Parameters` a PowerShell-objektum, amely tartalmazza a JSON-fájlban szereplő értékek alapján.
1. A runbook indítása

   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>További lépések

* Módjai, indítson el egy runbookot, lásd: [runbook elindítása](automation-starting-a-runbook.md).
* Szöveges runbookok szerkesztése, tekintse meg [szöveges runbookok szerkesztése](automation-edit-textual-runbook.md).
* A grafikus runbookok szerkesztése, tekintse meg [grafikus létrehozás az Azure Automationben](automation-graphical-authoring-intro.md).
