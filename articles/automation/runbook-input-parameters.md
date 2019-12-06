---
title: Forgatókönyv bemeneti paraméterei
description: A Runbook bemeneti paraméterei növelhetik a runbookok rugalmasságát azáltal, hogy lehetővé teszik az adatok átadását egy Runbook az indításakor. Ez a cikk különböző forgatókönyveket ismertet, amelyekben a bemeneti paraméterek a runbookok-ben használatosak.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: be7d244f5aa422b2083d35fc56a52318a4379b79
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850227"
---
# <a name="runbook-input-parameters"></a>Forgatókönyv bemeneti paraméterei

A Runbook bemeneti paraméterei növelhetik a runbookok rugalmasságát azáltal, hogy lehetővé teszik az adatok átadását az elindulásakor. A paraméterek lehetővé teszik, hogy a runbook műveletek meghatározott forgatókönyvek és környezetek számára legyenek megcélozva. Ebben a cikkben olyan különböző forgatókönyveket mutat be, amelyekben a bemeneti paraméterek a runbookok-ben használatosak.

## <a name="configure-input-parameters"></a>Bemeneti paraméterek konfigurálása

A bemeneti paraméterek konfigurálhatók a PowerShell, a PowerShell-munkafolyamat, a Python és a grafikus runbookok használatával. A runbook több, különböző adattípusú paraméterrel rendelkezhetnek, vagy nincsenek paraméterek. A bemeneti paraméterek kötelezőek vagy nem kötelezőek, és a választható paraméterek alapértelmezett értéke is lehet. Az értékeket a runbook tartozó bemeneti paraméterekhez rendelheti hozzá, amikor elindítják azt az elérhető módszerek egyikével. Ezek a módszerek közé tartozik a runbook elindítása a Azure Portalból, egy webszolgáltatásból vagy a PowerShellből. Azt is megteheti, hogy az egyiket alárendelt runbook, amely egy másik runbook inline néven szerepel.

## <a name="configure-input-parameters-in-powershell-runbooks"></a>Bemeneti paraméterek konfigurálása a PowerShell-runbookok

A PowerShell és a PowerShell munkafolyamat-runbookok Azure Automation támogatja a következő attribútumok által definiált bemeneti paramétereket:  

| **Tulajdonság** | **Leírás** |
|:--- |:--- |
| `Type` |Kötelező. A paraméter értékének várt adattípusa. Bármely .NET-típus érvényes. |
| `Name` |Kötelező. A paraméter neve. Ennek egyedinek kell lennie a runbook belül, és csak betűket, számokat és aláhúzás karaktereket tartalmazhat. Betűvel kell kezdődnie. |
| `Mandatory` |Választható. Megadja, hogy meg kell-e adni egy értéket a paraméterhez. Ha a beállítás értéke **\$True (igaz**), akkor meg kell adni egy értéket a runbook indításakor. Ha ezt a beállítást **\$false (hamis**) értékre állítja, akkor egy érték megadása nem kötelező. |
| `Default value` |Választható. A paraméterhez használt értéket adja meg, ha a runbook elindításakor a rendszer nem ad át értéket. Bármelyik paraméterhez beállítható egy alapértelmezett érték, és a kötelező beállítástól függetlenül automatikusan elvégzi a paramétert is. |

A Windows PowerShell támogatja az itt felsorolt bemeneti paraméterek több attribútumát, például az érvényesítést, az aliasokat és a paramétereket. A Azure Automation azonban jelenleg csak az előző bemeneti paramétereket támogatja.

A PowerShell-munkafolyamat runbookok a következő általános formátumban van definiálva, ahol több paramétert vesszővel elválasztva.

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
> Ha a paramétereket definiál, ha nem adja meg a **kötelező** attribútumot, akkor alapértelmezés szerint a paraméter nem kötelező. Ha egy paraméter alapértelmezett értékét a PowerShell-munkafolyamat runbookok állítja be, akkor azt a PowerShell a **kötelező** attribútum értékétől függetlenül nem kötelező paraméterként kezeli.

Tegyük fel például, hogy egy olyan PowerShell-munkafolyamathoz tartozó bemeneti paramétereket konfigurálunk, amely a virtuális gépek részleteit, vagy egyetlen virtuális gépet vagy egy erőforráscsoport összes virtuális gépét runbook. Ennek a runbook két paramétere van, ahogy az a következő képernyőképen látható: a virtuális gép neve és az erőforráscsoport neve.

![Automation PowerShell-munkafolyamat](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

Ebben a paraméter-definícióban a paraméterek **\$VMName** és **\$resourceGroupName** karakterlánc típusú egyszerű paraméterek. A PowerShell és a PowerShell munkafolyamat-runbookok azonban minden egyszerű típust és összetett típust támogat, például a bemeneti paraméterekhez tartozó **objektumot** vagy **PSCredential** .

Ha a runbook rendelkezik bemeneti paraméterrel, akkor egy érték beadásához használjon egy PowerShell-szórótábla (név, érték) párokkal. Ha például a következő paraméter szerepel egy runbook:

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

Ezután átadhatja a következő értéket a paraméternek:

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Ha nem ad meg értéket a nem kötelező `[String]` Type paraméternek, amely `\$null`_alapértelmezett értéke_ , akkor a paraméter értéke _üres karakterlánc_lesz, **nem** `\$null`.

## <a name="configure-input-parameters-in-graphical-runbooks"></a>Bemeneti paraméterek konfigurálása grafikus runbookok

A [grafikus runbook](automation-first-runbook-graphical.md) bemeneti paraméterekkel való konfigurálásához hozzon létre egy grafikus runbook, amely a virtuális gépek részleteit jeleníti meg, vagy egyetlen virtuális gépet vagy egy erőforráscsoport összes virtuális gépét. A runbook konfigurálása két fő tevékenységből áll, az alább leírtak szerint.

[**Runbookok hitelesítése Azure-beli futtató fiókkal**](automation-sec-configure-azure-runas-account.md) az Azure-beli hitelesítéshez.

[**Get-AzureRmVm**](/powershell/module/azurerm.compute/get-azurermvm) a virtuális gép tulajdonságainak lekéréséhez.

A [**Write-output**](/powershell/module/microsoft.powershell.utility/write-output) tevékenységgel a virtuális gépek nevei is kiadhatók. A **Get-AzureRmVm** tevékenység két paramétert fogad el, a **virtuális gép nevét** és az **erőforráscsoport nevét**. Mivel ezek a paraméterek különböző értékeket igényelhetnek a runbook elindításakor, hozzáadhat bemeneti paramétereket a runbook. A bemeneti paraméterek hozzáadásának lépései a következők:

1. Válassza ki a grafikus runbook a **runbookok** panelen, majd kattintson a [**Szerkesztés**](automation-graphical-authoring-intro.md) elemre.
2. A runbook-szerkesztőben kattintson a **bemenet és kimenet** lehetőségre a **bemenet és kimenet** panel megnyitásához.

   ![Automation grafikus runbook](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. A **bemenet és kimenet** panel a runbook meghatározott bemeneti paraméterek listáját jeleníti meg. Ezen a panelen hozzáadhat egy új bemeneti paramétert, vagy szerkesztheti a meglévő bemeneti paraméterek konfigurációját. Ha új paramétert szeretne hozzáadni a runbook, kattintson a **bemenet hozzáadása** gombra a **runbook bemeneti paraméter** panel megnyitásához. Itt a következő paramétereket állíthatja be:

   | **Tulajdonság** | **Leírás** |
   |:--- |:--- |
   | `Name` |Kötelező. A paraméter neve. Ennek egyedinek kell lennie a runbook belül, és csak betűket, számokat és aláhúzás karaktereket tartalmazhat. Betűvel kell kezdődnie. |
   | `Description` |Választható. Leírás a bemeneti paraméter céljáról. |
   | `Type` |Választható. A paraméter értékének várt adattípusa. A támogatott paraméterek a következők: **String**, **Int32**, **Int64**, **decimális**, **Boolean**, **datetime**és **Object**. Ha nincs kiválasztva adattípus, a rendszer az alapértelmezett **karakterláncot**adja meg. |
   | `Mandatory` |Választható. Megadja, hogy meg kell-e adni egy értéket a paraméterhez. Ha az **Igen**lehetőséget választja, akkor meg kell adni egy értéket a runbook indításakor. Ha a **nem**lehetőséget választja, akkor a runbook indításakor nem szükséges érték, és az alapértelmezett érték is beállítható. |
   | `Default Value` |Választható. A paraméterhez használt értéket adja meg, ha a runbook elindításakor a rendszer nem ad át értéket. Egy alapértelmezett érték megadható egy nem kötelező paraméterhez. Alapértelmezett érték beállításához válassza az **Egyéni**lehetőséget. Ez az érték csak akkor használható, ha a runbook indításakor egy másik érték van megadva. Válassza a **nincs** lehetőséget, ha nem szeretne alapértelmezett értéket megadni. |

    ![Új bemenet hozzáadása](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Hozzon létre két paramétert a **Get-AzureRmVm** tevékenység által használt alábbi tulajdonságokkal:

   * **Parameter1**
     * Név – VMName
     * Type-string
     * Kötelező – nem
   * **Parameter2**
     * Név – resourceGroupName
     * Type-string
     * Kötelező – nem
     * Alapértelmezett érték – egyéni
     * Egyéni alapértelmezett érték – \<a virtuális gépeket tartalmazó erőforráscsoport neve\>

5. Miután hozzáadta a paramétereket, kattintson **az OK**gombra. Ekkor megtekintheti őket a **bemeneti és a kimeneti oldalon**. Kattintson ismét **az OK gombra** , majd kattintson a **Save (Mentés** ) gombra, és **tegye közzé** a runbook.

## <a name="configure-input-parameters-in-python-runbooks"></a>Bemeneti paraméterek konfigurálása a Python runbookok

A PowerShell-lel, a PowerShell-munkafolyamatokkal és a grafikus runbookok ellentétben a Python runbookok nem fogad el nevesített paramétereket.
A bemeneti paraméterek az argumentumok tömbje lesznek értelmezve.
A tömböt úgy érheti el, ha a `sys` modult a Python-parancsfájlba importálja, majd a `sys.argv` tömböt használja.
Fontos megjegyezni, hogy a tömb első eleme, `sys.argv[0]`, a a parancsfájl neve, így az első tényleges bemeneti paraméter `sys.argv[1]`.

Egy Python-runbook található bemeneti paraméterek használatával kapcsolatos példát a [Azure Automation első Python-runbook](automation-first-runbook-textual-python2.md)talál.

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Értékek kiosztása a bemeneti paraméterekhez a runbookok-ben

Az értékeket a következő esetekben adhatja át a runbookok bemeneti paramétereinek:

### <a name="start-a-runbook-and-assign-parameters"></a>Runbook elindítása és paraméterek kiosztása

A runbook többféleképpen is elindíthatók: a Azure Portalon, webhooktal, PowerShell-parancsmagokkal, a REST API vagy az SDK-val. Az alábbiakban megbeszéljük a runbook elindításának és a paraméterek hozzárendelésének különböző módszereit.

#### <a name="start-a-published-runbook-by-using-the-azure-portal-and-assign-parameters"></a>Közzétett runbook elindítása a Azure Portal és a paraméterek hozzárendelésével

A [runbook elindításakor](start-runbooks.md#start-a-runbook-with-the-azure-portal)megnyílik a **runbook indítása** panel, amelyen megadhatja a létrehozott paraméterek értékeit.

![A portál használatának megkezdése](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

A beviteli mező alatti címkében láthatja a paraméterhez beállított attribútumokat. Az attribútumok tartalmazzák a kötelező vagy a választható, a típus és az alapértelmezett értéket. A paraméter neve melletti súgóban megtekintheti az összes szükséges információt, amelyekkel döntéseket hozhat a paraméter bemeneti értékeivel kapcsolatban. Ez az információ azt tartalmazza, hogy egy paraméter kötelező vagy nem kötelező. Emellett tartalmazza a típust és az alapértelmezett értéket (ha van ilyen) és más hasznos megjegyzéseket is.

> [!NOTE]
> A karakterlánc típusú paraméterek támogatják az **üres** karakterlánc-értékeket.  A (z) **[EmptyString]** megadása a bemeneti paraméter mezőben üres karakterláncot ad át a paraméternek. Emellett a karakterlánc típusú paraméterek nem támogatják az átadott **Null** értékeket. Ha nem adott meg értéket a karakterlánc-paraméternek, akkor a PowerShell null értékként értelmezi.

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>Közzétett runbook elindítása PowerShell-parancsmagok használatával és paraméterek kiosztása

* **Azure Resource Manager parancsmagok:** A [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook)használatával elindíthat egy olyan Automation-runbook, amely egy erőforráscsoporthoz lett létrehozva.
  
  **Példa**

  ```powershell
  $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
  ```

* **Klasszikus Azure üzembehelyezési modell-parancsmagok:** A [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook)használatával elindíthat egy alapértelmezett erőforráscsoporthoz létrehozott Automation-runbook.
  
  **Példa**

  ```powershell
  $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
  Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
  ```

> [!NOTE]
> Amikor a PowerShell-parancsmagok használatával indít el egy runbook, a **MicrosoftApplicationManagementStartedBy** a **PowerShell**értékkel jön létre. Ez a paraméter a **feladatok részletei** lapon tekinthető meg.  

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>Runbook elindítása SDK használatával és paraméterek kiosztása

* **Azure Resource Manager metódus:** A runbook a programozási nyelv SDK használatával indítható el. Az alábbiakban egy C# kódrészletet talál az Automation-fiókban lévő runbook elindításához. A [GitHub-tárházban](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)található összes kód megtekinthető.  

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

* **Klasszikus Azure üzembe helyezési modell módszere:** A runbook a programozási nyelv SDK használatával indítható el. Az alábbiakban egy C# kódrészletet talál az Automation-fiókban lévő runbook elindításához. A [GitHub-tárházban](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)található összes kód megtekinthető.

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

  A metódus elindításához hozzon létre egy szótárt, amely a runbook paramétereit, a **VMName** és a **resourceGroupName**, valamint azok értékeit tárolja. Ezután indítsa el a runbook. Alább látható C# a fentiekben megadott metódus meghívásához használt kódrészlet.

  ```csharp
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook("Get-AzureVMGraphical", RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>Runbook elindítása a REST API és a paraméterek hozzárendelésével

Runbook-feladatok hozhatók létre és indíthatók el a Azure Automation REST API használatával a **put** metódussal a következő kérés URI-val: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`


A kérelem URI azonosítójában cserélje le a következő paramétereket:

* **subscriptionId:** Az Azure-előfizetés azonosítója.  
* **resourceGroupName:** Az Automation-fiókhoz tartozó erőforráscsoport neve.
* **automationAccountName:** A megadott felhőalapú szolgáltatáson belül futtatott Automation-fiók neve.  
* **jobName:** A feladatokhoz tartozó GUID. A PowerShell GUID azonosítói a **(z) [GUID]:: NewGuid () használatával hozhatók létre. ToString ()** parancs.

Ahhoz, hogy paramétereket lehessen átadni a runbook-feladatoknak, használja a kérelem törzsét. A következő két tulajdonságot JSON formátumban kell megadni:

* **Runbook neve:** Szükséges. Az elindítani kívánt runbook neve.  
* **Runbook paraméterek:** Választható. A paraméter azon (név, érték) formátumában található szótár, ahol a névnek karakterlánc típusúnak kell lennie, és az érték bármely érvényes JSON-érték lehet.

Ha a korábban a **VMName** és a **resourceGroupName** paraméterrel létrehozott **Get-AzureVMTextual** runbook szeretné elindítani, használja a következő JSON-formátumot a kérelem törzséhez.

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

A rendszer a 201 HTTP-állapotkódot adja vissza, ha a feladatot sikeresen létrehozták. A válasz-és a válasz törzsével kapcsolatos további információkért tekintse meg a runbook- [feladatoknak a REST API használatával történő létrehozását](/rest/api/automation/job/create) ismertető cikket.

### <a name="test-a-runbook-and-assign-parameters"></a>Runbook tesztelése és paraméterek kiosztása

Ha a tesztelési lehetőséggel [teszteli a runbook piszkozatát](automation-testing-runbook.md) , megnyílik a **test (teszt** ) lap, és beállíthatja a létrehozott paraméterek értékeit.

![Paraméterek tesztelése és kiosztása](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Ütemterv összekapcsolása runbook és paraméterek kiosztása

Az [ütemtervet összekapcsolhatja](automation-schedules.md) a runbook, hogy a runbook egy adott időpontban induljon el. A bemeneti paramétereket az ütemterv létrehozásakor kell hozzárendelni, és a runbook ezeket az értékeket használja, amikor az ütemterv elindítja. Az ütemezés addig nem menthető, amíg meg nem történik az összes kötelező paraméterérték megadása.

![Paraméterek beosztása és kiosztása](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Webhook létrehozása runbook és paraméterek hozzárendeléséhez

Létrehozhat egy [webhookot](automation-webhooks.md) a runbook, és konfigurálhatja a runbook bemeneti paramétereit. A webhook nem menthető, amíg meg nem történik az összes kötelező paraméterérték megadása.

![Webhook létrehozása és paraméterek kiosztása](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Amikor webhook használatával hajt végre runbook, az előre definiált bemeneti paraméter **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** , valamint a megadott bemeneti paraméterekkel együtt. További részletekért kattintson a **WebhookData** paraméter kibontásához.

![WebhookData paraméter](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>JSON-objektum továbbítása runbook

Hasznos lehet olyan adattárolást tárolni, amelyet egy JSON-fájlban lévő runbook szeretne átadni.
Létrehozhat például egy JSON-fájlt, amely tartalmazza az összes olyan paramétert, amelyet át szeretne adni egy runbook. Ehhez át kell alakítania a JSON-t egy karakterlánccá, majd át kell alakítania a karakterláncot egy PowerShell-objektumba, mielőtt a rendszer átadja a runbook.

Ebben a példában egy PowerShell-parancsfájllal meghívja a [Start-AzureRmAutomationRunbook-](/powershell/module/azurerm.automation/start-azurermautomationrunbook) t, hogy elindítson egy PowerShell-runbook, és átadja a JSON tartalmát a runbook.
A PowerShell-runbook egy Azure-beli virtuális gépet indít el, és beolvassa a virtuális gép paramétereit az átadott JSON-ből.

### <a name="create-the-json-file"></a>A JSON-fájl létrehozása

Írja be a következő tesztet egy szövegfájlba, és mentse `test.json` valahova a helyi számítógépen.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>A runbook létrehozása

Hozzon létre egy "test-JSON" nevű új PowerShell-runbook Azure Automationban.
Az új PowerShell-runbook létrehozásával kapcsolatos információkért lásd [az első PowerShell-runbook](automation-first-runbook-textual-powershell.md).

A JSON-adatok elfogadásához a runbook bemeneti paraméterként kell felvennie egy objektumot.

A runbook ezután a JSON-ban definiált tulajdonságokat használhatják.

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

Mentse és tegye közzé ezt a runbook az Automation-fiókjában.

### <a name="call-the-runbook-from-powershell"></a>A runbook meghívása a PowerShellből

Most meghívhatja a runbook a helyi gépről Azure PowerShell használatával.
A következő PowerShell-parancsok futtatása:

1. Jelentkezzen be az Azure-ba:

   ```powershell
   Connect-AzureRmAccount
   ```

   A rendszer felszólítja az Azure-beli hitelesítő adatok megadására.

   > [!IMPORTANT]
   > A **Add-AzureRmAccount** mostantól egy alias a **kapcsolat-AzureRmAccount**. Ha nem látja a **AzureRMAccount**, használhatja a **AzureRMAccount**, vagy frissítheti a modulokat az Automation-fiókban.

1. A JSON-fájl tartalmának beolvasása és átalakítása karakterlánccá:

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

   `JsonPath` az az elérési út, ahová a JSON-fájlt mentette.

1. `$json` karakterlánc tartalmának konvertálása PowerShell-objektumra:

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Hozzon létre egy szórótábla a `Start-AzureRmAutomationRunbook`paramétereinek számára:

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Figyelje meg, hogy `Parameters` értékének értékét a JSON-fájlból származó értékeket tartalmazó PowerShell-objektumra állítja be.
1. A runbook indítása

   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Következő lépések

* A runbook elindításának különböző módjaival kapcsolatos részletekért lásd: [Runbook elindítása](automation-starting-a-runbook.md).
* A szöveges runbook szerkesztéséhez tekintse meg a [szöveges Runbookok szerkesztését](automation-edit-textual-runbook.md)ismertető témakört.
* Grafikus runbook szerkesztéséhez tekintse meg [Azure Automation grafikus szerzői](automation-graphical-authoring-intro.md)műveleteit.
