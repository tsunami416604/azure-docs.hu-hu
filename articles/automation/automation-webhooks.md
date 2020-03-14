---
title: Azure Automation runbook indítása webhooktal
description: Webhook, amely lehetővé teszi, hogy az ügyfél HTTP-hívással indítson el egy runbook a Azure Automationban.  Ez a cikk bemutatja, hogyan hozhat létre webhookot, és hogyan hívhat meg egyet egy runbook elindításához.
services: automation
ms.subservice: process-automation
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: 043350db2c5372fc81fbb2b68155a4ac75457208
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278401"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Azure Automation runbook indítása webhooktal

A webhookok lehetővé teszik, hogy egy külső szolgáltatás egy adott runbook indítson el Azure Automation egyetlen HTTP-kéréssel. A külső szolgáltatások közé tartoznak az Azure DevOps Services, a GitHub, a Azure Monitor-naplók és az egyéni alkalmazások. A szolgáltatás egy webhook használatával elindíthat egy runbook anélkül, hogy teljes megoldást kellene végrehajtania a Azure Automation API-val. A webhookokat összehasonlíthatja más módszerekkel, hogy runbook indítson el [egy runbook a Azure Automation](automation-starting-a-runbook.md).

> [!NOTE]
> Egy webhook használata a Python-runbook elindításához nem támogatott.

![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="webhook-properties"></a>Webhook tulajdonságai

A következő táblázat ismerteti azokat a tulajdonságokat, amelyeket egy webhookhoz kell konfigurálni.

| Tulajdonság | Leírás |
|:--- |:--- |
| Name (Név) |A webhook neve. Megadhatja a kívánt nevet, mert az nem érhető el az ügyfél számára. A rendszer csak a runbook azonosítására szolgál Azure Automationban. Ajánlott eljárásként a webhooknak az azt használó ügyféllel kapcsolatos nevet kell adnia. |
| URL-cím |A webhook URL-címe. Ez az az egyedi címnek, amelyet az ügyfél HTTP-BEJEGYZÉSsel hív meg, hogy elindítsa a webhookhoz csatolt runbook. A webhook létrehozásakor automatikusan létrejön. Nem adhat meg egyéni URL-címet. <br> <br> Az URL-cím olyan biztonsági jogkivonatot tartalmaz, amely lehetővé teszi, hogy egy harmadik féltől származó rendszer további hitelesítés nélkül hívja meg a runbook. Ezért az URL-címet jelszóként kell kezelni. Biztonsági okokból a webhook létrehozásakor csak a Azure Portal URL-címét tekintheti meg. A jövőbeli használatra biztonságos helyen jegyezze fel az URL-címet. |
| Lejárati dátum | A webhook lejárati dátuma, amely után már nem használható. A webhook létrehozása után módosíthatja a lejárati dátumot, feltéve, hogy a webhook nem járt le. |
| Engedélyezve | Ez a beállítás azt jelzi, hogy a webhook alapértelmezés szerint engedélyezve van-e a létrehozásakor. Ha a tulajdonságot letiltva értékre állítja, akkor egyetlen ügyfél sem használhatja a webhookot. Ezt a tulajdonságot akkor állíthatja be, amikor létrehozza a webhookot vagy bármely más időt a létrehozása után. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>A webhook runbook indításakor használt paraméterek

A webhookok meghatározhatják a runbook indításakor használt runbook paraméterek értékeit. A webhooknak tartalmaznia kell a kötelező runbook paraméterek értékeit, és tartalmazhat értékeket a választható paraméterek számára. A webhookra konfigurált paraméterérték a webhook létrehozása után is módosítható. Egy runbook összekapcsolt webhookok mindegyike különböző runbook paramétereket használhat. Amikor egy ügyfél egy webhook használatával indít el egy runbook, az nem bírálhatja felül a webhookban definiált paraméterek értékét.

Az ügyféltől érkező adatok fogadásához a runbook támogatja a *WebhookData*nevű egyetlen paramétert. Ez a paraméter egy olyan objektumot határoz meg, amely tartalmazza az ügyfél által a POST kérelemben foglalt adatkészletet.

![WebhookData tulajdonságai](media/automation-webhooks/webhook-data-properties.png)

A *WebhookData* paraméter a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás |
|:--- |:--- |
| WebhookName | A webhook neve. |
| RequestHeader | A beérkező POST kérelem fejléceit tartalmazó szórótábla. |
| RequestBody | A beérkező POST kérelem törzse. Ez a törzs megőrzi az adatformázást, például a karakterláncot, a JSON-t, az XML-t vagy az űrlap kódolását. A runbook a várt adatformátummal való együttműködésre kell írni. |

Nincs szükség a webhook konfigurálására a *WebhookData* paraméter támogatásához, és a runbook nem szükséges az elfogadásához. Ha a runbook nem határozza meg a paramétert, a rendszer figyelmen kívül hagyja az ügyféltől érkező kérések részleteit.

> [!NOTE]
> Webhook meghívásakor az ügyfélnek mindig minden paraméter értékét tárolnia kell, ha a hívás sikertelen. Hálózati leállás vagy kapcsolati probléma esetén az alkalmazás nem tudja beolvasni a sikertelen webhook-hívásokat.

Ha a webhook létrehozásakor megad egy értéket a *WebhookData* számára, a rendszer felülbírálja azt, amikor a webhook elindítja a runbook az ügyfél post kérelemből származó adatokkal. Ez akkor is megtörténik, ha az alkalmazás nem tartalmaz semmilyen adatbevitelt a kérelem törzsében. 

Ha olyan runbook indít el, amely a webhookon kívül más mechanizmussal definiál *WebhookData* , megadhat egy értéket a runbook által felismert *WebhookData* számára. Ennek az értéknek olyan objektumnak kell lennie, amely ugyanazokkal a [tulajdonságokkal](#webhook-properties) rendelkezik, mint a *WebhookData* paraméter, így a runbook ugyanúgy működhet, mint a webhook által átadott tényleges *WebhookData* objektumokkal.

Ha például a következő runbook indítja el a Azure Portalból, és szeretne átadni valamilyen minta webhook-adatait tesztelésre, akkor a felhasználói felületen át kell adnia a JSON-ban lévő adatok adatait.

![WebhookData paraméter a felhasználói felületen](media/automation-webhooks/WebhookData-parameter-from-UI.png)

A következő runbook példaként adja meg a következő tulajdonságokat *WebhookData*:

* **WebhookName**: MyWebhook
* **RequestBody**: `*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

Most átadjuk a következő JSON-objektumot a *WebhookData* paraméter felhasználói felületén. Ez a példa a szállítás visszaadásával és a sortörés karakterrel egyezik meg a webhookból átadott formátummal.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![A WebhookData paraméter elindítása a felhasználói felületen](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure Automation naplózza az összes bemeneti paraméter értékét a runbook feladattal. Így az ügyfél által a webhook-kérelemben megadott összes bemenet naplózásra kerül, és elérhető bárki számára, aki hozzáfér az Automation-feladathoz. Emiatt körültekintően kell megjelennie a webhook-hívásokban lévő bizalmas adatokkal kapcsolatban.

## <a name="webhook-security"></a>Webhookok biztonsága

A webhook biztonsága az URL-címének védelmére támaszkodik, amely egy olyan biztonsági jogkivonatot tartalmaz, amely lehetővé teszi a webhook meghívását. A Azure Automation nem végez hitelesítést a kérelemben, amíg a megfelelő URL-címre kerül. Ezért az ügyfelek nem használhatnak olyan webhookokat a runbookok, amelyek nagy mértékben bizalmas műveleteket hajtanak végre anélkül, hogy alternatív módszert kellene alkalmazni a kérés érvényesítésére.

A runbook belül eldöntheti, hogy egy webhook hívja-e meg a logikát. A runbook a *WebhookData* paraméter **WebhookName** tulajdonságát kell megnéznie. A runbook további ellenőrzéseket hajthat végre, ha a **RequestHeader** és a **RequestBody** tulajdonságban meghatározott adatokat keres.

Egy másik stratégia az, hogy a runbook végre kell hajtania egy külső feltétel érvényességét, amikor webhook-kérést kap. Vegyünk például egy GitHub által meghívott runbook, amikor új véglegesít egy GitHub-tárházat. A runbook csatlakozhat a GitHubhoz annak ellenőrzéséhez, hogy új véglegesítés történt a folytatás előtt.

## <a name="creating-a-webhook"></a>Webhook létrehozása

A következő eljárással hozhat létre egy új webhookot, amely egy runbook kapcsolódik a Azure Portal.

1. A Azure Portal Runbookok lapján kattintson arra a runbook, amelyet a webhook elkezd megtekinteni a runbook részleteit. Győződjön meg arról, hogy a runbook **állapota** mező **közzé**van téve.
2. Kattintson a **webhook** elemre az oldal tetején a webhook hozzáadása lap megnyitásához.
3. Kattintson az **új webhook létrehozása** elemre a webhook létrehozása lap megnyitásához.
4. Töltse ki a webhook **neve** és **lejárati dátuma** mezőket, és adja meg, hogy engedélyezni kell-e. A tulajdonságokkal kapcsolatos további információkért tekintse meg a [webhook tulajdonságai](#webhook-properties) című témakört.
5. Kattintson a másolás ikonra, és nyomja le a CTRL + C billentyűkombinációt a webhook URL-címének másolásához. Ezt követően rögzítse egy biztonságos helyen. 

    > [!NOTE]
    > A webhook létrehozása után nem kérheti le újra az URL-címet.

   ![Webhook URL-címe](media/automation-webhooks/copy-webhook-url.png)

1. Kattintson a **Parameters (paraméterek** ) elemre a runbook paramétereinek értékének megadásához. Ha a runbook kötelező paraméterekkel rendelkezik, akkor nem hozhatja létre a webhookot, hacsak nem ad meg értékeket.
1. A webhook létrehozásához kattintson a **Létrehozás** elemre.

## <a name="using-a-webhook"></a>Webhook használata

Ha a létrehozása után webhookot szeretne használni, az ügyfélnek egy HTTP POST-kérelmet kell kiadnia a webhook URL-címével. A szintaxis a következő:

```http
http://<Webhook Server>/token?=<Token Value>
```

Az ügyfél a következő visszatérési kódok egyikét fogadja a POST kérelemből.

| Kód | Szöveg | Leírás |
|:--- |:--- |:--- |
| 202 |Elfogadva |A kérést elfogadták, és a runbook sikeresen várólistára került. |
| 400 |Hibás kérés |A kérelmet a következő okok egyike miatt nem fogadták el: <ul> <li>A webhook lejárt.</li> <li>A webhook le van tiltva.</li> <li>Az URL-címben szereplő jogkivonat érvénytelen.</li>  </ul> |
| 404 |Nem található |A kérelmet a következő okok egyike miatt nem fogadták el: <ul> <li>A webhook nem található.</li> <li>A runbook nem található.</li> <li>A fiók nem található.</li>  </ul> |
| 500 |Belső kiszolgálóhiba |Az URL-cím érvénytelen, de hiba történt. Küldje el újra a kérelmet. |

Ha a kérelem sikeres, a webhook válasza JSON formátumban tartalmazza a feladatot, az alább látható módon. Egyetlen AZONOSÍTÓJÚ feladatot tartalmaz, de a JSON formátum lehetővé teszi a lehetséges jövőbeli fejlesztéseket.

```json
{"JobIds":["<JobId>"]}
```

Az ügyfél nem tudja meghatározni, hogy mikor fejeződött be a runbook-feladatok, vagy a befejezési állapota a webhookban. Ezt az információt a feladattal együtt egy másik mechanizmussal, például a [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) vagy a [Azure Automation API](/rest/api/automation/job)használatával tekintheti meg.

## <a name="renew-webhook"></a>Webhook megújítása

Webhook létrehozásakor az érvényességi időtartam tíz év, amely után az automatikusan lejár. Miután egy webhook lejárt, nem aktiválhatja újra. Csak azt távolíthatja el, majd újból létrehozhatja. 

Kiterjesztheti azt a webhookot, amely nem érte el a lejárati idejét. Webhook kiterjesztése:

1. Navigáljon a webhookot tartalmazó runbook. 
2. Válassza a **webhookok** lehetőséget az **erőforrások**területen. 
3. Kattintson a terjeszteni kívánt webhookra. 
4. A webhook lapon válassza ki az új lejárati dátumot és időpontot, majd kattintson a **Mentés**gombra.

## <a name="sample-runbook"></a>Minta runbook

A következő minta-runbook fogadja a webhook-adatkapcsolatot, és elindítja a kérés törzsében megadott virtuális gépeket. A runbook teszteléséhez az Automation-fiók **runbookok**területén kattintson a **runbook létrehozása**lehetőségre. Ha nem tudja, hogyan hozhat létre runbook, tekintse meg [a Runbook létrehozását](automation-quickstart-create-runbook.md)ismertető témakört.

> [!NOTE]
> A nem grafikus PowerShell-runbookok esetében a **Add-AzAccount** és a **Add-AzureRMAccount** aliasok a következőhöz: [kapcsolat-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Ezeket a parancsmagokat használhatja, vagy [frissítheti a modulokat](automation-update-azure-modules.md) az Automation-fiókban a legújabb verzióra. Előfordulhat, hogy frissítenie kell a modulokat akkor is, ha nemrég létrehozott egy új Automation-fiókot.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Check header for message to validate request
    if ($WebhookData.RequestHeader.message -eq 'StartedbyContoso')
    {
        Write-Output "Header has required information"}
    else
    {
        Write-Output "Header missing required information";
        exit;
    }

    # Retrieve VMs from Webhook request body
    $vms = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.

    Write-Output "Authenticating to Azure with service principal and certificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    Write-Output "Get connection asset: $ConnectionAssetName"

    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Output "Authenticating to Azure with service principal."
            Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="testing-the-sample"></a>A minta tesztelése

Az alábbi példa a Windows PowerShellt használja egy webhooktal rendelkező runbook elindításához. Bármely olyan nyelv, amely HTTP-kéréseket tesz elérhetővé, webhookot használhat. Példaként a Windows PowerShellt használjuk.

A runbook a kérelem törzsében a JSON-ban formázott virtuális gépek listáját várja. A runbook ellenőrzi, hogy a fejlécek tartalmaznak-e egy meghatározott üzenetet annak ellenőrzéséhez, hogy a webhook-hívó érvényes-e.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-WebRequest -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

A következő példa a runbook **RequestBody** tulajdonságában elérhető kérelem törzsét mutatja be a *WebhookData*. Ez az érték a JSON-ban van formázva, hogy kompatibilis legyen a kérelem törzsében található formátummal.

```json
[
    {
        "Name":  "vm01",
        "ResourceGroup":  "myResourceGroup"
    },
    {
        "Name":  "vm02",
        "ResourceGroup":  "myResourceGroup"
    }
]
```

Az alábbi képen látható a Windows PowerShellből küldött kérelem és az eredményül kapott válasz. A rendszer kinyeri a feladatot a válaszból, és karakterlánccá alakítja át.

![Webhookok gomb](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Következő lépések

* Ha szeretné megtudni, hogyan használhatja a Azure Automationt az Azure-riasztásokkal kapcsolatos műveletek elvégzéséhez, tekintse meg a [riasztások használata Azure Automation runbook](automation-create-alert-triggered-runbook.md).
