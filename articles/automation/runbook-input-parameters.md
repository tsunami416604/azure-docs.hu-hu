---
title: Runbook bemeneti paraméterei
description: A Runbook bemeneti paraméterei növelik a runbookok rugalmasságát azáltal, hogy lehetővé teszik az adatok runbookba való átvitelét indításkor. Ez a cikk ismerteti a különböző forgatókönyvek, ahol a bemeneti paraméterek runbookokban használt.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 3741adbab6bcbc68f266c331e3056013afc0105e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656033"
---
# <a name="runbook-input-parameters"></a>Runbook bemeneti paraméterei

A Runbook bemeneti paraméterei növelik a runbook rugalmasságát azáltal, hogy lehetővé teszik az adatok átadása, amikor elindul. Ezek a paraméterek lehetővé teszik, hogy a runbook-műveletek meghatározott forgatókönyvekhez és környezetekhez legyenek megcélozva. Ez a cikk ismerteti a konfigurációs és a bemeneti paraméterek használatát a runbookok.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="configuring-input-parameters"></a>Bemeneti paraméterek konfigurálása

A PowerShell, a PowerShell-munkafolyamat, a grafikus és a Python runbookok bemeneti paramétereit konfigurálhatja. Egy runbook több paraméterrel rendelkezhet különböző adattípusokkal, vagy egyáltalán nem rendelkezhet paraméterekkel. A bemeneti paraméterek lehetnek kötelezőek vagy választhatóak, és a választható paraméterekhez alapértelmezett értékeket is használhat.

Értékeket rendel a bemeneti paraméterek egy runbook indításakor. Elindíthat egy runbookot az Azure Portalról, egy webszolgáltatásból vagy a PowerShellből. Egy másik runbookban inline-nak nevezett gyermek runbookként is indíthat egyet.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>Bemeneti paraméterek konfigurálása a PowerShell runbookjaiban

Az Azure Automation PowerShell- és PowerShell-munkafolyamat-runbookjai támogatják a következő tulajdonságokon keresztül definiált bemeneti paramétereket. 

| **Tulajdonság** | **Leírás** |
|:--- |:--- |
| Típus |Kötelező. A paraméterértékhez szükséges adattípus. Minden .NET típus érvényes. |
| Név |Kötelező. A paraméter neve. Ennek a névnek egyedinek kell lennie a runbookon belül, betűvel kell kezdődnie, és csak betűket, számokat vagy aláhúzáskaraktereket tartalmazhat. |
| Kötelező |Választható. Logikai érték, amely megadja, hogy a paraméterhez szükség van-e értékre. Ha ezt igaz értékre állítja, a runbook indításakor meg kell adni egy értéket. Ha ezt hamis értékre állítja, az érték megadása nem kötelező. Ha nem ad meg értéket `Mandatory` a tulajdonsághoz, a PowerShell alapértelmezés szerint választhatónak tekinti a bemeneti paramétert. |
| Alapértelmezett érték |Választható. Olyan érték, amelyet a paraméter, ha a runbook indításakor nem ad át bemeneti értéket. A runbook bármely paraméter alapértelmezett értékét beállíthatja. |

A Windows PowerShell a fent felsoroltaknál több bemeneti paraméter-attribútumot támogat, például az érvényesítést, az aliasokat és a paraméterkészleteket. Az Azure Automation azonban jelenleg csak a felsorolt bemeneti paraméter tulajdonságait támogatja.

Például nézzük meg a paraméterdefiníciót a PowerShell-munkafolyamat runbookjában. Ez a meghatározás a következő általános formát ölti, ahol több paramétervesszővel van elválasztva.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

Most konfigurálja a bemeneti paramétereket egy PowerShell-munkafolyamat-runbook, amely a virtuális gépek részleteit adja ki, akár egyetlen virtuális gép, vagy az erőforráscsoporton belüli összes virtuális gép. Ez a runbook két paraméterrel rendelkezik, ahogy az a`VMName`következő képernyőképen látható:`resourceGroupName`a virtuális gép neve ( ) és az erőforráscsoport neve ( ).

![Automation PowerShell-munkafolyamat](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

Ebben a paraméterdefinícióban a bemeneti paraméterek a karakterlánc típusának egyszerű paraméterei.

Vegye figyelembe, hogy a PowerShell és a PowerShell-munkafolyamat `Object` `PSCredential` runbookok minden egyszerű és összetett típusok, például a bemeneti paraméterek támogatása. Ha a runbook rendelkezik egy objektum bemeneti paraméter, egy PowerShell-kivonatnév-érték párokkal rendelkező powershell-kivonatot kell használnia egy érték átadásához. Például a következő paraméterrel rendelkezik egy runbookban.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

Ebben az esetben a következő értéket adhatja át a paraméternek.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Ha nem ad át értéket egy null alapértelmezett értéket tartalmazó nem kötelező karakterláncparaméternek, a paraméter értéke null helyett üres karakterlánc lesz.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Bemeneti paraméterek konfigurálása grafikus runbookokban

A grafikus runbook bemeneti paramétereinek konfigurálása, hozzon létre egy runbookot, amely a virtuális gépek részleteit adja ki, akár egyetlen virtuális gép, vagy egy erőforráscsoporton belüli összes virtuális gép. További részletek: [Az első grafikus runbook](automation-first-runbook-graphical.md).

A grafikus runbook az alábbi fő runbook-tevékenységeket használja:

* Az Azure Run As fiók konfigurálása az Azure-ral való hitelesítéshez. 
* A [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) parancsmag meghatározása a virtuális gép tulajdonságainak leéséhez.
* A [write-output](/powershell/module/microsoft.powershell.utility/write-output) tevékenység használata a virtuális gép nevek kimenetére. 

A `Get-AzVM` tevékenység két bemenetet határoz meg, a virtuális gép nevét és az erőforráscsoport nevét. Mivel ezek a nevek a runbook indításakor eltérőek lehetnek, a bemeneti paramétereket hozzá kell adnia a runbookhoz, hogy elfogadja ezeket a bemeneteket. Tekintse meg [a grafikus szerzői az Azure Automationben.](automation-graphical-authoring-intro.md)

A bemeneti paraméterek konfigurálásához kövesse az alábbi lépéseket.

1. Jelölje ki a grafikus runbookot a Runbooks lapon, majd kattintson a **Szerkesztés gombra.**
2. A grafikus szerkesztőben kattintson a **Bemenet i és kimeneti** gombra, majd **a Bevitel hozzáadása gombra** a Runbook bemeneti paraméter ablaktábla megnyitásához.

   ![Automatizálási grafikus runbook](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. A bemeneti és kimeneti vezérlő megjeleníti a runbookhoz definiált bemeneti paraméterek listáját. Itt új bemeneti paramétert adhat hozzá, vagy szerkesztheti egy meglévő bemeneti paraméter konfigurációját. Új paraméter hozzáadásához a runbook, kattintson a **Bevitel hozzáadása** a **Runbook bemeneti paraméter** panel megnyitásához, ahol konfigurálhatja a paramétereket az Azure Automation grafikus [szerzői](automation-graphical-authoring-intro.md)által meghatározott tulajdonságok használatával.

    ![Új bemenet hozzáadása](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Hozzon létre két paramétert a tevékenység `Get-AzVM` által használandó következő tulajdonságokkal, majd kattintson az **OK**gombra.

   * 1. paraméter:
        * **Név** -- **VMName**
        * **Típus** -- Karakterlánc
        * **Kötelező** -- **szám**

   * 2. paraméter:
        * **Név** -- **resourceGroupName**
        * **Típus** -- Karakterlánc
        * **Kötelező** -- **szám**
        * **Alapértelmezett érték** -- **Egyéni**
        * Egyéni alapértelmezett érték – A virtuális gépeket tartalmazó erőforráscsoport neve

5. Tekintse meg a paramétereket a Bemenet és kimenet vezérlőben. 
6. Kattintson ismét **az OK,** majd a **Mentés gombra.**
7. Kattintson **a Közzététel** gombra a runbook közzétételéhez.

### <a name="configure-input-parameters-in-python-runbooks"></a>Bemeneti paraméterek konfigurálása python runbookokban

A PowerShell, a PowerShell-munkafolyamat és a grafikus runbookok eltérően a Python runbookok nem vesznek fel elnevezett paramétereket. A runbook-szerkesztő az összes bemeneti paramétert argumentumértékek tömbjeként elemzi. A tömb a modul importálásával a `sys` Python-parancsfájlba, `sys.argv` majd a tömb használatával. Fontos megjegyezni, hogy a tömb `sys.argv[0]`első eleme a parancsfájl neve. Ezért az első tényleges `sys.argv[1]`bemeneti paraméter .

A python-runbookok bemeneti paramétereinek használatát példaként tekintse [meg: Az első Python-runbook az Azure Automationben.](automation-first-runbook-textual-python2.md)

## <a name="assigning-values-to-input-parameters-in-runbooks"></a>Értékek hozzárendelése a runbookok bemeneti paramétereihez

Ez a szakasz számos módon ismerteti az értékek et a runbookok bemeneti paramétereinek. Paraméterértékeket akkor rendelhet hozzá, ha:

* [Runbook indítása](#start-a-runbook-and-assign-parameters)
* [Runbook tesztelése](#test-a-runbook-and-assign-parameters)
* [A runbook ütemezésének összekapcsolása](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Webhook létrehozása a runbookhoz](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Runbook indítása és paraméterek hozzárendelése

A runbook számos módon indítható el: az Azure Portalon keresztül, egy webhook, PowerShell-parancsmagokkal, a REST API-val vagy az SDK-val. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Közzétett runbook indítása az Azure Portal használatával és paraméterek hozzárendelése

Amikor [elindítja a runbookot](start-runbooks.md#start-a-runbook-with-the-azure-portal) az Azure Portalon, megnyílik a **Runbook indítása** panel, és megadhatja a létrehozott paraméterek értékeit.

![A portál használatának megkezdése](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

A beviteli mező alatti címkén láthatja a paraméterattribútumok definiálására beállított tulajdonságokat, például kötelező vagy választható típus, alapértelmezett érték. A paraméternév melletti súgóbuborék a paraméterbemeneti értékekkel kapcsolatos döntések meghozatalához szükséges legfontosabb információkat is meghatározza. 

> [!NOTE]
> A karakterlánc-paraméterek a Karakterlánc típusú üres értékeket támogatják. A `[EmptyString]` bemeneti paraméter mezőbe való beírásegy üres karakterláncot ad át a paraméternek. Emellett a karakterlánc-paraméterek nem támogatják a Null értéket. Ha nem adja át az értéket egy karakterlánc-paraméternek, a PowerShell null értékként értelmezi azt.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>Közzétett runbook indítása PowerShell-parancsmagokkal és paraméterek hozzárendelése

* **Az Azure Resource Manager parancsmagjai:** Elindíthat egy erőforráscsoportban létrehozott Automation runbookot a [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.5.0
)használatával.

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Klasszikus Azure-telepítési modell parancsmagjai:** Elindíthat egy olyan automatizálási runbookot, amelyet egy alapértelmezett erőforráscsoportban hoztak létre a [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook)használatával.
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> Amikor PowerShell-parancsmagokkal indít el egy runbookot, egy alapértelmezett paraméter `MicrosoftApplicationManagementStartedBy`jön létre a használatával. `PowerShell` Ezt a paramétert a Feladat részletei ablaktáblán tekintheti meg.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>Runbook indítása SDK használatával és paraméterek hozzárendelése

* **Azure Resource Manager metódus:** A runbook egy programozási nyelv SDK használatával indítható el. Az alábbiakban egy C# kódrészlet et az Automation-fiókban egy runbook indításához. Az összes kódot megtekintheti a [GitHub-adattárban.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)

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

* **Klasszikus Azure-telepítési modell módszer:** A runbookot egy programozási nyelv SDK-jának használatával indíthatja el. Az alábbiakban egy C# kódrészlet et az Automation-fiókban egy runbook indításához. Az összes kódot megtekintheti a [GitHub-adattárban.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)

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

   A módszer elindításához hozzon létre egy szótárat `VMName` `resourceGroupName` a runbook-paraméterek és azok értékei tárolására. Ezután indítsa el a runbookot. Az alábbiakban a C# kódrészlet a fent meghatározott metódus hívásához.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>Runbook indítása a REST API használatával és paraméterek hozzárendelése

Az Azure Automation REST API-val runbook-feladatot hozhat `PUT` létre és indíthat el a módszer használatával a következő kérelem URI-val:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

A kérelem URI-jában cserélje le a következő paramétereket:

* `subscriptionId`: Az Azure-előfizetés azonosítója.  
* `resourceGroupName`: Az Automation-fiók erőforráscsoportjának neve.
* `automationAccountName`: A megadott felhőszolgáltatáson belül üzemeltetett Automation-fiók neve.  
* `jobName`: A feladat GUID azonosítója. A PowerShell ben lévő GUID-ok a használatával `[GUID]::NewGuid().ToString()*`hozhatók létre.

Paraméterek átadása a runbook-feladat, használja a kérelem törzse. A következő információkat veszi alapul, json formátumban:

* Runbook neve: Kötelező. A feladat elindításához a runbook neve.  
* Runbook-paraméterek: Nem kötelező. A paraméterlista szótára (név, érték) formátumban, ahol a név Karakterlánc és érték típusú, bármilyen érvényes JSON-érték lehet.

Ha azt szeretné, hogy indítsa el a **Get-AzureVMTextual** runbook korábban létrehozott paraméterekkel, `VMName` `resourceGroupName` használja a következő JSON formátumban a kérelem törzs.

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

A 201-es HTTP-állapotkódot adja vissza a rendszer, ha a feladat sikeresen létrejött. A válaszfejlécekről és a választörzsről további információt [a Runbook-feladat létrehozása a REST API használatával című](/rest/api/automation/job/create)témakörben talál.

### <a name="test-a-runbook-and-assign-parameters"></a>Runbook tesztelése és paraméterek hozzárendelése

Ha [a tesztbeállítással teszteli a runbook vázlatverzióját,](automation-testing-runbook.md) megnyílik a Teszt lap. Ezen a lapon állíthatja be a létrehozott paraméterek értékeit.

![Paraméterek tesztelése és hozzárendelése](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Ütemezés csatolása runbookhoz és paraméterek hozzárendelése

Az [ütemezést összekapcsolhatja](automation-schedules.md) a runbook, hogy a runbook egy adott időpontban kezdődik. Az ütemezés létrehozásakor hozzárendeli a bemeneti paramétereket, és a runbook ezeket az értékeket használja az ütemezés indításakor. Az ütemezés addig nem menthető, amíg az összes kötelező paraméterérték meg nincs adva.

![Paraméterek ütemezése és hozzárendelése](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Webhook létrehozása runbookhoz és paraméterek hozzárendelése

Létrehozhat egy [webhookot](automation-webhooks.md) a runbookhoz, és konfigurálhatja a runbook bemeneti paramétereit. A webhook nem menthető, amíg az összes kötelező paraméterérték meg van adva.

![Webhook létrehozása és paraméterek hozzárendelése](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Amikor egy runbookot webhook használatával hajt végre, `[WebhookData](automation-webhooks.md)` a rendszer elküldi az előre definiált bemeneti paramétert a megadott bemeneti paraméterekkel együtt. 

![WebhookData paraméter](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="passing-a-json-object-to-a-runbook"></a>JSON-objektum átadása runbooknak

Hasznos lehet olyan adatokat tárolni, amelyeket egy Runbooknak szeretne átadni egy JSON-fájlban. Létrehozhat például egy JSON-fájlt, amely tartalmazza a runbooknak átadni kívánt összes paramétert. Ehhez a JSON-kódot karakterláncgá kell konvertálnia, majd a karakterláncot PowerShell-objektummá kell konvertálnia, mielőtt átadná azt a runbooknak.

Ez a szakasz egy példát használ, amelyben egy PowerShell-parancsfájl meghívja [a Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) ot egy PowerShell-runbook elindításához, és átadja a JSON-fájl tartalmát a runbooknak. A PowerShell-runbook elindítja az Azure virtuális gép a virtuális gép paramétereinek a JSON-objektumból.

### <a name="create-the-json-file"></a>A JSON-fájl létrehozása

Írja be a következő kódot egy szövegfájlba, és mentse **test.json** néven valahol a helyi számítógépen.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>A runbook létrehozása

Hozzon létre egy új PowerShell-runbook nevű **Test-Json** az Azure Automationben. Lásd: [Az első PowerShell-runbook.](automation-first-runbook-textual-powershell.md)

A JSON-adatok fogadásához a runbooknak egy objektumot kell bemeneti paraméterként figyelembe vennie. A runbook ezután használhatja a JSON-fájlban definiált tulajdonságokat.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Mentse és tegye közzé ezt a runbookot az Automation-fiókban.

### <a name="call-the-runbook-from-powershell"></a>Hívja fel a runbookot a PowerShellből

Most már hívhatja a runbookot a helyi gépről az Azure PowerShell használatával. 

1. Jelentkezzen be az Azure-ba az ábrán látható módon. Ezt követően a rendszer kéri, hogy adja meg az Azure-hitelesítő adatait.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >A PowerShell runbookok, `Add-AzAccount` és `Connect-AzAccount` `Add-AzureRMAccount` aliasok. Vegye figyelembe, hogy ezek az aliasok nem érhetők el a grafikus runbookok. A grafikus runbookcsak `Connect-AzAccount` önmagát használhatja.

1. A mentett JSON-fájl tartalmának bepillantása és karakterláncgá alakítása. `JsonPath`azt az elérési utat jelzi, ahová a JSON-fájlt mentette.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. Konvertálja a `$json` karakterlánc tartalmát egy PowerShell-objektummá.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Hozzon létre egy kivonattáblát `Start-AzAutomationRunbook`a paramétereihez. 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Figyelje meg, hogy a `Parameters` JSON-fájl értékeit tartalmazó PowerShell-objektum értékét állítja be.
1. Indítsa el a runbookot.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>További lépések

* A runbookok indításának különböző módjairól a [Runbook indítása című témakörben talál részleteket.](automation-starting-a-runbook.md)
* Szöveges runbook szerkesztéséhez olvassa el [a Szöveges runbookok szerkesztése](automation-edit-textual-runbook.md)című területet.
* Grafikus runbook szerkesztéséhez olvassa el a [grafikus szerzői munkát az Azure Automationben.](automation-graphical-authoring-intro.md)
